
```
package server

import (
	"encoding/json"
	"fmt"
	"net/http"
	"strings"

	"github.com/dhiki-labs/chhaaano__backend/internal/errors"
	"github.com/dhiki-labs/chhaaano__backend/internal/middlewares"
	"github.com/dhiki-labs/chhaaano__backend/internal/model"
	"github.com/dhiki-labs/chhaaano__backend/internal/password"
	"github.com/dhiki-labs/chhaaano__backend/internal/service"
	"github.com/dhiki-labs/chhaaano__backend/internal/token"
	"github.com/dhiki-labs/chhaaano__backend/internal/utils"
	"github.com/dhiki-labs/chhaaano__backend/validators"
	"github.com/gin-contrib/requestid"
	"github.com/gin-gonic/gin"
)

// createUser creates a new user account
func (server *Server) handleCreateUser(ctx *gin.Context) {
	request := model.NewCreateUserRequest()

	if err := ctx.BindJSON(request); err != nil {

		server.logger.Error().
			Str("Error", err.Error()).
			Str("request-id", requestid.Get(ctx)).
			Msg("could not bind to request body")

		errorsListslice := make([]model.Error, 0, 1)
		errorsListslice = append(errorsListslice, model.Error{
			Code:    int(errors.ErrCouldNotCreateUser),
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

	if !validators.IsSame(request.Password, request.RetypedPassword) {

		errorsListslice := make([]model.Error, 0, 1)

		server.logger.Error().
			Str("request-id", requestid.Get(ctx)).
			Msg("password and retyped password are not same")

		errorsListslice = append(errorsListslice, model.Error{
			Code:    int(errors.ErrPasswordRetypedPasswordMismatch),
			Message: errors.ErrPasswordRetypedPasswordMismatch.Message(),
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
			Message: errors.ErrInvalidEmailFormat.Message(),
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

	if !validators.IsPasswordOfValidLength(request.Password) {

		errorsListslice := make([]model.Error, 0, 1)

		server.logger.Error().
			Str("request-id", requestid.Get(ctx)).
			Msg("invalid password length.")

		errorsListslice = append(errorsListslice, model.Error{
			Code:    int(errors.ErrInvalidPasswordLength),
			Message: errors.ErrInvalidPasswordLength.Message(),
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

	if validators.PasswordContainsName(request.Password, utils.ToLowerCase(request.Email)) {

		errorsListslice := make([]model.Error, 0, 1)

		server.logger.Error().
			Str("request-id", requestid.Get(ctx)).
			Msg("password contains names from email")

		errorsListslice = append(errorsListslice, model.Error{
			Code:    int(errors.ErrPasswordContainsUsername),
			Message: errors.ErrPasswordContainsUsername.Message(),
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

	server.logger.Info().Str("email", utils.ToLowerCase(request.Email)).Str("request-id", requestid.Get(ctx)).Msg("creating new user")

	user, accessToken, expiresAt, err := service.CreateUser(
		request,
		server.tokenMaker,
		server.conf.Token.AccessTokenDuration,
		&server.store)
	if err != nil {
		errorsListslice := make([]model.Error, 0, 1)

		if err.Error() == errors.ErrUserAlreadyExists.Message() {
			errorsListslice = append(errorsListslice, model.Error{
				Code:    int(errors.ErrUserAlreadyExists),
				Message: errors.ErrOperationFailed.Message(),
			})
			result := model.Result{}
			// sending response back to the user
			response := model.HTTPResponse{
				Errors:     errorsListslice,
				Result:     &result,
				StatusCode: http.StatusConflict,
			}
			ctx.JSON(http.StatusConflict, response)
			return
		}

		server.logger.Error().
			Str("request-id", requestid.Get(ctx)).
			Msg(err.Error())
		errorsListslice = append(errorsListslice, model.Error{
			Code:    int(errors.ErrCouldNotCreateUser),
			Message: errors.ErrOperationFailed.Message(),
		})
		result := model.Result{}
		// sending response back to the user
		response := model.HTTPResponse{
			Errors:     errorsListslice,
			Result:     &result,
			StatusCode: http.StatusInternalServerError,
		}
		ctx.JSON(http.StatusInternalServerError, response)
		return
	}

	server.logger.
		Info().
		Str("email", utils.ToLowerCase(request.Email)).
		Str("request-id", requestid.Get(ctx)).
		Msg("user created")

	result := model.Result{}

	result["user"] = user
	result["access_token"] = accessToken
	result["expires_at"] = expiresAt
	result["message"] = "user created"
	ctx.Writer.Header().Set("Content-Type", "application/json")

	response := model.HTTPResponse{
		Errors:     nil,
		Result:     &result,
		StatusCode: http.StatusOK,
	}

	ctx.JSON(http.StatusOK, response)
}

func (server *Server) handleGetUserByID(ctx *gin.Context) {
	userID := ctx.Param("user-id")
	user, err := service.GetUserByID(userID, &server.store)
	if err != nil {
		server.logger.
			Error().
			Str("Error", errors.ErrUserDoesNotExists.Message()).
			Str("request-id", requestid.Get(ctx)).
			Msg(err.Error())

		errorsListslice := make([]model.Error, 0, 1)

		// Stacking all the relevant errors to create slice of errors
		errorsListslice = append(errorsListslice, model.Error{
			Code:    int(errors.ErrUserDoesNotExists),
			Message: errors.ErrOperationFailed.Message(),
		})

		result := model.Result{}

		response := model.HTTPResponse{
			Errors:     errorsListslice,
			Result:     &result,
			StatusCode: http.StatusNotFound,
		}

		ctx.JSON(http.StatusNotFound, response)
		return
	}

	result := model.Result{}

	result["user"] = user
	result["message"] = "user fetched"

	response := model.HTTPResponse{
		Errors:     nil,
		Result:     &result,
		StatusCode: http.StatusOK,
	}

	ctx.JSON(http.StatusOK, response)
}

func (server *Server) handleGetAllUsers(ctx *gin.Context) {
	server.logger.
		Info().
		Str("request-id", requestid.Get(ctx)).
		Msg("Fetching all users")

	users, err := service.GetAllUsers(&server.store)
	if err != nil {
		server.logger.
			Error().
			Str("Error", errors.ErrCouldNotFetchListing.Message()).
			Str("request-id", requestid.Get(ctx)).
			Msg(err.Error())

		errorsListslice := make([]model.Error, 0, 1)
		// Stacking all the relevant errors to create slice of errors
		errorsListslice = append(errorsListslice, model.Error{
			Code:    int(errors.ErrCouldNotFetchUser),
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

	result := model.Result{}

	// making sure that null value is not returned and empty array is send back
	if users == nil {
		result["users"] = []string{}
	} else {
		result["users"] = users
	}

	result["users"] = users
	result["message"] = "users fetched"
	ctx.Writer.Header().Set("Content-Type", "application/json")

	response := model.HTTPResponse{
		Errors:     nil,
		Result:     &result,
		StatusCode: http.StatusOK,
	}

	ctx.JSON(http.StatusOK, response)
}

func (server *Server) handleUpdateUser(ctx *gin.Context) {
	userID := ctx.Param("user-id")

	// Make sure user is logged in
	authPayload := ctx.MustGet(middlewares.AuthorizationPayloadKey).(*token.Payload)

	userUpdateRequest := model.NewUpdateUserRequest()

	if err := ctx.ShouldBind(&userUpdateRequest); err != nil {

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

	updatedUser, err := service.UpdateUser(userID, authPayload, userUpdateRequest, &server.store)
	if err != nil {

		server.logger.Error().
			Str("Error", err.Error()).
			Str("request-id", requestid.Get(ctx)).
			Msg(errors.ErrCouldNotUpdateListing.Message())

		errorsListslice := make([]model.Error, 0, 1)
		errorsListslice = append(errorsListslice, model.Error{
			Code:    int(errors.ErrCouldNotUpdateUser),
			Message: errors.ErrCouldNotUpdateUser.Message(),
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

	listings, err := server.store.GetUserListings(updatedUser.Email)
	if err != nil {
		server.logger.Error().
			Str("Error", err.Error()).
			Str("request-id", requestid.Get(ctx)).
			Msg(errors.ErrCouldNotFetchListing.Message())
	}

	for _, listing := range listings {
		server.logger.Info().
			Str("request-id", requestid.Get(ctx)).
			Msg("revalidating user listings " + listing.ID.Hex())

		// Create an HTTP client
		client := &http.Client{}

		// Set the URL of the API endpoint
		url := fmt.Sprintf(server.conf.Webhook.Revalidation.Endpoint + "?s=" + server.conf.Webhook.Revalidation.Secret + "&listingId=" + listing.ID.Hex())
		// Create a new GET request
		req, _ := http.NewRequest("GET", url, nil)
		// Send the request and get the response
		resp, err := client.Do(req)
		if err != nil {
			var j interface{}
			_ = json.NewDecoder(resp.Body).Decode(&j)
			server.logger.Error().
				Str("Error", err.Error()).
				Str("request-id", requestid.Get(ctx)).
				Str("response", j.(string)).
				Msg("could not revalidate listing")
		}

	}

	result := model.Result{}

	result["user"] = updatedUser
	result["message"] = "user updated"
	ctx.Writer.Header().Set("Content-Type", "application/json")

	response := model.HTTPResponse{
		Errors:     nil,
		Result:     &result,
		StatusCode: http.StatusOK,
	}

	ctx.JSON(http.StatusOK, response)
}

func (server *Server) handleDeleteUser(ctx *gin.Context) {
	authPayload := ctx.MustGet(middlewares.AuthorizationPayloadKey).(*token.Payload)

	userID := ctx.Param("user-id")

	err := service.DeleteUser(userID, authPayload, &server.store)
	if err != nil {
		switch {
		case strings.Contains(err.Error(), errors.ErrNotAuthorized.Message()):

			server.logger.Error().
				Str("Error", err.Error()).
				Str("request-id", requestid.Get(ctx)).
				Msg(err.Error())

			errorsListslice := make([]model.Error, 0, 1)
			errorsListslice = append(errorsListslice, model.Error{
				Code:    int(errors.ErrNotAuthorized),
				Message: errors.ErrNotAuthorized.Message(),
			})

			result := model.Result{}

			response := model.HTTPResponse{
				Errors:     errorsListslice,
				Result:     &result,
				StatusCode: http.StatusUnauthorized,
			}

			ctx.JSON(http.StatusUnauthorized, response)
			return
		default:
			server.logger.Error().
				Str("Error", err.Error()).
				Str("request-id", requestid.Get(ctx)).
				Msg(err.Error())

			errorsListslice := make([]model.Error, 0, 1)
			errorsListslice = append(errorsListslice, model.Error{
				Code:    int(errors.ErrCouldNotDeleteUser),
				Message: errors.ErrCouldNotDeleteUser.Message(),
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

	server.logger.
		Info().
		Str("listing-id", userID).
		Str("request-id", requestid.Get(ctx)).
		Msg("user deleted")

	result := model.Result{}

	result["message"] = "user deleted"
	ctx.Writer.Header().Set("Content-Type", "application/json")

	response := model.HTTPResponse{
		Errors:     nil,
		Result:     &result,
		StatusCode: http.StatusOK,
	}

	ctx.JSON(http.StatusOK, response)
}
```