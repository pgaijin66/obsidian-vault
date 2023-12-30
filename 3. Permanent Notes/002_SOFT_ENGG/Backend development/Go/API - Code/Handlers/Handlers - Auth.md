
```
package server

import (
	"net/http"
	"strings"

	"github.com/dhiki-labs/chhaaano__backend/internal/errors"
	"github.com/dhiki-labs/chhaaano__backend/internal/model"
	"github.com/dhiki-labs/chhaaano__backend/internal/service"
	"github.com/dhiki-labs/chhaaano__backend/internal/utils"
	"github.com/gin-contrib/requestid"
	"github.com/gin-gonic/gin"
	"github.com/ua-parser/uap-go/uaparser"
)

func (server *Server) handleLogin(ctx *gin.Context) {
	request := model.NewLoginUserRequest()

	if err := ctx.ShouldBindJSON(&request); err != nil {
		server.logger.Error().
			Str("Error", err.Error()).
			Str("request-id", requestid.Get(ctx)).
			Msg("could not bind to request body")

		errorsList := make([]model.Error, 0, 1)
		errorsList = append(errorsList, model.Error{
			Code:    int(errors.ErrCouldNotLogin),
			Message: errors.ErrOperationFailed.Message(),
		})

		result := model.Result{}

		response := model.HTTPResponse{
			Errors:     errorsList,
			Result:     &result,
			StatusCode: http.StatusBadRequest,
		}

		ctx.JSON(http.StatusBadRequest, response)
		return
	}

	server.logger.
		Info().
		Str("email", utils.ToLowerCase(request.Email)).
		Str("request-id", requestid.Get(ctx)).
		Msg("attempting to log in")

	user, accessToken, expiresAt, err := service.Login(
		request,
		server.tokenMaker,
		server.conf.Token.AccessTokenDuration,
		&server.store)
	if err != nil {

		errorsList := make([]model.Error, 0, 1)

		// TODO: Implement switch case to handle these if statements
		if strings.Contains(err.Error(), errors.ErrUserDoesNotExists.Message()) {
			server.logger.Error().Str("email", utils.ToLowerCase(request.Email)).Str("request-id", requestid.Get(ctx)).Msg(errors.ErrUserDoesNotExists.Message())
			errorsList = append(errorsList, model.Error{
				Code:    int(errors.ErrCouldNotLogin),
				Message: errors.ErrOperationFailed.Message(),
			})

			result := model.Result{}

			response := model.HTTPResponse{
				Errors:     errorsList,
				Result:     &result,
				StatusCode: http.StatusNotFound,
			}

			ctx.JSON(http.StatusNotFound, response)
			return
		}

		if strings.Contains(err.Error(), errors.ErrEmailPasswordIncorrect.Message()) {

			server.logger.
				Error().
				Str("email", utils.ToLowerCase(request.Email)).
				Str("request-id", requestid.Get(ctx)).
				Msg(errors.ErrEmailPasswordIncorrect.Message())

			errorsList = append(errorsList, model.Error{
				Code:    int(errors.ErrEmailPasswordIncorrect),
				Message: errors.ErrOperationFailed.Message(),
			})

			result := model.Result{}

			response := model.HTTPResponse{
				Errors:     errorsList,
				Result:     &result,
				StatusCode: http.StatusUnauthorized,
			}

			ctx.JSON(http.StatusUnauthorized, response)
			return
		}

		if strings.Contains(err.Error(), errors.ErrAccountDisabled.Message()) {

			server.logger.
				Error().Str("email", request.Email).
				Str("request-id", requestid.Get(ctx)).
				Msg(errors.ErrAccountDisabled.Message())

			errorsList = append(errorsList, model.Error{
				Code:    int(errors.ErrAccountDisabled),
				Message: errors.ErrAccountDisabled.Message(),
			})

			result := model.Result{}

			response := model.HTTPResponse{
				Errors:     errorsList,
				Result:     &result,
				StatusCode: http.StatusForbidden,
			}

			ctx.JSON(http.StatusForbidden, response)
			return
		}

		if strings.Contains(err.Error(), errors.ErrCouldNotCreateAccessToken.Message()) {

			server.logger.
				Error().
				Str("email", utils.ToLowerCase(request.Email)).
				Str("request-id", requestid.Get(ctx)).
				Msg(errors.ErrCouldNotCreateAccessToken.Message())

			errorsList = append(errorsList, model.Error{
				Code:    int(errors.ErrCouldNotCreateAccessToken),
				Message: errors.ErrOperationFailed.Message(),
			})

			result := model.Result{}

			response := model.HTTPResponse{
				Errors:     errorsList,
				Result:     &result,
				StatusCode: http.StatusInternalServerError,
			}

			ctx.JSON(http.StatusInternalServerError, response)
			return
		}

		server.logger.
			Error().
			Str("email", utils.ToLowerCase(request.Email)).
			Str("request-id", requestid.Get(ctx)).
			Msg(err.Error())

		errorsList = append(errorsList, model.Error{
			Code:    int(errors.ErrCouldNotLogin),
			Message: errors.ErrOperationFailed.Message(),
		})

		result := model.Result{}

		response := model.HTTPResponse{
			Errors:     errorsList,
			Result:     &result,
			StatusCode: http.StatusInternalServerError,
		}

		ctx.JSON(http.StatusInternalServerError, response)
		return
	}

	server.logger.
		Info().
		Str("email", utils.ToLowerCase(request.Email)).
		Str("request-id", requestid.
			Get(ctx)).
		Msg("logged in successfully")

	userResponse := model.UserResonse{
		ID:              user.ID,
		Email:           user.Email,
		FirstName:       user.FirstName,
		LastName:        user.LastName,
		Address:         user.Address,
		UpdatedAt:       user.UpdatedAt,
		CreatedAt:       user.CreatedAt,
		PhoneNumber:     user.PhoneNumber,
		AvatarURL:       user.AvatarURL,
		IsEmailVerified: user.IsEmailVerified,
		LastLogin:       user.LastLogin,
	}

	result := model.Result{}

	result["user"] = userResponse
	result["access_token"] = accessToken

	// set server side cookie
	tokenCookie := &http.Cookie{
		Name:     "__at",
		Value:    accessToken,
		Expires:  expiresAt,
		Path:     "/",
		Domain:   ".chhaano.com",
		HttpOnly: true,
		SameSite: http.SameSiteLaxMode,
		Secure:   true, // use true if serving over HTTPS
	}

	// client side cookie
	// logged_in
	// has_recent_activity
	// user => userID or userdetails
	//

	// Parse the User-Agent header using uaparser
	parser := uaparser.NewFromSaved()
	userAgent := ctx.Request.Header.Get("User-Agent")
	client := parser.Parse(userAgent)

	// Generate a device ID using a hash function
	deviceID := utils.GenerateHMAC(userAgent+client.Device.Family+client.Device.Model, server.conf.Crypto.HMAC.Secret)

	deviceIDCookie := &http.Cookie{
		Name:     "__device_id",
		Value:    deviceID,
		Expires:  expiresAt,
		Path:     "/",
		Domain:   ".chhaano.com",
		HttpOnly: true,
		SameSite: http.SameSiteLaxMode,
		Secure:   true, // use true if serving over HTTPS
	}

	result["expires_at"] = expiresAt
	result["expires_at"] = expiresAt

	http.SetCookie(ctx.Writer, tokenCookie)
	http.SetCookie(ctx.Writer, deviceIDCookie)

	result["message"] = "login successful"

	ctx.Writer.Header().Set("Content-Type", "application/json")

	response := model.HTTPResponse{
		Errors:     nil,
		Result:     &result,
		StatusCode: http.StatusOK,
	}

	ctx.JSON(http.StatusOK, response)
}

// Logout loggs user out and clears all the sessions and cookies
func (server *Server) handleLogout(ctx *gin.Context) {
	// TODO: clear all the user related from datastores ( redis )
	result := model.Result{}

	result["message"] = "logout successful"
	ctx.Writer.Header().Set("Content-Type", "application/json")

	response := model.HTTPResponse{
		Errors:     nil,
		Result:     &result,
		StatusCode: http.StatusOK,
	}

	ctx.JSON(http.StatusOK, response)
}

// Logout loggs user out and clears all the sessions and cookies
func (server *Server) handleTokenVerification(ctx *gin.Context) {
	// Since token validation is already checked by auth middleware, if it is valid, we do not need to do anything here
	//  and can just return valid token.
	result := model.Result{}

	result["message"] = "valid"
	ctx.Writer.Header().Set("Content-Type", "application/json")

	response := model.HTTPResponse{
		Errors:     nil,
		Result:     &result,
		StatusCode: http.StatusOK,
	}

	ctx.JSON(http.StatusOK, response)
}

func (server *Server) handleLockUser(ctx *gin.Context) {
	userID := ctx.Param("user-id")

	server.logger.
		Debug().
		Str("user-id", userID).
		Str("request-id", requestid.Get(ctx)).
		Msg("attempting to lock user account")

	err := service.LockUserAccount(userID, &server.store)
	if err != nil {
		ctx.JSON(http.StatusInternalServerError, gin.H{
			"status":  http.StatusInternalServerError,
			"data":    "null",
			"message": "could not lock account",
		})
	}

	server.logger.
		Info().
		Str("user-id", userID).
		Str("request-id", requestid.Get(ctx)).
		Msg("user account locked")

	result := model.Result{}

	result["message"] = "account locked"
	ctx.Writer.Header().Set("Content-Type", "application/json")

	response := model.HTTPResponse{
		Errors:     nil,
		Result:     &result,
		StatusCode: http.StatusOK,
	}

	ctx.JSON(http.StatusOK, response)
}
```