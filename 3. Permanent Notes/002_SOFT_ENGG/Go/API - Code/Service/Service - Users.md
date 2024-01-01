
```
package service

import (
	"fmt"
	"time"

	"github.com/dhiki-labs/chhaaano__backend/internal/errors"
	"github.com/dhiki-labs/chhaaano__backend/internal/model"
	"github.com/dhiki-labs/chhaaano__backend/internal/password"
	"github.com/dhiki-labs/chhaaano__backend/internal/repository"
	"github.com/dhiki-labs/chhaaano__backend/internal/token"
	"github.com/dhiki-labs/chhaaano__backend/internal/utils"
	"github.com/dhiki-labs/chhaaano__backend/validators"
	"go.mongodb.org/mongo-driver/bson/primitive"
)

const (
	defaultRole              = 0
	defaultFirstName         = ""
	defaultLastName          = ""
	defaultIsEmailVerified   = false
	defaultIsAccountDisabled = false
	defaultPhoneNumber       = ""
	defaultPhotoURL          = "https://res.cloudinary.com/chhaano/image/upload/v1681364024/avatar_ypl7ck.png"
	defaultAddress           = ""
)

func CreateUser(
	request *model.CreateUserRequest,
	tokenMaker token.Maker,
	tokenDuration time.Duration,
	store *repository.Store,
) (*model.User, string, time.Time, error) {
	user := model.NewUser()
	user.ID = primitive.NewObjectID()
	user.FirstName = utils.ToTitleCase(defaultFirstName)
	user.LastName = utils.ToTitleCase(defaultLastName)

	user.Email = utils.ToLowerCase(request.Email)

	user.Address = defaultAddress
	user.AvatarURL = defaultPhotoURL
	user.PhoneNumber = defaultPhoneNumber
	user.IsEmailVerified = defaultIsEmailVerified
	user.IsAccountDisabled = defaultIsAccountDisabled
	user.HasRole = defaultRole

	user.HashedPassword, _ = password.GetHash(request.Password)

	user.CreatedAt = time.Now()
	user.UpdatedAt = time.Now()
	user.LastLogin = time.Now()

	exists := store.IsUserExists(request.Email)
	if exists {
		return nil, "", time.Now(), fmt.Errorf(errors.ErrUserAlreadyExists.Message())
	}

	err := store.CreateUser(user)
	if err != nil {
		return nil, "", time.Now(), fmt.Errorf("%v: %v", errors.ErrCouldNotCreateUser.Message(), err)
	}

	accessToken, expiresAt, err := tokenMaker.CreateToken(
		utils.ToLowerCase(request.Email),
		tokenDuration,
	)
	if err != nil {
		return nil, "", time.Now(), fmt.Errorf("%v, %v", errors.ErrCouldNotCreateAccessToken.Message(), err)
	}

	return user, accessToken, expiresAt, nil
}

func GetUserByID(
	userID string,
	store *repository.Store,
) (*model.User, error) {
	user, err := store.GetUserByID(userID)
	if err != nil {
		return nil, fmt.Errorf("could not get user: %v", err)
	}

	return &user, nil
}

func GetAllUsers(
	store *repository.Store,
) ([]model.User, error) {
	users, err := store.GetAllUsers()
	if err != nil {
		return nil, fmt.Errorf("could not get users: %v", err)
	}

	return users, nil
}

func UpdateUser(userID string, authPayload *token.Payload, updateUserRequest *model.UpdateUserRequest,
	store *repository.Store,
) (*model.User, error) {
	user, err := store.GetUserByEmail(utils.ToLowerCase(authPayload.Email))
	if err != nil {
		return nil, fmt.Errorf("could not update listing: %v", err)
	}

	if !utils.IsEqual(userID, user.ID.Hex()) {
		return nil, fmt.Errorf("user not same. not authorized")
	}

	err = store.UpdateUserAccount(userID, updateUserRequest)
	if err != nil {
		return nil, fmt.Errorf("could not update listing: %v", err)
	}

	user, err = store.GetUserByID(userID)
	if err != nil {
		return nil, fmt.Errorf("could not get listing: %v", err)
	}

	// now we update all the listings document that has user as contact person
	err = repository.UpdateManyListings(user)
	if err != nil {
		return nil, fmt.Errorf("could not update listing contact details: %v", err)
	}

	return &user, nil
}

func DeleteUser(userID string, authPayload *token.Payload,
	store *repository.Store,
) error {
	// TODO: make sure if user is authorized to delete ownself

	if !validators.IsAuthorised(authPayload.Email, userID, store) {
		return fmt.Errorf(errors.ErrNotAuthorized.Message())
	}

	err := store.DeleteUserByID(userID)
	if err != nil {
		return fmt.Errorf("could not delete user: %v", err)
	}

	return nil
}
```