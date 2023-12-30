

```
package server

import (
	"context"
	"fmt"
	"log"
	"mime/multipart"
	"net/http"

	"github.com/cloudinary/cloudinary-go"
	"github.com/cloudinary/cloudinary-go/api/uploader"
	"github.com/dhiki-labs/chhaaano__backend/internal/middlewares"
	"github.com/dhiki-labs/chhaaano__backend/internal/model"
	"github.com/dhiki-labs/chhaaano__backend/internal/random"
	"github.com/dhiki-labs/chhaaano__backend/internal/token"
	"github.com/dhiki-labs/chhaaano__backend/internal/utils"
	"github.com/gin-gonic/gin"
)

type CLDImage struct {
	ImageFile multipart.File
	PublicID  string
}

func (server *Server) handleUploadAvatarImage(c *gin.Context) {
	// check if user is authenticated
	authPayload := c.MustGet(middlewares.AuthorizationPayloadKey).(*token.Payload)

	cld, err := cloudinary.New()
	if err != nil {
		log.Fatalf("Failed to intialize Cloudinary, %v", err)
	}
	// TODO: Add max upload size

	form, err := c.MultipartForm()
	if errorMessage(err, "Failed to parse multipart form", c) {
		return
	}

	// all files passed during upload should have name as file
	fileHeaders := form.File["file"]

	userEmail := authPayload.Email

	user, err := server.store.GetUserByEmail(userEmail)
	if err != nil {
		log.Fatalf("Failed to fetch user details, %v", err)
	}

	// Converting primitive objectID to String.
	userUUID := user.ID.Hex()

	var images []CLDImage

	for _, fileHeader := range fileHeaders {
		renderFile, err := fileHeader.Open()
		if errorMessage(err, "Could not open image file for rendering", c) {
			return
		}
		defer func() {
			if err := renderFile.Close(); err != nil {
				server.logger.Error().
					Msg("could not close file: " + err.Error())
			}
		}()
		var profileImageID string
		profileImageID, err = generateProfileImageID(userUUID)
		if errorMessage(err, "Could not generate profileImageID", c) {
			return
		}

		images = append(images, CLDImage{
			ImageFile: renderFile,
			PublicID:  profileImageID,
		})
	}

	resp, err := uploadNewImage(cld, c, images[0])
	if errorMessage(err, "Could not upload avatar image to cloudinary", c) {
		return
	}

	newImageURL := resp.SecureURL

	updatedUser := model.NewUpdateUserRequest()

	updatedUser.FirstName = utils.ToTitleCase(user.FirstName)
	updatedUser.LastName = utils.ToTitleCase(user.LastName)
	updatedUser.Email = utils.ToLowerCase(user.Email)
	updatedUser.PhoneNumber = user.PhoneNumber
	updatedUser.Address = user.Address

	updatedUser.AvatarURL = newImageURL

	err = server.store.UpdateUserAccountByID(userUUID, updatedUser)
	if err != nil {
		result := model.Result{}
		result["message"] = "could not upload avatar image"
		response := model.HTTPResponse{
			Errors:     nil,
			Result:     &result,
			StatusCode: http.StatusInternalServerError,
		}
		c.JSON(http.StatusOK, response)
		return
	}

	user, err = server.store.GetUserByID(userUUID)
	if err != nil {
		result := model.Result{}
		result["message"] = "could not upload avatar image"
		response := model.HTTPResponse{
			Errors:     nil,
			Result:     &result,
			StatusCode: http.StatusInternalServerError,
		}
		c.JSON(http.StatusOK, response)
		return
	}

	result := model.Result{}
	result["message"] = "avatar image uploaded"
	result["data"] = newImageURL
	response := model.HTTPResponse{
		Errors:     nil,
		Result:     &result,
		StatusCode: http.StatusAccepted,
	}
	c.JSON(http.StatusAccepted, response)
}

func (server *Server) handleUploadListingImage(c *gin.Context) {
	// check if user is authenticated
	authPayload := c.MustGet(middlewares.AuthorizationPayloadKey).(*token.Payload)

	cld, err := cloudinary.New()
	if err != nil {
		log.Fatalf("Failed to intialize Cloudinary, %v", err)
	}

	// TODO: Add max upload size

	form, err := c.MultipartForm()
	if errorMessage(err, "Failed to parse multipart form", c) {
		return
	}

	// all files passed during upload should have name as file
	fileHeaders := form.File["file"]

	userEmail := authPayload.Email

	user, err := server.store.GetUserByEmail(userEmail)
	if err != nil {
		log.Fatalf("Failed to fetch user details, %v", err)
	}

	// Converting primitive objectID to String.
	userUUID := user.ID.Hex()

	listingID := c.Param("listing-id")

	var images []CLDImage

	for _, fileHeader := range fileHeaders {
		renderFile, err := fileHeader.Open()
		if errorMessage(err, "Could not open image file for rendering", c) {
			return
		}
		defer func() {
			if err := renderFile.Close(); err != nil {
				server.logger.Error().
					Msg("could not close file: " + err.Error())
			}
		}()
		var imageID string
		imageID, err = generateListingImageID(userUUID, listingID)
		if errorMessage(err, "Could not generate listingImageID", c) {
			return
		}

		images = append(images, CLDImage{
			ImageFile: renderFile,
			PublicID:  imageID,
		})
	}

	respArray, err := uploadImageInBulk(cld, c, images)
	if errorMessage(err, "Could not upload images in bulk to cloudinary", c) {
		return
	}

	listing, _ := server.store.GetListingByID(listingID)

	newImageURL := make([]string, 0, 1)

	for _, resp := range respArray {
		newImageURL = append(newImageURL, resp.SecureURL)
	}

	// listing.ImageUrls = newImageURL

	updatedListing := model.NewUpdateListingRequest()

	updatedListing.Title = listing.Title
	updatedListing.Description = listing.Description
	updatedListing.PropertyAddress = listing.PropertyAddress
	updatedListing.Zip = listing.Zip
	updatedListing.RentAmount = listing.RentAmount
	updatedListing.BondAmount = listing.BondAmount
	updatedListing.PropertyType = listing.PropertyType
	updatedListing.ListingType = listing.ListingType
	updatedListing.CurrentNumberOfTenants = listing.CurrentNumberOfTenants

	updatedListing.ImageUrls = newImageURL

	updatedListing.AvailableFrom = listing.AvailableFrom
	updatedListing.PreferredTenant = listing.PreferredTenant
	updatedListing.ContactMethod = listing.ContactMethod
	updatedListing.NumberOfBathRooms = listing.NumberOfBathRooms
	updatedListing.NumberOfBedRooms = listing.NumberOfBedRooms
	updatedListing.NumberOfParking = listing.NumberOfParking
	updatedListing.FloorType = listing.FloorType
	updatedListing.NoticePeriodInWeeks = listing.NoticePeriodInWeeks
	updatedListing.ListingStatus = listing.ListingStatus
	updatedListing.AdditionalFeatures = listing.AdditionalFeatures

	err = server.store.UpdateListingByID(listingID, updatedListing)
	if err != nil {
		result := model.Result{}
		result["message"] = "could not upload listing image"
		response := model.HTTPResponse{
			Errors:     nil,
			Result:     &result,
			StatusCode: http.StatusInternalServerError,
		}
		c.JSON(http.StatusOK, response)
		return
	}

	result := model.Result{}
	result["data"] = newImageURL
	result["message"] = "listing image uploaded"
	response := model.HTTPResponse{
		Errors:     nil,
		Result:     &result,
		StatusCode: http.StatusInternalServerError,
	}
	c.JSON(http.StatusOK, response)
}

// TODO : Move this to integrations / cloudinary folder
func uploadNewImage(cld *cloudinary.Cloudinary, c context.Context, images CLDImage) (resp *uploader.UploadResult, err error) {
	// Upload the image.
	// Set the asset's public ID and allow overwriting the asset with new versions

	resp, err = cld.Upload.Upload(c, images.ImageFile, uploader.UploadParams{
		PublicID:       images.PublicID,
		UniqueFilename: false,
		Overwrite:      true,
	})
	if err != nil {
		return nil, fmt.Errorf("could not upload images to cloudinary: %v", err)
	}
	return resp, err
}

// TODO: Move this to integrations / cloudinary folder
func uploadImageInBulk(cld *cloudinary.Cloudinary, ctx context.Context, images []CLDImage) (respArray []*uploader.UploadResult, err error) {
	for _, image := range images {
		resp, err := uploadNewImage(cld, ctx, image)
		if err != nil {
			return nil, fmt.Errorf("could not upload images to cloudinary: %v", err)
		}
		respArray = append(respArray, resp)
	}
	return respArray, err
}

// TODO: Move this to integrations / cloudinary folder
func generateProfileImageID(userUUID string) (profileImageID string, err error) {
	uniqueID, err := random.GenerateRandomString(5)
	if err != nil {
		return uniqueID, fmt.Errorf("could not generate profileImageId: %V", err)
	}
	return userUUID + "profileImages/" + uniqueID, err
}

// TODO: Move this to integrations / cloudinary folder
func generateListingImageID(userUUID string, listingID string) (listingImageID string, err error) {
	uniqueID, err := random.GenerateRandomString(5)
	if err != nil {
		return uniqueID, fmt.Errorf("could not generate listingImageID: %V", err)
	}

	return userUUID + "/listingImages/" + listingID + "/" + uniqueID, err
}

func errorMessage(err error, message string, c *gin.Context) bool {
	if err != nil {
		c.JSON(http.StatusInternalServerError, gin.H{
			"status":  http.StatusInternalServerError,
			"data":    "null",
			"message": message,
		})
		return true
	}
	return false
}
```