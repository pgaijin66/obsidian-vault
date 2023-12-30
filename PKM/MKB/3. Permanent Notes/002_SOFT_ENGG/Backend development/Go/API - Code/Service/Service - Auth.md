
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
	"github.com/rs/zerolog/log"
)

// Login is used to login . It takes `request *model.LoginUserRequest, tokenMaker token.Maker, tokenDuration time.Duration`
// as an input and returns user model and access token.
func Login(
	request *model.LoginUserRequest,
	tokenMaker token.Maker,
	tokenDuration time.Duration,
	store *repository.Store,
) (*model.User, string, time.Time, error) {
	user, err := store.GetUserByEmail(utils.ToLowerCase(request.Email))
	if err != nil {
		return nil, "", time.Now(), fmt.Errorf("%v, %v", errors.ErrUserDoesNotExists.Message(), err)
	}

	userHashPassword := fmt.Sprintf("%v", user.HashedPassword)
	ok, _ := password.Matches(request.Password, userHashPassword)
	if !ok {
		return nil, "", time.Now(), fmt.Errorf("%v, %v", errors.ErrEmailPasswordIncorrect.Message(), err)
	}

	if validators.IsAccountDisabled(user, store) {
		return nil, "", time.Now(), fmt.Errorf("%v, %v", errors.ErrAccountDisabled.Message(), err)
	}

	accessToken, expiresAt, err := tokenMaker.CreateToken(
		utils.ToLowerCase(request.Email),
		tokenDuration,
	)
	if err != nil {
		return nil, "", time.Now(), fmt.Errorf("%v, %v", errors.ErrCouldNotCreateAccessToken.Message(), err)
	}

	err = store.UpdateUserAccountByKey(user.ID.Hex(), "lastLogin", time.Now())
	if err != nil {
		log.Info().Msg("could not update last login time.")
	}

	return &user, accessToken, expiresAt, nil
}

// LockUserAccount disables user account. It takes `email string` as an input.
func LockUserAccount(userID string, store *repository.Store) error {
	err := store.UpdateUserAccountByKey(userID, "isAccountDisabled", true)
	if err != nil {
		return fmt.Errorf("could not lock user account, %v", err)
	}

	err = store.UpdateUserAccountByKey(userID, "changedAt", time.Now())
	if err != nil {
		log.Info().Msg("could not update changed time.")
	}
	return nil
}
```