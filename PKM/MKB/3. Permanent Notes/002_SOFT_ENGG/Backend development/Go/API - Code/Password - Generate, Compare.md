

```
func GetHash(plaintextPassword string) (string, error) {
	hashedPassword, err := bcrypt.GenerateFromPassword([]byte(plaintextPassword), bcrypt.DefaultCost)
	if err != nil {
		return "", err
	}

	return string(hashedPassword), nil
}
```

```
func Matches(plaintextPassword, hashedPassword string) (bool, error) {
	err := bcrypt.CompareHashAndPassword([]byte(hashedPassword), []byte(plaintextPassword))
	if err != nil {
		switch {
		case errors.Is(err, bcrypt.ErrMismatchedHashAndPassword):
			return false, nil
		default:
			return false, err
		}
	}

	return true, nil
}
```


Check common passwords

```
package password

// CommonPasswords list is from
// https://github.com/danielmiessler/SecLists/blob/master/Passwords/Common-Credentials/10k-most-common.txt
var CommonPasswords = []string{
	"password",
	"123456",
	"12345678",
	"1234",
}

if validators.NotIn(password.CommonPasswords, request.Password) {

		errorsListslice := make([]model.Error, 0, 1)

		server.logger.Error().
			Str("request-id", requestid.Get(ctx)).
			Msg("password too common")

		errorsListslice = append(errorsListslice, model.Error{
			Code:    int(errors.ErrPasswordTooCommon),
			Message: errors.ErrPasswordTooCommon.Message(),
		})

		result := model.Result{}

		// sending response back to the user
		response := model.HTTPResponse{
			Errors:     errorsListslice,
			Result:     &result,
			StatusCode: http.StatusBadRequest,
		}

		ctx.JSON(http.StatusBadRequest, response)
		return
	}

```