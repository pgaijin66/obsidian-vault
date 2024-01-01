
```
package repository

import (
	"context"
	"errors"
	"fmt"
	"time"

	"github.com/dhiki-labs/chhaaano__backend/internal/model"
	"github.com/dhiki-labs/chhaaano__backend/internal/utils"
	"github.com/rs/zerolog/log"
	"go.mongodb.org/mongo-driver/bson"
	"go.mongodb.org/mongo-driver/bson/primitive"
	"go.mongodb.org/mongo-driver/mongo"
)

// TODO: Remove db.GetDB call and use it via dependency injection

type UserStorer interface {
	CreateUser(user *model.User) error
	GetAllUsers() ([]model.User, error)
	GetUserByEmail(email string) (model.User, error)
	GetUserByID(userID string) (model.User, error)
	UpdateUserAccount(userID string, updateUserRequest *model.UpdateUserRequest) error
	UpdateUserAccountByID(userID string, input *model.UpdateUserRequest) error
	UpdateUserAccountByKey(userID string, key string, value interface{}) error
	IsUserExists(email string) bool
	DeleteUserByID(userID string) error
}

type UserStore struct {
	dbClient *mongo.Client
}

func NewUserStore(db *mongo.Client) *UserStore {
	return &UserStore{
		dbClient: db,
	}
}

func (u *UserStore) CreateUser(user *model.User) error {
	collection := u.dbClient.Database("auth-svc").Collection("users")

	_, err := collection.InsertOne(context.TODO(), user)
	if err != nil {
		return fmt.Errorf("could not insert to the collection: %v", err)
	}

	log.Info().Msg("user created")
	return nil
}

func (u *UserStore) GetAllUsers() ([]model.User, error) {
	database := u.dbClient.Database("auth-svc")
	usersCollection := database.Collection("users")

	var user model.User
	var users []model.User

	ctx := context.Background()

	cursor, err := usersCollection.Find(ctx, bson.D{})
	if err != nil {
		defer func() {
			if err := cursor.Close(ctx); err != nil {
				log.Error().Msg("could not close the cursor")
			}
		}()
		return users, err
	}

	for cursor.Next(ctx) {
		err := cursor.Decode(&user)
		if err != nil {
			return users, err
		}
		users = append(users, user)
	}

	return users, nil
}

func (u *UserStore) GetUserByEmail(email string) (model.User, error) {
	ctx := context.Background()

	result := model.User{}

	database := u.dbClient.Database("auth-svc")
	usersCollection := database.Collection("users")

	filter := bson.M{"email": email}

	err := usersCollection.FindOne(ctx, filter).Decode(&result)
	if err != nil {
		if errors.Is(err, mongo.ErrNoDocuments) {
			return model.User{}, fmt.Errorf("document not found: %w", err)
		}
		return model.User{}, fmt.Errorf("error fetching from database: %w", err)
	}

	return result, nil
}

func (u *UserStore) GetUserByID(userID string) (model.User, error) {
	result := model.User{}

	database := u.dbClient.Database("auth-svc")
	usersCollection := database.Collection("users")

	objectID, _ := primitive.ObjectIDFromHex(userID)

	err := usersCollection.FindOne(context.TODO(), bson.D{{Key: "_id", Value: objectID}}).Decode(&result)
	if err != nil {
		if errors.Is(err, mongo.ErrNoDocuments) {
			return model.User{}, fmt.Errorf("document not found: %w", err)
		}
		return model.User{}, fmt.Errorf("error fetching from database: %w", err)
	}

	return result, nil
}

func (u *UserStore) UpdateUserAccount(userID string, updateUserRequest *model.UpdateUserRequest) error {
	database := u.dbClient.Database("auth-svc")
	usersCollection := database.Collection("users")

	// Set the filter for the MongoDB update
	userIDObj, _ := primitive.ObjectIDFromHex(userID)

	filter := bson.M{"_id": userIDObj}

	updates := bson.M{}
	if updateUserRequest.Email != "" {
		updates["email"] = utils.ToLowerCase(updateUserRequest.Email)
	}

	if updateUserRequest.Address != "" {
		updates["address"] = updateUserRequest.Address
	}

	if updateUserRequest.FirstName != "" {
		updates["firstName"] = utils.ToTitleCase(updateUserRequest.FirstName)
	}

	if updateUserRequest.LastName != "" {
		updates["lastName"] = utils.ToTitleCase(updateUserRequest.LastName)
	}

	if updateUserRequest.AvatarURL != "" {
		updates["avatarUrl"] = updateUserRequest.AvatarURL
	}

	if updateUserRequest.PhoneNumber != "" {
		updates["phoneNumber"] = updateUserRequest.PhoneNumber
	}

	// Changing the last updated date time.
	updates["updatedAt"] = time.Now()

	updateDoc := bson.M{"$set": updates}

	// Define options for the MongoDB update
	// options := options.Update().SetUpsert(false)

	_, err := usersCollection.UpdateOne(context.TODO(), filter, updateDoc)
	if err != nil {
		return fmt.Errorf("error updating document : %w", err)
	}

	return nil
}

func (u *UserStore) UpdateUserAccountByID(userID string, input *model.UpdateUserRequest) error {
	ctx := context.Background()

	database := u.dbClient.Database("auth-svc")
	usersCollection := database.Collection("users")

	// Declare a BSON update object that will change a boolean field to 'false'
	objectID, _ := primitive.ObjectIDFromHex(userID)
	filter := bson.D{{Key: "_id", Value: objectID}}

	// Create a nested BSON document for the documents' fields that are updated
	update := bson.D{{Key: "$set", Value: bson.D{
		{Key: "firstName", Value: input.FirstName},
		{Key: "lastName", Value: input.LastName},
		{Key: "email", Value: input.Email},
		{Key: "phoneNumber", Value: input.PhoneNumber},
		{Key: "address", Value: input.Address},
		{Key: "avatarUrl", Value: input.AvatarURL},
	}}}

	_, err := usersCollection.UpdateOne(ctx, filter, update)
	if err != nil {
		return fmt.Errorf("error updating document: %w", err)
	}

	return nil
}

func (u *UserStore) DeleteUserByID(userID string) error {
	ctx := context.Background()

	database := u.dbClient.Database("auth-svc")
	listingCollection := database.Collection("users")
	objectID, _ := primitive.ObjectIDFromHex(userID)

	_, err := listingCollection.DeleteOne(ctx, bson.D{{Key: "_id", Value: objectID}})
	if err != nil {
		return fmt.Errorf("error deleting document : %w", err)
	}

	return nil
}

func (u *UserStore) IsUserExists(email string) bool {
	filter := bson.M{"email": email}

	result := model.User{}

	collection := u.dbClient.Database("auth-svc").Collection("users")

	err := collection.FindOne(context.TODO(), filter).Decode(&result)
	if err != nil {
		if errors.Is(err, mongo.ErrNoDocuments) {
			return false
		}
	}
	return true
}

func (u *UserStore) UpdateUserAccountByKey(userID string, key string, value interface{}) error {
	database := u.dbClient.Database("auth-svc")
	usersCollection := database.Collection("users")

	// filter := bson.M{"email": email}
	objectID, _ := primitive.ObjectIDFromHex(userID)
	filter := bson.D{{Key: "_id", Value: objectID}}

	update := bson.D{{Key: "$set", Value: bson.D{{Key: key, Value: value}}}}

	_, err := usersCollection.UpdateOne(context.TODO(), filter, update)
	if err != nil {
		return fmt.Errorf("error updating document : %w", err)
	}

	return nil
}
```