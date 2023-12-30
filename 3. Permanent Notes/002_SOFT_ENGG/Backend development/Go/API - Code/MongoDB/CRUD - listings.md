

```
package repository

import (
	"context"
	"errors"
	"fmt"
	"strconv"
	"time"

	"github.com/dhiki-labs/chhaaano__backend/internal/db"
	"github.com/dhiki-labs/chhaaano__backend/internal/model"
	"github.com/dhiki-labs/chhaaano__backend/internal/utils"
	"github.com/rs/zerolog/log"
	"go.mongodb.org/mongo-driver/bson"
	"go.mongodb.org/mongo-driver/bson/primitive"
	"go.mongodb.org/mongo-driver/mongo"
	"go.mongodb.org/mongo-driver/mongo/options"
)

type ListingStorer interface {
	CreateListing(listing *model.Listing) error
	GetListingByID(listingID string) (model.Listing, error)
	GetAllListings(
		filters map[string]string,
		sort string,
		page int,
		pageSize int,
		defaultPage int,
		defaultPageSize int) ([]model.Listing, int64, error)
	DeleteListingByID(listingID string) error
	GetUserListings(email string) ([]model.Listing, error)
	UpdateListingByID(listingID string, input *model.UpdateListingRequest) error
}

type ListingStore struct {
	dbClient *mongo.Client
}

func NewListingStore(db *mongo.Client) *ListingStore {
	return &ListingStore{
		dbClient: db,
	}
}

func (l *ListingStore) CreateListing(listing *model.Listing) error {
	// TODO: Pass these values via config file.
	collection := l.dbClient.Database("auth-svc").Collection("listings")

	_, err := collection.InsertOne(context.TODO(), listing)
	if err != nil {
		return fmt.Errorf("could not insert to the collection: %v", err)
	}

	log.Info().Msg("listing created")
	return nil
}

func (l *ListingStore) GetListingByID(listingID string) (model.Listing, error) {
	database := l.dbClient.Database("auth-svc")
	listingCollection := database.Collection("listings")

	var listing model.Listing

	objectID, err := primitive.ObjectIDFromHex(listingID)
	if err != nil {
		return model.Listing{}, err
	}

	filter := bson.D{{Key: "_id", Value: objectID}}

	err = listingCollection.FindOne(context.TODO(), filter).Decode(&listing)
	if err != nil {

		if errors.Is(err, mongo.ErrNoDocuments) {
			return model.Listing{}, fmt.Errorf("document not found: %w", err)
		}

		return model.Listing{}, fmt.Errorf("error fetching from database: %w", err)
	}

	return listing, nil
}

func (l *ListingStore) GetAllListings(filters map[string]string, sort string, page int, pageSize int, defaultPage int, defaultPageSize int) ([]model.Listing, int64, error) {
	database := l.dbClient.Database("auth-svc")
	listingCollection := database.Collection("listings")

	var listings []model.Listing

	ctx := context.Background()

	// making sure only published listings are shown
	filter := bson.M{"listingStatus": "published"}

	// set up options for the find operation. This will return data in order of created date in descending order
	findOptions := options.Find().SetSort(bson.D{{Key: "createdAt", Value: -1}})

	if filters["zip"] != "" {
		filter["zip"] = filters["zip"]
	}

	bedRooms, _ := strconv.Atoi(filters["bedRooms"])
	if bedRooms != 0 {
		filter["noOfBedRooms"] = bedRooms
	}

	bathRooms, _ := strconv.Atoi(filters["bathRooms"])
	if bathRooms != 0 {
		filter["noOfBathRooms"] = bathRooms
	}

	if filters["priceMin"] != "" && filters["priceMax"] != "" {
		priceMinInt, _ := strconv.Atoi(filters["priceMin"])
		priceMaxInt, _ := strconv.Atoi(filters["priceMax"])

		filter["rentAmount"] = bson.M{
			"$gte": priceMinInt,
			"$lt":  priceMaxInt,
		}
	}

	if filters["propertyType"] != "" {
		filter["propertyType"] = bson.M{
			"$regex": primitive.Regex{
				Pattern: filters["propertyType"],
				Options: "i",
			},
		}
	}

	if filters["listingType"] != "" {
		filter["listingType"] = bson.M{
			"$regex": primitive.Regex{
				Pattern: filters["listingType"],
				Options: "i",
			},
		}
	}

	// Sorting by rent_amount
	if sort != "" {
		if sort == "asc" {
			findOptions.SetSort(bson.D{{Key: "rentAmount", Value: 1}})
		}
		if sort == "desc" {
			findOptions.SetSort(bson.D{{Key: "rentAmount", Value: -1}})
		}
	}

	// Pagination
	findOptions.SetSkip((int64(page) - 1) * int64(pageSize))
	findOptions.SetLimit(int64(pageSize))

	total, _ := listingCollection.CountDocuments(ctx, filter)

	cursor, err := listingCollection.Find(ctx, filter, findOptions)
	if err != nil {
		return listings, total, err
	}

	defer func() {
		if err := cursor.Close(ctx); err != nil {
			log.Error().Msg("could not close the cursor")
		}
	}()

	for cursor.Next(ctx) {
		listing := model.NewListing()
		err := cursor.Decode(&listing)
		if err != nil {
			return listings, total, err
		}

		if listing.ListingStatus == "published" {
			listings = append(listings, *listing)
		}
	}

	return listings, total, nil
}

func (l *ListingStore) DeleteListingByID(listingID string) error {
	ctx := context.Background()
	database := l.dbClient.Database("auth-svc")
	listingCollection := database.Collection("listings")
	objectID, _ := primitive.ObjectIDFromHex(listingID)

	_, err := listingCollection.DeleteOne(ctx, bson.D{{Key: "_id", Value: objectID}})
	if err != nil {
		return fmt.Errorf("error deleting document : %w", err)
	}

	return nil
}


// Here we are trying to get all listings created by that user. Since two values are on separate collection, we need to use aggregation
func (l *ListingStore) GetUserListings(email string) ([]model.Listing, error) {
	ctx := context.Background()

	database := l.dbClient.Database("auth-svc")
	usersCollection := database.Collection("users")

	matchStage := bson.D{{Key: "$match", Value: bson.D{{Key: "email", Value: email}}}}

	lookupStage := bson.D{{
		Key: "$lookup",
		Value: bson.D{
			{Key: "from", Value: "listings"},
			{Key: "localField", Value: "email"},
			{Key: "foreignField", Value: "author"},
			{Key: "as", Value: "listings"},
		},
	}}

	showLoadedCursor, err := usersCollection.Aggregate(ctx, mongo.Pipeline{matchStage, lookupStage})
	if err != nil {
		return nil, fmt.Errorf("error aggregating documents: %w", err)
	}

	var userListings []struct {
		Email    string `bson:"email"`
		Listings []model.Listing
	}

	if err = showLoadedCursor.All(ctx, &userListings); err != nil {
		return nil, fmt.Errorf("error fetching documents: %w", err)
	}

	if len(userListings) == 0 {
		return nil, fmt.Errorf("no documents found: %w", err)
	}

	return userListings[0].Listings, err
}

func (l *ListingStore) UpdateListingByID(listingID string, input *model.UpdateListingRequest) error {
	database := l.dbClient.Database("auth-svc")
	listingsCollection := database.Collection("listings")

	// Declare a BSON update object that will change a boolean field to 'false'
	listingIDObj, _ := primitive.ObjectIDFromHex(listingID)

	filter := bson.D{{Key: "_id", Value: listingIDObj}}

	updates := bson.M{}

	if input.Title != "" {
		updates["title"] = utils.ToTitleCase(input.Title)
	}

	if input.Description != "" {
		updates["description"] = input.Description
	}

	if input.PropertyAddress != "" {
		updates["propertyAddress"] = input.PropertyAddress
	}

	if input.Zip != "" {
		updates["zip"] = input.Zip
	}

	if input.RentAmount != "" {
		updates["rentAmount"] = input.RentAmount
	}

	if input.BondAmount != "" {
		updates["bondAmount"] = input.BondAmount
	}

	if input.ListingType != "" {
		updates["listingType"] = input.ListingType
	}

	if input.CurrentNumberOfTenants != 0 {
		updates["currentNoOfTenants"] = input.CurrentNumberOfTenants
	}

	if len(input.ImageUrls) != 0 {
		updates["imageUrls"] = input.ImageUrls
	}

	if input.AvailableFrom != "" {
		updates["availableFrom"] = input.AvailableFrom
	}

	if input.PreferredTenant != "" {
		updates["preferredTenant"] = input.PreferredTenant
	}

	if input.ContactName != "" {
		updates["contactName"] = input.ContactName
	}

	if input.ContactEmail != "" {
		updates["contactEmail"] = input.ContactEmail
	}

	if input.ContactPhoneNumber != "" {
		updates["contactPhoneNumber"] = input.ContactPhoneNumber
	}

	if input.ContactMethod != "" {
		updates["contactMethod"] = input.ContactMethod
	}

	if input.NumberOfBathRooms != 0 {
		updates["noOfBathRooms"] = input.NumberOfBathRooms
	}

	if input.NumberOfBedRooms != 0 {
		updates["noOfBedRooms"] = input.NumberOfBedRooms
	}

	if input.NumberOfParking != 0 {
		updates["noOfParking"] = input.NumberOfParking
	}

	if input.FloorType != "" {
		updates["floorType"] = input.FloorType
	}

	if input.NoticePeriodInWeeks != 0 {
		updates["noticePeriodInWeeks"] = input.NoticePeriodInWeeks
	}

	switch input.ListingStatus {

	case "draft", "pendingApproval":
		updates["listingStatus"] = input.ListingStatus
	default:
		updates["listingStatus"] = "pendingApproval"
	}

	if len(input.AdditionalFeatures) != 0 {
		updates["additionalFeatures"] = input.AdditionalFeatures
	}

	// Changing the last updated date time.
	updates["updatedAt"] = time.Now()

	updateDoc := bson.M{"$set": updates}

	_, err := listingsCollection.UpdateOne(context.TODO(), filter, updateDoc)
	if err != nil {
		return fmt.Errorf("error updating document : %w", err)
	}

	return nil
}

func UpdateManyListings(updatedUser model.User) error {
	client := db.GetDB()

	ctx := context.Background()

	database := client.Database("auth-svc")
	listingsCollection := database.Collection("listings")

	filter := bson.D{{Key: "contactEmail", Value: updatedUser.Email}}

	updates := bson.M{}

	// user, _ := GetUserByEmail(updatedUser.Email)

	// Changing the last updated date time.
	updates["contactName"] = fmt.Sprintf(utils.ToTitleCase(updatedUser.FirstName) + " " + utils.ToTitleCase(updatedUser.LastName))
	updates["contactEmail"] = updatedUser.Email
	updates["contactPhoneNumber"] = updatedUser.PhoneNumber

	updateDoc := bson.M{"$set": updates}

	_, err := listingsCollection.UpdateMany(ctx, filter, updateDoc)
	if err != nil {
		return fmt.Errorf("error updating documents : %w", err)
	}

	return nil
}
```