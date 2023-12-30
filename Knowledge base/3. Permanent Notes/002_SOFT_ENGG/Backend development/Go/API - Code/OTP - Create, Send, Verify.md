
```go
package otp


const (
	otpLength       = 8
	EmailSender     = "security@chhaano.com"
	EmailSenderName = "Chhaano"
	subject         = "One Time Pin (OTP) for Your Recent Verification Request"
	IsDecayed       = "DECAYED"
	optNonceLength  = 64 // 64 chars, or 32 bit. Nonce should be 32 bit
)

// OTP represents a new OTP request
type OTP struct {
	Email  string
	Code   int
	Expiry time.Duration
}

// NewOTP returns a new OTP code. It takes email, code and time duration as an input.
func NewOTP(email string, code int, expiry time.Duration) *OTP {
	return &OTP{
		Email:  email,
		Code:   code,
		Expiry: expiry,
	}
}

// templateData represents a template data for new OTP request.
type templateData struct {
	OTPCode int
}

// SendOTP sends OTP code to user email.
func SendOTP(
	recepient string,
	redisClient *redis.Client,
	OTPExpirationDuration time.Duration,
) (string, string, error) {
	code, _ := utils.GenerateOTP(otpLength)
	codeInt, _ := strconv.Atoi(code)

	t, err := template.ParseFiles("assets/otp-html-template.tmpl")
	if err != nil {
		return "", "", fmt.Errorf("could not parse template file: %v", err)
	}

	data := &templateData{
		OTPCode: codeInt,
	}

	buf := new(bytes.Buffer)
	if err = t.ExecuteTemplate(buf, "OTPHTMLEmailBody", data); err != nil {
		return "", "", fmt.Errorf("could not apply data to parse object: %v", err)
	}

	r := email.Email{
		SenderName:    EmailSenderName,
		SenderEmail:   EmailSender,
		ReceiverName:  recepient,
		ReceiverEmail: recepient,
		Subject:       subject,
		Body:          buf.String(),
		BodyPlainText: buf.String(),
	}

	_, err = r.SendEmail()
	if err != nil {
		return "", "", fmt.Errorf("could not send otp email: %v", err)
	}

	if err := db.SetRedisValue(redisClient, recepient, code, OTPExpirationDuration); err != nil {
		return "", "", fmt.Errorf("could not save otp to redis: %v", err)
	}

	nonce, err := utils.GenerateNonce(optNonceLength)
	if err != nil {
		return "", "", fmt.Errorf("could not generate random token: %v", err)
	}

	// TODO: use this to verify signature of two otp tokens
	h := utils.GenerateSignature(os.Getenv("SECRET_TOKEN"), code)

	// TODO: generate nonce for the transaction
	if err := db.SetRedisValue(redisClient, nonce, recepient, OTPExpirationDuration); err != nil {
		return "", "", fmt.Errorf("could not save nonce to redis: %v", err)
	}

	return nonce, h, nil
}

func VerifyOTP(
	redisClient *redis.Client,
	code string,
	nonce string,
	OTPExpirationTime time.Duration,
	store *repository.Store,
) (bool, error) {
	ctx := context.Background()

	userEmail, err := redisClient.Get(ctx, nonce).Result()
	if userEmail == IsDecayed {
		return false, fmt.Errorf(errors.ErrReplayedOTP.Message())
	}

	if err != nil {
		if err.Error() == "redis: nil" {
			return false, fmt.Errorf(errors.ErrExpiredOTP.Message())
		}
	}

	originalOTP, err := redisClient.Get(ctx, userEmail).Result()
	if err != nil {
		if err.Error() == "redis: nil" {
			return false, fmt.Errorf(errors.ErrExpiredOTP.Message())
		}
	}

	if !validators.IsSame(originalOTP, code) {
		return false, fmt.Errorf(errors.ErrBadOTP.Message())
	}

	// Invalidate OTP once its used up
	if err := db.InvalidateRedisValue(redisClient, nonce, IsDecayed, OTPExpirationTime); err != nil {
		return false, fmt.Errorf("could not invalidate OTP nonce")
	}

	if err := db.InvalidateRedisValue(redisClient, userEmail, IsDecayed, OTPExpirationTime); err != nil {
		return false, fmt.Errorf("could not invalidate email")
	}

	user, err := store.GetUserByEmail(userEmail)
	if err != nil {
		return false, fmt.Errorf(errors.ErrUserDoesNotExists.Message())
	}

	// set is email verified parameter to true
	if err := store.UpdateUserAccountByKey(user.ID.Hex(), "isEmailVerified", true); err != nil {
		return false, fmt.Errorf(errors.ErrCouldNotUpdateUser.Message())
	}

	return true, nil
}
```


OTP templates

```
{{define "OTPHTMLEmailBody"}}
...
<strong>{{.OTPCode}}</strong>
....
  </html>
{{end}}
```

password reset template

```
{{define "ForgetPasswordEmailBody"}}
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" ...#434245"> 

</span><span style="font-size: 16px; color: #1155cc">{{.ResetEmailLink}}
</span>
...
{{end}}
```