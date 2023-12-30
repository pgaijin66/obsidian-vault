
```
package server

import (
	"fmt"
	"io"
	"net/http"
	"strconv"
	"strings"

	"github.com/dhiki-labs/chhaaano__backend/internal/errors"
	"github.com/dhiki-labs/chhaaano__backend/internal/middlewares"
	"github.com/dhiki-labs/chhaaano__backend/internal/model"
	"github.com/dhiki-labs/chhaaano__backend/internal/service"
	"github.com/dhiki-labs/chhaaano__backend/internal/token"
	"github.com/dhiki-labs/chhaaano__backend/internal/utils"
	"github.com/dhiki-labs/chhaaano__backend/validators"
	"github.com/gin-contrib/requestid"
	"github.com/gin-gonic/gin"
)

func (server *Server) handleCreateListing(ctx *gin.Context) {
	request := model.NewCreateListingRequest()

	if err := ctx.BindJSON(request); err != nil {
		server.logger.Error().
			Str("Error", err.Error()).
			Str("request-id", requestid.Get(ctx)).
			Msg("could not bind to request body")

		errorsListslice := []model.Error{
			{
				Code:    int(errors.ErrBadRequest),
				Message: errors.ErrBadRequest.Message(),
			},
		}
		result := model.Result{}
		response := model.HTTPResponse{
			Errors:     errorsListslice,
			Result:     &result,
			StatusCode: http.StatusBadRequest,
		}

		ctx.JSON(http.StatusBadRequest, response)
		return
	}

	authPayload := ctx.MustGet(middlewares.AuthorizationPayloadKey).(*token.Payload)

	if err := validators.ValidateCreateListingRequest(request); err != nil {
		server.logger.Error().
			Str("Error", err.Error()).
			Str("request-id", requestid.Get(ctx)).
			Msg("could not create new listing")

		errorsListslice := []model.Error{
			{
				Code:    int(errors.ErrBadRequest),
				Message: err.Error(),
			},
		}
		result := model.Result{}
		response := model.HTTPResponse{
			Errors:     errorsListslice,
			Result:     &result,
			StatusCode: http.StatusBadRequest,
		}
		ctx.JSON(http.StatusBadRequest, response)
		return
	}

	server.logger.Info().Str("email", authPayload.Email).Str("request-id", requestid.Get(ctx)).Msg("creating new listing")

	listing, err := service.CreateListing(authPayload, request, &server.store)
	if err != nil {
		switch {
		case strings.Contains(err.Error(), errors.ErrUserDoesNotExists.Message()):
			server.logger.Error().
				Str("Error", errors.ErrUserDoesNotExists.Message()).
				Str("request-id", requestid.Get(ctx)).
				Msg("could not create new listing")

			errorsListslice := []model.Error{
				{
					Code:    int(errors.ErrUserDoesNotExists),
					Message: errors.ErrOperationFailed.Message(),
				},
			}
			result := model.Result{}
			response := model.HTTPResponse{
				Errors:     errorsListslice,
				Result:     &result,
				StatusCode: http.StatusNotFound,
			}
			ctx.JSON(http.StatusNotFound, response)
			return
		default:
			server.logger.Error().
				Str("Error", err.Error()).
				Str("request-id", requestid.Get(ctx)).
				Msg("could not create new listing")

			errorsListslice := []model.Error{
				{
					Code:    int(errors.ErrCouldNotCreateListing),
					Message: errors.ErrOperationFailed.Message(),
				},
			}
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
	result["listing"] = listing
	result["message"] = "listing created"

	ctx.Writer.Header().Set("Content-Type", "application/json")

	response := model.HTTPResponse{
		Errors:     nil,
		Result:     &result,
		StatusCode: http.StatusOK,
	}

	ctx.JSON(http.StatusOK, response)
}

func (server *Server) handleGetAllListings(ctx *gin.Context) {
	server.logger.Info().Str("request-id", requestid.Get(ctx)).Msg("Fetching all listings")

	filters := make(map[string]string)

	// Searching by zip code
	filters["zip"] = ctx.Query("zip")
	filters["bedRooms"] = ctx.Query("bed_rooms")
	filters["bathRooms"] = ctx.Query("bath_rooms")
	filters["priceMin"] = ctx.Query("price_min")
	filters["priceMax"] = ctx.Query("price_max")
	filters["listingType"] = ctx.Query("listing_type")
	filters["propertyType"] = ctx.Query("property_type")

	// sorting
	sort := ctx.Query("sort")

	page, _ := strconv.Atoi(ctx.Query("page"))

	// If no page value is given, page value is set to default. This is to make sure we do not add much load to the API
	if page <= 0 {
		page = server.conf.Pagination.DefaultPage
	}

	// If page size is not set, by default, default page_size value will be applied
	pageSize, _ := strconv.Atoi(ctx.Query("page_size"))
	if pageSize <= 0 {
		pageSize = server.conf.Pagination.DefaultPageSize
	}

	listings, total, err := service.GetAllListings(
		filters,
		sort,
		page,
		pageSize,
		server.conf.Pagination.DefaultPage,
		server.conf.Pagination.DefaultPageSize,
		&server.store)
	if err != nil {
		server.logger.Error().Str("Error", errors.ErrCouldNotFetchListing.Message()).Str("request-id", requestid.Get(ctx)).Msg(err.Error())
		errorsListslice := make([]model.Error, 0, 1)
		errorsListslice = append(errorsListslice, model.Error{
			Code:    int(errors.ErrCouldNotFetchListing),
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
	if listings == nil {
		result["listings"] = []string{}
	} else {
		result["listings"] = listings
	}

	result["message"] = "listing fetched"
	result["page"] = page

	pages := total / int64(pageSize)

	if total%int64(pageSize) != 0 {
		pages++
		result["last_page"] = pages
	} else {
		result["last_page"] = 1
	}

	result["total"] = total

	response := model.HTTPResponse{
		Errors:     nil,
		Result:     &result,
		StatusCode: http.StatusOK,
	}
	ctx.JSON(http.StatusOK, response)
}

func (server *Server) handleGetListingByID(ctx *gin.Context) {
	listingID := ctx.Param("listing-id")

	listing, err := service.GetListingByID(listingID, &server.store)
	if err != nil {
		server.logger.Error().Str("Error", errors.ErrListingNotFound.Message()).Str("request-id", requestid.Get(ctx)).Msg(err.Error())
		errorsListslice := make([]model.Error, 0, 1)
		errorsListslice = append(errorsListslice, model.Error{
			Code:    int(errors.ErrListingNotFound),
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

	result["listing"] = listing
	result["message"] = "listing fetched"

	response := model.HTTPResponse{
		Errors:     nil,
		Result:     &result,
		StatusCode: http.StatusOK,
	}

	ctx.JSON(http.StatusOK, response)
}

func (server *Server) handleDeleteListing(ctx *gin.Context) {
	listingID := ctx.Param("listing-id")

	authPayload := ctx.MustGet(middlewares.AuthorizationPayloadKey).(*token.Payload)

	err := service.DeleteListing(authPayload, listingID, &server.store)
	if err != nil {
		switch {
		case strings.Contains(err.Error(), errors.ErrUserDoesNotOwn.Message()):
			server.logger.Error().
				Str("Error", err.Error()).
				Str("request-id", requestid.Get(ctx)).
				Msg(err.Error())
			errorsListslice := make([]model.Error, 0, 1)
			errorsListslice = append(errorsListslice, model.Error{
				Code:    int(errors.ErrUserDoesNotOwn),
				Message: errors.ErrOperationFailed.Message(),
			})
			result := model.Result{}
			response := model.HTTPResponse{
				Errors:     errorsListslice,
				Result:     &result,
				StatusCode: http.StatusForbidden,
			}
			ctx.JSON(http.StatusForbidden, response)
			return
		default:
			server.logger.Error().
				Str("Error", err.Error()).
				Str("request-id", requestid.Get(ctx)).
				Msg(err.Error())
			errorsListslice := make([]model.Error, 0, 1)
			errorsListslice = append(errorsListslice, model.Error{
				Code:    int(errors.ErrCouldNotDeleteListing),
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

	server.logger.Info().Str("listing-id", listingID).Str("request-id", requestid.Get(ctx)).Msg("listing deleted")

	result := model.Result{}

	result["message"] = "listing deleted"
	ctx.Writer.Header().Set("Content-Type", "application/json")

	response := model.HTTPResponse{
		Errors:     nil,
		Result:     &result,
		StatusCode: http.StatusOK,
	}

	ctx.JSON(http.StatusOK, response)
}

func (server *Server) handleUpdateListing(ctx *gin.Context) {
	// Make sure user is logged in
	authPayload := ctx.MustGet(middlewares.AuthorizationPayloadKey).(*token.Payload)

	listingID := ctx.Param("listing-id")

	listingUpdateRequest := model.NewUpdateListingRequest()

	if err := ctx.ShouldBind(&listingUpdateRequest); err != nil {

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

	server.logger.Info().Str("email", authPayload.Email).Str("request-id", requestid.Get(ctx)).Msg("updating listing")

	updatedListing, err := service.UpdateListing(authPayload,
		listingID,
		listingUpdateRequest, &server.store)
	if err != nil {
		switch {

		case strings.Contains(err.Error(), errors.ErrUserDoesNotOwn.Message()):
			server.logger.Error().
				Str("Error", err.Error()).
				Str("request-id", requestid.Get(ctx)).
				Msg(errors.ErrUserDoesNotOwn.Message())

			errorsListslice := make([]model.Error, 0, 1)

			errorsListslice = append(errorsListslice, model.Error{
				Code:    int(errors.ErrUserDoesNotOwn),
				Message: errors.ErrOperationFailed.Message(),
			})

			result := model.Result{}

			response := model.HTTPResponse{
				Errors:     errorsListslice,
				Result:     &result,
				StatusCode: http.StatusForbidden,
			}

			ctx.JSON(http.StatusForbidden, response)
			return

		default:
			server.logger.Error().
				Str("Error", err.Error()).
				Str("request-id", requestid.Get(ctx)).
				Msg(errors.ErrCouldNotUpdateListing.Message())

			errorsListslice := make([]model.Error, 0, 1)

			errorsListslice = append(errorsListslice, model.Error{
				Code:    int(errors.ErrCouldNotUpdateListing),
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

	// Create an HTTP client
	client := &http.Client{}

	// Set the URL of the API endpoint
	url := fmt.Sprintf(server.conf.Webhook.Revalidation.Endpoint + "?s=" + server.conf.Webhook.Revalidation.Secret + "&listingId=" + listingID)

	server.logger.Info().
		Str("request-id", requestid.Get(ctx)).
		Msg("revalidating listing: " + listingID)

	fmt.Println(url)

	// Create a new GET request
	req, _ := http.NewRequest("GET", url, nil)
	// Send the request and get the response
	resp, err := client.Do(req)
	if err != nil {
		server.logger.Error().
			Str("Error", err.Error()).
			Str("request-id", requestid.Get(ctx)).
			Msg("could not revalidate listing")
	}

	defer func() {
		if err := resp.Body.Close(); err != nil {
			server.logger.Error().
				Str("request-id", requestid.Get(ctx)).
				Msg("could not close response body: " + err.Error())
		}
	}()

	bodyBytes, _ := io.ReadAll(resp.Body)

	if resp.StatusCode != http.StatusOK {
		server.logger.Info().
			Str("request-id", requestid.Get(ctx)).
			Str("response", string(bodyBytes)).
			Msg("could not revalidate listing")

		errorsListslice := make([]model.Error, 0, 1)

		errorsListslice = append(errorsListslice, model.Error{
			Code:    int(errors.ErrCouldNotUpdateListing),
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

	server.logger.Info().
		Str("request-id", requestid.Get(ctx)).
		Str("response", string(bodyBytes)).
		Msg("revalidated")

	result := model.Result{}

	result["listing"] = updatedListing
	result["message"] = "listing updated"
	ctx.Writer.Header().Set("Content-Type", "application/json")

	response := model.HTTPResponse{
		Errors:     nil,
		Result:     &result,
		StatusCode: http.StatusOK,
	}

	ctx.JSON(http.StatusOK, response)
}

func (server *Server) handleGetListingByAuthor(ctx *gin.Context) {
	authpayload := ctx.MustGet(middlewares.AuthorizationPayloadKey).(*token.Payload)

	listings, err := service.GetListingByAuthor(utils.ToLowerCase(authpayload.Email), &server.store)
	if err != nil {
		if strings.Contains(err.Error(), errors.ErrUserDoesNotExists.Message()) {
			server.logger.Error().
				Str("Error", err.Error()).
				Str("request-id", requestid.Get(ctx)).
				Msg("could not get listing by author")
			errorsListslice := make([]model.Error, 0, 1)
			errorsListslice = append(errorsListslice, model.Error{
				Code:    int(errors.ErrCouldNotFetchListing),
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

		server.logger.Error().
			Str("Error", err.Error()).
			Str("request-id", requestid.Get(ctx)).
			Msg("could not get listing by author")
		errorsListslice := make([]model.Error, 0, 1)
		errorsListslice = append(errorsListslice, model.Error{
			Code:    int(errors.ErrCouldNotFetchListing),
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
	if listings == nil {
		result["listings"] = []string{}
	} else {
		result["listings"] = listings
	}
	result["message"] = "listing fetched"
	response := model.HTTPResponse{
		Errors:     nil,
		Result:     &result,
		StatusCode: http.StatusOK,
	}
	ctx.JSON(http.StatusOK, response)
}
```