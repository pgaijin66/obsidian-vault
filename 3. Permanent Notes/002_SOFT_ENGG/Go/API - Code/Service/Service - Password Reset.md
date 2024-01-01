
```
package service

import (
	"bytes"
	"fmt"
	"strconv"
	"strings"
	"text/template"
	"time"

	"github.com/dhiki-labs/chhaaano__backend/internal/email"
	"github.com/dhiki-labs/chhaaano__backend/internal/errors"
	"github.com/dhiki-labs/chhaaano__backend/internal/password"
	"github.com/dhiki-labs/chhaaano__backend/internal/repository"
	"github.com/dhiki-labs/chhaaano__backend/internal/utils"
)

const (
	emailSender     = "noreply@chhaano.com"
	emailSenderName = "Chhaano"
	subject         = "Please reset your password"
)

// templateData represents the data for html template
type templateData struct {
	ResetEmailLink string
}

// SendPasswordResetEmail send password reset link via email to the user's email address
func SendPasswordResetEmail(
	userEmail string,
	resetPageURL string,
	resetPageEndpoint string,
	resetLinkDuration time.Duration,
	hmacSecret string,
	store *repository.Store,
) error {
	// Check if user exists
	user, err := store.GetUserByEmail(userEmail)
	if err != nil {
		return fmt.Errorf("%v, %v", errors.ErrUserDoesNotExists.Message(), err)
	}

	userIDString := user.ID.Hex()

	// currentTimestamp := strconv.FormatInt(time.Now().Unix(), 10) // converting timestamp in unix format which is int64 to string
	resetLinkExpiry := time.Now().Add(resetLinkDuration).Unix()
	resetLinkExpiryString := strconv.FormatInt(resetLinkExpiry, 10)    // converting timestamp in unix format which is int64 to string
	lastLoginTimestamp := strconv.FormatInt(user.LastLogin.Unix(), 10) // converting timestamp in unix format which is int64 to string

	// this function takes last login time, userID, and user hashed password along with secret and generates a hash.
	// If any of the value changes, the generated hash is nullified.
	// If user logins after generating password reseting link, this hash will be changed and then nullified
	hash := utils.GenerateHMAC(
		lastLoginTimestamp+userIDString+user.HashedPassword,
		hmacSecret)

	token := fmt.Sprintf(resetLinkExpiryString + "$" + hash)

	resetEmailLink := fmt.Sprintf(resetPageURL + resetPageEndpoint + "?token=" + token)

	// Send email to user
	parsedTemplate, err := template.ParseFiles("assets/password-reset-template.tmpl")
	if err != nil {
		return fmt.Errorf("could not parse template file: %v", err)
	}

	data := &templateData{
		ResetEmailLink: resetEmailLink,
	}

	buf := new(bytes.Buffer)
	if err = parsedTemplate.ExecuteTemplate(buf, "ForgetPasswordEmailBody", data); err != nil {
		return fmt.Errorf("could not apply data to parse object: %v", err)
	}

	r := email.Email{
		SenderName:    emailSenderName,
		SenderEmail:   emailSender,
		ReceiverName:  userEmail,
		ReceiverEmail: userEmail,
		Subject:       subject,
		Body:          buf.String(),
		BodyPlainText: buf.String(),
	}

	_, err = r.SendEmail()
	if err != nil {
		return fmt.Errorf("could not send password reset email: %v", err)
	}

	return nil
}

// ChangeUnAuthenticatedUserPassword changes user password for unauthenticated user
func ChangeUnAuthenticatedUserPassword(token, newPassword, userEmail string, hmacSecret string, store repository.Store) error {
	tokenGenerationTimestamp, _ := strconv.Atoi(strings.ToLower(strings.Split(token, "$")[0]))

	tokenHash := strings.ToLower(strings.Split(token, "$")[1])

	delta := tokenGenerationTimestamp - int(time.Now().Unix())
	if delta <= 0 {
		return fmt.Errorf("%v", errors.ErrPasswordResetLinkExipired.Message())
	}

	user, err := store.GetUserByEmail(userEmail)
	if err != nil {
		return fmt.Errorf("%v, %v", errors.ErrUserDoesNotExists.Message(), err)
	}
	userIDString := user.ID.Hex()

	lastLoginTimestamp := strconv.FormatInt(user.LastLogin.Unix(), 10) // converting timestamp in unix format which is int64 to string

	userCurrentHash := utils.GenerateHMAC(lastLoginTimestamp+userIDString+user.HashedPassword, hmacSecret)

	if !utils.IsValidHMAC([]byte(tokenHash), []byte(userCurrentHash)) {
		return fmt.Errorf("%v", errors.ErrInvalidHMAC.Message())
	}

	newHashedPassword, err := password.GetHash(newPassword)
	if err != nil {
		return fmt.Errorf("could not generate password hash: %v", err)
	}

	err = store.UpdateUserAccountByKey(userIDString, "hashedPassword", newHashedPassword)
	if err != nil {
		return fmt.Errorf("could not reset password: %v", err)
	}

	return nil
}

// ChangePassword changes user password for authenticated user
func ChangePassword(email, old, new string, store repository.Store) error {
	user, err := store.GetUserByEmail(email)
	if err != nil {
		return fmt.Errorf("%v, %v", errors.ErrUserDoesNotExists.Message(), err)
	}

	userHashPassword := fmt.Sprintf("%v", user.HashedPassword)
	ok, _ := password.Matches(old, userHashPassword)
	if !ok {
		return fmt.Errorf("%v, %v", errors.ErrEmailPasswordIncorrect.Message(), err)
	}

	newHashedPassword, err := password.GetHash(new)
	if err != nil {
		return fmt.Errorf("could not generate password hash: %v", err)
	}

	err = store.UpdateUserAccountByKey(user.ID.Hex(), "hashedPassword", newHashedPassword)
	if err != nil {
		return fmt.Errorf("could not reset password: %v", err)
	}

	return nil
}
```