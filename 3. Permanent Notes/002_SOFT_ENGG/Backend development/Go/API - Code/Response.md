
```
package model

import (
	"time"

	"go.mongodb.org/mongo-driver/bson/primitive"
)

// Error represents a new error
type Error struct {
	Code    int    `json:"code"`
	Message string `json:"message"`
}

type Result map[string]interface{}

type HTTPResponse struct {
	Errors     []Error `json:"errors"`
	StatusCode int     `json:"status_code"`
	Result     *Result `json:"result"`
}

type ServiceStatus struct {
	Database   string `json:"database"`
	Redis      string `json:"redis"`
	Cloudinary string `json:"cloudinary"`
}

type UserResonse struct {
	ID              primitive.ObjectID `json:"id,omitempty"`
	Email           string             `json:"email"`
	FirstName       string             `json:"first_name"`
	LastName        string             `json:"last_name"`
	Address         string             `json:"address"`
	UpdatedAt       time.Time          `json:"updated_at"`
	CreatedAt       time.Time          `json:"created_at"`
	PhoneNumber     string             `bson:"phoneNumber" json:"phone_number"`
	AvatarURL       string             `bson:"avatarUrl" json:"avatar_url"`
	IsEmailVerified bool               `bson:"isEmailVerified" json:"is_verified"`
	LastLogin       time.Time          `bson:"lastLogin" json:"last_login"`
}

type CreateListingResponse struct {
	ID                     primitive.ObjectID `json:"id,omitempty"`
	Title                  string             `json:"title"`
	PropertyAddress        string             `json:"property_address"`
	Zip                    int                `json:"zip"`
	RentAmount             string             `json:"rent_amount"`
	BondAmount             string             `json:"bond_amount"`
	CurrentNumberOfTenants int                `json:"current_no_of_tenants"`
	PropertyType           string             `json:"property_type"`
	Author                 string             `bson:"author" json:"author"`
	ContactName            string             `bson:"contactName" json:"contact_name"`
	ContactEmail           string             `bson:"contactEmail" json:"contact_email"`
	ContactPhoneNumber     string             `bson:"contactPhoneNumber" json:"contact_phone_number"`
	AvailableFrom          string             `json:"available_form"`
	ImageUrls              []string           `json:"image_urls"`
	Features               []string           `json:"features"`
	NumberOfBathRooms      int                `json:"no_of_bath_rooms"`
	NumberOfBedRooms       int                `json:"no_of_bed_rooms"`
	NoticePeriodInWeeks    int                `json:"notice_period_in_weeks"`
	PreferredTenant        string             `json:"preferred_tenant"`
	Description            string             `json:"description"`
	PreferredContactMethod string             `json:"contact_method"`
}

type OTPValidateResponse struct {
	// The OTP sent to the user email
	OTP string `json:"otp" binding:"required"`
	// A 16 to 40 character long string with random unique data
	Nonce string `json:"nonce" binding:"required"`
	// transaction id
	CreatedAt string `json:"created_at" binding:"required"`
}
```