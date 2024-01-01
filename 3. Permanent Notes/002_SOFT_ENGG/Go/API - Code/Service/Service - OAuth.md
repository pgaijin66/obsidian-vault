
```
package service

import (
	"context"
	"encoding/json"
	"fmt"
	"time"

	"github.com/dhiki-labs/chhaaano__backend/internal/errors"
	"github.com/dhiki-labs/chhaaano__backend/internal/model"
	"github.com/dhiki-labs/chhaaano__backend/internal/password"
	"github.com/dhiki-labs/chhaaano__backend/internal/random"
	"github.com/dhiki-labs/chhaaano__backend/internal/repository"
	"github.com/dhiki-labs/chhaaano__backend/internal/token"
	"github.com/dhiki-labs/chhaaano__backend/internal/utils"
	"go.mongodb.org/mongo-driver/bson/primitive"
	"golang.org/x/oauth2"
)

func OAuthCallback(
	authorizatioCode string,
	oauth2Config *oauth2.Config,
	tokenMaker token.Maker,
	tokenDuration time.Duration,
	store *repository.Store,
	endpoint string,
) (*model.User, string, time.Time, error) {
	ctx := context.Background()
	token, err := oauth2Config.Exchange(ctx, authorizatioCode)
	if err != nil {
		// TODO(pthapa): add proper error here
		return nil, "", time.Now(), fmt.Errorf("%v, %v", errors.ErrUserDoesNotExists.Message(), err)
	}

	// Check if the access token is still valid
	if !token.Valid() {
		return nil, "", time.Now(), fmt.Errorf("could not validate token")
	}

	// if token is valid not we get user details
	// Get user info from Google API
	email, firstName, lastname, avatarURL, err := getUserInfoUsingProviderAPI(oauth2Config, token, endpoint)
	if err != nil {
		return nil, "", time.Now(), fmt.Errorf("could not get user info from google auth server: %v", err)
	}

	// check if user exists and if it does not, create a new one.
	exists := store.IsUserExists(utils.ToLowerCase(email))
	if !exists {
		user := model.NewUser()
		user.ID = primitive.NewObjectID()
		user.FirstName = utils.ToTitleCase(firstName)
		user.LastName = utils.ToTitleCase(lastname)

		user.Email = utils.ToLowerCase(email)

		user.Address = defaultAddress
		user.AvatarURL = avatarURL
		user.PhoneNumber = defaultPhoneNumber
		user.IsEmailVerified = defaultIsEmailVerified
		user.IsAccountDisabled = defaultIsAccountDisabled
		user.HasRole = defaultRole

		// some random password, this will not be used. This is better than leaving it as empty
		pw, _ := random.GenerateRandomString(21)
		user.HashedPassword, _ = password.GetHash(pw)

		user.CreatedAt = time.Now()
		user.UpdatedAt = time.Now()
		user.LastLogin = time.Now()
		err := store.CreateUser(user)
		if err != nil {
			return nil, "", time.Now(), fmt.Errorf("could not create user: %v", err)
		}
	}

	accessToken, expiresAt, err := tokenMaker.CreateToken(
		utils.ToLowerCase(email),
		tokenDuration,
	)
	if err != nil {
		return nil, "", time.Now(), fmt.Errorf("%v, %v", errors.ErrCouldNotCreateAccessToken.Message(), err)
	}

	user, err := store.GetUserByEmail(email)
	if err != nil {
		return nil, "", time.Now(), fmt.Errorf("could not create user: %v", err)
	}

	return &user, accessToken, expiresAt, nil
}

func getUserInfoUsingProviderAPI(oauth2Config *oauth2.Config, token *oauth2.Token, endpoint string) (email string, firstName string, lastName string, avatarURL string, err error) {
	client := oauth2Config.Client(context.Background(), token)
	resp, err := client.Get(endpoint)
	if err != nil {
		return "", "", "", "", fmt.Errorf("could not fetch user information")
	}

	// Parse the response body to get user info
	var userInfo struct {
		Email     string `json:"email"`
		FirstName string `json:"given_name"`
		LastName  string `json:"family_name"`
		AvatarURL string `json:"picture"`
	}
	err = json.NewDecoder(resp.Body).Decode(&userInfo)
	if err != nil {
		return "", "", "", "", fmt.Errorf("could not decode user information")
	}

	return userInfo.Email, userInfo.FirstName, userInfo.LastName, userInfo.AvatarURL, nil
}
```