
```
package server

import (
	"net/http"
	"strings"

	"github.com/dhiki-labs/chhaaano__backend/internal/errors"
	"github.com/dhiki-labs/chhaaano__backend/internal/middlewares"
	"github.com/dhiki-labs/chhaaano__backend/internal/model"
	"github.com/dhiki-labs/chhaaano__backend/internal/service"
	"github.com/dhiki-labs/chhaaano__backend/internal/token"
	"github.com/dhiki-labs/chhaaano__backend/internal/utils"
	"github.com/gin-contrib/requestid"
	"github.com/gin-gonic/gin"
)

func (server *Server) handleUnAuthenticatedUserResetPassword(ctx *gin.Context) {
	request := model.NewResetPasswordRequest()

	if err := ctx.ShouldBindJSON(request); err != nil {

		server.logger.Error().
			Str("Error", err.Error()).
			Str("request-id", requestid.Get(ctx)).
			Msg("could not bind to request body")

		errorsListslice := make([]model.Error, 0, 1)

		errorsListslice = append(errorsListslice, model.Error{
			Code:    int(errors.ErrBadRequest),
			Message: errors.ErrOperationFailed.Message(),
		})

		result := model.Result{}

		response := model.HTTPResponse{
			Errors:     errorsListslice,
			Result:     &result,
			StatusCode: http.StatusBadRequest,
		}

		ctx.JSON(http.StatusBadRequest, response)
		return
	}

	err := service.ChangeUnAuthenticatedUserPassword(request.Token, request.NewPassword, request.Email, server.conf.Crypto.HMAC.Secret, server.store)
	if err != nil {
		switch {
		case strings.Contains(err.Error(), errors.ErrInvalidHMAC.Message()):

			server.logger.Error().
				Str("Error", err.Error()).
				Str("request-id", requestid.Get(ctx)).
				Msg("reset token invalid")

			errorsListslice := make([]model.Error, 0, 1)

			errorsListslice = append(errorsListslice, model.Error{
				Code:    int(errors.ErrInvalidHMAC),
				Message: errors.ErrOperationFailed.Message(),
			})

			result := model.Result{}

			response := model.HTTPResponse{
				Errors:     errorsListslice,
				Result:     &result,
				StatusCode: http.StatusBadRequest,
			}

			ctx.JSON(http.StatusBadRequest, response)
			return

		case strings.Contains(err.Error(), errors.ErrPasswordResetLinkExipired.Message()):

			server.logger.Error().
				Str("Error", err.Error()).
				Str("request-id", requestid.Get(ctx)).
				Msg("reset link expired")
			errorsListslice := make([]model.Error, 0, 1)
			errorsListslice = append(errorsListslice, model.Error{
				Code:    int(errors.ErrPasswordResetLinkExipired),
				Message: errors.ErrPasswordResetLinkExipired.Message(),
			})
			result := model.Result{}
			response := model.HTTPResponse{
				Errors:     errorsListslice,
				Result:     &result,
				StatusCode: http.StatusBadRequest,
			}
			ctx.JSON(http.StatusBadRequest, response)
			return

		default:
			server.logger.Error().
				Str("Error", err.Error()).
				Str("request-id", requestid.Get(ctx)).
				Msg(err.Error())

			errorsListslice := make([]model.Error, 0, 1)
			errorsListslice = append(errorsListslice, model.Error{
				Code:    int(errors.ErrOperationFailed),
				Message: errors.ErrOperationFailed.Message(),
			})
			result := model.Result{}
			response := model.HTTPResponse{
				Errors:     errorsListslice,
				Result:     &result,
				StatusCode: http.StatusInternalServerError,
			}
			ctx.JSON(http.StatusInternalServerError, response)
			return
		}
	}

	result := model.Result{}
	result["message"] = "password changed"
	ctx.Writer.Header().Set("Content-Type", "application/json")
	response := model.HTTPResponse{
		Errors:     nil,
		Result:     &result,
		StatusCode: http.StatusOK,
	}
	ctx.JSON(http.StatusOK, response)
}

func (server *Server) handleAuthenticatedUserResetPassword(ctx *gin.Context) {
	// Make use is authenticated
	authPayload := ctx.MustGet(middlewares.AuthorizationPayloadKey).(*token.Payload)

	request := model.NewChangePasswordRequest()
	if err := ctx.ShouldBindJSON(request); err != nil {
		server.logger.Error().
			Str("Error", err.Error()).
			Str("request-id", requestid.Get(ctx)).
			Msg("could not bind to request body")
		errorsListslice := make([]model.Error, 0, 1)
		errorsListslice = append(errorsListslice, model.Error{
			Code:    int(errors.ErrBadRequest),
			Message: errors.ErrBadRequest.Message(),
		})
		result := model.Result{}
		response := model.HTTPResponse{
			Errors:     errorsListslice,
			Result:     &result,
			StatusCode: http.StatusBadRequest,
		}
		ctx.JSON(http.StatusBadRequest, response)
		return
	}

	err := service.ChangePassword(authPayload.Email, request.Old, request.New, server.store)
	if err != nil {
		if strings.Contains(err.Error(), errors.ErrEmailPasswordIncorrect.Message()) {
			server.logger.Error().
				Str("Error", err.Error()).
				Str("request-id", requestid.Get(ctx)).
				Msg("incorrect password")
			errorsListslice := make([]model.Error, 0, 1)
			errorsListslice = append(errorsListslice, model.Error{
				Code:    int(errors.ErrEmailPasswordIncorrect),
				Message: errors.ErrOperationFailed.Message(),
			})
			result := model.Result{}
			response := model.HTTPResponse{
				Errors:     errorsListslice,
				Result:     &result,
				StatusCode: http.StatusBadRequest,
			}
			ctx.JSON(http.StatusBadRequest, response)
			return
		}

		ctx.JSON(http.StatusInternalServerError, gin.H{
			"status":  http.StatusInternalServerError,
			"data":    "null",
			"message": errors.ErrOperationFailed.Message(),
		})
		return
	}

	result := model.Result{}
	result["message"] = "password changed"
	ctx.Writer.Header().Set("Content-Type", "application/json")
	response := model.HTTPResponse{
		Errors:     nil,
		Result:     &result,
		StatusCode: http.StatusOK,
	}
	ctx.JSON(http.StatusOK, response)
}

// handleForgotPassword send password reset email link to user's email
func (server *Server) handleForgotPassword(ctx *gin.Context) {
	request := model.NewForgetPasswordRequest()
	if err := ctx.ShouldBindJSON(request); err != nil {
		server.logger.Error().
			Str("Error", err.Error()).
			Str("request-id", requestid.Get(ctx)).
			Msg("could not bind to request body")
		errorsListslice := make([]model.Error, 0, 1)
		errorsListslice = append(errorsListslice, model.Error{
			Code:    int(errors.ErrBadRequest),
			Message: errors.ErrOperationFailed.Message(),
		})
		result := model.Result{}
		response := model.HTTPResponse{
			Errors:     errorsListslice,
			Result:     &result,
			StatusCode: http.StatusBadRequest,
		}
		ctx.JSON(http.StatusBadRequest, response)
		return
	}

	err := service.SendPasswordResetEmail(
		utils.ToLowerCase(request.Email),
		server.conf.PasswordResetPageURL,
		server.conf.PasswordResetPageEndpoint,
		server.conf.PasswordResetEmailLinkDuration,
		server.conf.Crypto.HMAC.Secret,
		&server.store)
	// We will log any errors that occur, but we'll respond to the user with a "202 Accepted" message regardless of the error.
	// This is done in order to prevent a username enumeration attack.
	if err != nil {
		server.logger.Error().
			Str("Error", err.Error()).
			Str("request-id", requestid.Get(ctx)).
			Msg(err.Error())
	}

	ctx.JSON(http.StatusAccepted, gin.H{
		"status":  http.StatusAccepted,
		"data":    "",
		"message": "password reset link sent to your email",
	})
}
```