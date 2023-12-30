
```
package server

import (
	"net/http"
	"time"

	"github.com/dhiki-labs/chhaaano__backend/internal/errors"
	"github.com/dhiki-labs/chhaaano__backend/internal/model"
	"github.com/dhiki-labs/chhaaano__backend/internal/otp"
	"github.com/dhiki-labs/chhaaano__backend/internal/utils"
	"github.com/dhiki-labs/chhaaano__backend/validators"
	"github.com/gin-contrib/requestid"
	"github.com/gin-gonic/gin"
)

func (server *Server) handleSendOTP(ctx *gin.Context) {
	request := &model.OTPRequest{}

	response := &model.HTTPResponse{}

	// binding request body to the create user request model
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

	if !validators.IsEmail(utils.ToLowerCase(request.Email)) {

		errorsListslice := make([]model.Error, 0, 1)

		server.logger.Error().
			Str("request-id", requestid.Get(ctx)).
			Msg("invalid email format")

		errorsListslice = append(errorsListslice, model.Error{
			Code:    int(errors.ErrInvalidEmailFormat),
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

	// If email is correct then send OTP
	nonce, h, err := otp.SendOTP(
		utils.ToLowerCase(request.Email), // This is recepient email
		server.redisClient,
		server.conf.OTP.ExpirationTime) // This is
	if err != nil {

		server.logger.Error().
			Str("request-id", requestid.Get(ctx)).
			Str("error", err.Error()).
			Msg("could not sent otp")

		ctx.JSON(http.StatusOK, gin.H{
			"status":  http.StatusInternalServerError,
			"data":    "null",
			"message": "could not send otp",
		})
		return
	}

	server.logger.Info().
		Str("email", utils.ToLowerCase(request.Email)).
		Str("request-id", requestid.Get(ctx)).
		Msg("otp generated created")

	result := model.Result{}

	result["email"] = utils.ToLowerCase(request.Email)
	result["message"] = "otp sent"
	result["nonce"] = nonce
	result["created_at"] = time.Now()
	result["request_id"] = requestid.Get(ctx)
	result["h"] = h

	response.Result = &result
	response.StatusCode = http.StatusOK

	ctx.Writer.Header().Set("Content-Type", "application/json")
	ctx.JSON(http.StatusOK, response)
}

func (server *Server) handleVerifyOTP(ctx *gin.Context) {
	code := ctx.Query("otp")
	nonce := ctx.Query("nonce")

	_, err := otp.VerifyOTP(server.redisClient, code, nonce, server.conf.OTP.ExpirationTime, &server.store)
	if err != nil {
		if err.Error() == errors.ErrReplayedOTP.Message() {

			errorsListslice := make([]model.Error, 0, 1)

			server.logger.Error().
				Str("request-id", requestid.Get(ctx)).
				Msg(errors.ErrReplayedOTP.Message())

			errorsListslice = append(errorsListslice, model.Error{
				Code:    int(errors.ErrReplayedOTP),
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

		if err.Error() == errors.ErrExpiredOTP.Message() {

			errorsListslice := make([]model.Error, 0, 1)

			server.logger.Error().
				Str("request-id", requestid.Get(ctx)).
				Msg(errors.ErrExpiredOTP.Message())

			errorsListslice = append(errorsListslice, model.Error{
				Code:    int(errors.ErrExpiredOTP),
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

		errorsListslice := make([]model.Error, 0, 1)

		server.logger.Error().
			Str("request-id", requestid.Get(ctx)).
			Msg(err.Error())

		errorsListslice = append(errorsListslice, model.Error{
			Code:    int(http.StatusInternalServerError),
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

	result := model.Result{}

	result["message"] = "valid otp"

	response := model.HTTPResponse{
		Errors:     nil,
		Result:     &result,
		StatusCode: http.StatusOK,
	}

	ctx.JSON(http.StatusOK, response)
}
```