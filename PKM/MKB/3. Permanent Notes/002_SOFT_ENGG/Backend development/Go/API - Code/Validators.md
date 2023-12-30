
```go
package validators

import (
	"errors"
	"net/mail"
	"os"
	"strings"

	"github.com/dhiki-labs/chhaaano__backend/internal/model"
	"github.com/dhiki-labs/chhaaano__backend/internal/repository"
	"github.com/dhiki-labs/chhaaano__backend/internal/token"
)

const (
	DefaultPasswordLength = 9
)

// ValidateCreateListingRequest validates listings when they are created
func ValidateCreateListingRequest(request *model.CreateListingRequest) error {
	validPropertyTypes := map[string]bool{"townHouse": true, "grannyHouse": true, "apartment": true, "studio": true}
	validListingTypes := map[string]bool{"singleRoom": true, "sharing": true, "leaseTransfer": true, "leasing": true}
	validPreferredTenants := map[string]bool{"single": true, "couple": true}
	validContactMethods := map[string]bool{"phone": true, "message": true, "email": true}
	validFloorTypes := map[string]bool{"carpet": true, "timber": true}

	if !validPropertyTypes[request.PropertyType] {
		return errors.New("property type must be either townHouse or grannyHouse or apartment or studio")
	}

	if !validListingTypes[request.ListingType] {
		return errors.New("listing type must be either singleRoom or sharing or leaseTransfer")
	}

	if !validPreferredTenants[request.PreferredTenant] {
		return errors.New("preferred tenant must be either single or couple")
	}

	if !validContactMethods[request.ContactMethod] {
		return errors.New("contact method must be either phone or message or email")
	}

	if !validFloorTypes[request.FloorType] {
		return errors.New("floor type must be either carpet or timber")
	}

	return nil
}

func IsFileExists(filepath string) bool {
	if _, err := os.Stat(filepath); err != nil {
		return false
	}
	return true
}

func IsDefined(configVariable string) bool {
	return configVariable != ""
}

func NotIn(s []string, str string) bool {
	for _, v := range s {
		if v == str {
			return true
		}
	}
	return false
}

func IsEmail(email string) bool {
	_, err := mail.ParseAddress(email)
	return err == nil
}

func IsPasswordOfValidLength(s string) bool {
	return len(s) >= DefaultPasswordLength
}

func IsSame(s1 string, s2 string) bool {
	return s1 == s2
}

func PasswordContainsName(password string, email string) bool {
	// Split email address into username and domain
	parts := strings.Split(email, "@")
	username := parts[0]

	sequenceLength := 3

	// Loop through each character of the string
	for i := 0; i <= len(username)-sequenceLength; i++ {
		// Check if a substring of sequenceLength containing the current character appears in the password
		substring := username[i : i+sequenceLength]
		if strings.Contains(password, substring) {
			return true
		}
	}
	return false
}

// IsUserExists returns whether the user exists or not. It takes `userEmail string` as an input and returns a bool value.
func IsUserExists(userEmail string, store *repository.Store) bool {
	_, err := store.GetUserByEmail(userEmail)
	return err == nil
}

// IsUserOwner returns whether user owns the resource. It takes `userEmail string and listing model.Listing` as an input and returns a bool value.
func IsUserOwner(userEmail string, listing model.Listing) bool {
	return listing.Author == userEmail
}

// IsAccountDisabled returns whether the user account is disabled or not. It takes `user model.User“ as an input and returns a bool value.
func IsAccountDisabled(user model.User, store *repository.Store) bool {
	_, err := store.GetUserByEmail(user.Email)
	if err != nil {
		return false
	}

	if user.IsAccountDisabled {
		return true
	}
	return false
}

// IsAuthenticated returns whether the user account is authenticated or not. It takes `authPayload *token.Payload“ as an input and returns a bool value.
func IsAuthenticated(authPayload *token.Payload) bool {
	// 1. check if user exists

	// 2. Check if palyload if valid
	return false
}

// IsAuthorised returns whether the user account is authorized to perform said operation. It takes `userEmail string, userID string` as an input and returns a bool value.
func IsAuthorised(userEmail string, userID string, store *repository.Store) bool {
	user, err := store.GetUserByID(userID)
	if err != nil {
		return false
	}

	if user.Email == userEmail {
		return true
	}

	return false
}
```