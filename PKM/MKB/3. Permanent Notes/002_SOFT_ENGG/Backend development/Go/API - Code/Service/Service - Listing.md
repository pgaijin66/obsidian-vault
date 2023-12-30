
```
package service

import (
	"fmt"
	"time"

	"github.com/dhiki-labs/chhaaano__backend/internal/errors"
	"github.com/dhiki-labs/chhaaano__backend/internal/model"
	"github.com/dhiki-labs/chhaaano__backend/internal/repository"
	"github.com/dhiki-labs/chhaaano__backend/internal/token"
	"github.com/dhiki-labs/chhaaano__backend/internal/utils"
	"github.com/dhiki-labs/chhaaano__backend/validators"
	"go.mongodb.org/mongo-driver/bson/primitive"
)

const (
	defaultListingImage = "https://res.cloudinary.com/chhaano/image/upload/v1681062616/defaultImage_ollulr.jpg"
)

func CreateListing(
	authPayload *token.Payload,
	request *model.CreateListingRequest,
	store *repository.Store,
) (*model.Listing, error) {
	if !validators.IsUserExists(authPayload.Email, store) {
		return nil, fmt.Errorf(errors.ErrUserDoesNotExists.Message())
	}

	// Set default image if no image is provided while listing
	if len(request.ImageUrls) == 0 {
		request.ImageUrls = []string{defaultListingImage}
	}

	listing := &model.Listing{
		ID:                     primitive.NewObjectID(),
		Title:                  utils.ToTitleCase(request.Title),
		Description:            request.Description,
		PropertyAddress:        request.PropertyAddress,
		Zip:                    request.Zip,
		RentAmount:             request.RentAmount,
		BondAmount:             request.BondAmount,
		PropertyType:           request.PropertyType,
		ListingType:            request.ListingType,
		CurrentNumberOfTenants: request.CurrentNumberOfTenants,
		ImageUrls:              request.ImageUrls,
		IsIncludeBills:         request.IsIncludeBills,
		AvailableFrom:          request.AvailableFrom,
		PreferredTenant:        request.PreferredTenant,
		ContactMethod:          request.ContactMethod,
		Author:                 authPayload.Email,
		NumberOfBathRooms:      request.NumberOfBathRooms,
		NumberOfBedRooms:       request.NumberOfBedRooms,
		NumberOfParking:        request.NumberOfParking,
		FloorType:              request.FloorType,
		NoticePeriodInWeeks:    request.NoticePeriodInWeeks,
		AdditionalFeatures:     request.AdditionalFeatures,
		CreatedAt:              time.Now(),
		UpdatedAt:              time.Now(),
		ListingStatus:          "pendingApproval",
	}

	// If equest.AuthorName, equest.AuthorEmail, equest.AuthorPhoneNumber is empty, this means the listing was created using
	// FE i.e website. For this case, we need to fetch uer details from token and add it accordingly.
	// If they have value in them, then it means it was created using API.
	if request.ContactName == "" && request.ContactEmail == "" && request.ContactPhoneNumber == "" {
		user, err := store.GetUserByEmail(authPayload.Email)
		if err != nil {
			return nil, fmt.Errorf("could not create listing: %v", err)
		}
		listing.ContactName = fmt.Sprintf(user.FirstName + " " + user.LastName)
		listing.ContactEmail = user.Email
		listing.ContactPhoneNumber = user.PhoneNumber
	} else {
		listing.ContactName = request.ContactName
		listing.ContactEmail = request.ContactEmail
		listing.ContactPhoneNumber = request.ContactPhoneNumber
	}

	// If listing status is draft or pendingApproval then allow, any request other than that will be pendingApproval
	switch request.ListingStatus {
	case "draft", "pendingApproval":
		listing.ListingStatus = request.ListingStatus
	}

	listing.AdditionalFeatures = request.AdditionalFeatures
	listing.CreatedAt = time.Now()
	listing.UpdatedAt = time.Now()

	err := store.CreateListing(listing)
	if err != nil {
		return nil, fmt.Errorf("could not create listing: %v", err)
	}

	return listing, nil
}

func GetListingByID(
	listingID string,
	store *repository.Store,
) (model.Listing, error) {
	listing, err := store.GetListingByID(listingID)
	if err != nil {
		return *model.NewListing(), fmt.Errorf("could not get listing: %v", err)
	}

	return listing, nil
}

func GetAllListings(
	filters map[string]string,
	sort string,
	page int,
	pageSize int,
	defaultPage int,
	defaultPageSize int,
	store *repository.Store,
) ([]model.Listing, int64, error) {
	listings, total, err := store.GetAllListings(filters,
		sort,
		page,
		pageSize,
		defaultPage,
		defaultPageSize)
	if err != nil {
		return nil, 0, fmt.Errorf("could not get listing: %v", err)
	}

	return listings, total, nil
}

func GetListingByAuthor(
	email string,
	store *repository.Store,
) ([]model.Listing, error) {
	if !store.IsUserExists(email) {
		return nil, fmt.Errorf("could not get listing: %v", errors.ErrUserDoesNotExists.Message())
	}

	listings, err := store.GetUserListings(email)
	if err != nil {
		return nil, fmt.Errorf("could not get listing: %v", err)
	}

	return listings, nil
}

func DeleteListing(
	authPayload *token.Payload,
	listingID string,
	store *repository.Store,
) error {
	if !validators.IsUserExists(authPayload.Email, store) {
		return fmt.Errorf(errors.ErrUserDoesNotExists.Message())
	}

	listing, err := store.GetListingByID(listingID)
	if err != nil {
		return fmt.Errorf("listing not found: %v", err)
	}

	if !validators.IsUserOwner(authPayload.Email, listing) {
		return fmt.Errorf(errors.ErrUserDoesNotOwn.Message())
	}

	err = store.DeleteListingByID(listingID)
	if err != nil {
		return fmt.Errorf("could not delete listing: %v", err)
	}

	return nil
}

func UpdateListing(
	authPayload *token.Payload,
	listingID string,
	request *model.UpdateListingRequest,
	store *repository.Store,
) (*model.Listing, error) {
	if !validators.IsUserExists(authPayload.Email, store) {
		return nil, fmt.Errorf(errors.ErrUserDoesNotExists.Message())
	}

	listing, err := store.GetListingByID(listingID)
	if err != nil {
		return nil, fmt.Errorf("listing not found: %v", err)
	}

	// Check if user is owner of the listing
	if !validators.IsUserOwner(authPayload.Email, listing) {
		return nil, fmt.Errorf(errors.ErrUserDoesNotExists.Message())
	}

	err = store.UpdateListingByID(listingID, request)
	if err != nil {
		return nil, fmt.Errorf("could not update listing: %v", err)
	}

	updatedListing, err := store.GetListingByID(listingID)
	if err != nil {
		return nil, fmt.Errorf("listing not found: %v", err)
	}

	return &updatedListing, nil
}
```