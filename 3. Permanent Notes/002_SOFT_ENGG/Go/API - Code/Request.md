
```
package model

// TODO: Revisit all the values which are required and mark them as required.

// CreateUserRequest represents a new request to create new user.
// CreateUserRequest can be called concurrently using multiple goroutines.
type CreateUserRequest struct {
	Email           string `json:"email" binding:"required"`
	Password        string `json:"password" binding:"required"`
	RetypedPassword string `json:"retyped_password" binding:"required"`
}

// NewCreateUserRequest returns a new instance of CreateUserRequest type.
func NewCreateUserRequest() *CreateUserRequest {
	return &CreateUserRequest{}
}

// CreateListingRequest represens a new request to create new lising.
// CreateListingRequest can be called concurrently using multiple goroutines.
type CreateListingRequest struct {
	Title                  string   `bson:"title" json:"title"`
	Description            string   `bson:"description" json:"description"`
	PropertyAddress        string   `bson:"propertyAddress" json:"property_address"`
	Zip                    string   `bson:"zip" json:"zip"`
	RentAmount             string   `bson:"rentAmount" json:"rent_amount"`
	BondAmount             string   `bson:"bondAmount" json:"bond_amount"`
	PropertyType           string   `bson:"propertyType" json:"property_type"`
	ListingType            string   `bson:"listingType" json:"listing_type"`
	CurrentNumberOfTenants int      `bson:"currentNoOfTenants" json:"current_no_of_tenants"`
	ImageUrls              []string `bson:"imageUrls" json:"image_urls"`
	Author                 string   `bson:"author" json:"author"`
	ContactName            string   `bson:"contactName" json:"contact_name"`
	ContactEmail           string   `bson:"contactEmail" json:"contact_email"`
	ContactPhoneNumber     string   `bson:"contactPhoneNumber" json:"contact_phone_number"`
	IsIncludeBills         bool     `bson:"isIncludeBills" json:"is_include_bills"`
	AvailableFrom          string   `bson:"availableFrom" json:"available_from"`
	PreferredTenant        string   `bson:"preferredTenant" json:"preferred_tenant"`
	ContactMethod          string   `bson:"contactMethod" json:"contact_method"`
	NumberOfBathRooms      int      `bson:"noOfBathRooms" json:"no_of_bath_rooms"`
	NumberOfBedRooms       int      `bson:"noOfBedRooms" json:"no_of_bed_rooms"`
	NumberOfParking        int      `bson:"numberOfParking" json:"no_of_parking_space"`
	FloorType              string   `bson:"floorType" json:"floor_type" `
	NoticePeriodInWeeks    int      `bson:"noticePeriodInWeeks" json:"notice_period_in_weeks"`
	ListingStatus          string   `bson:"listingStatus" json:"listing_status"`
	AdditionalFeatures     []string `bson:"additionalFeatures" json:"additional_features"`
}

// NewCreateListingRequest returns a new CreateListingRequest type
func NewCreateListingRequest() *CreateListingRequest {
	return &CreateListingRequest{}
}

// LoginUserRequest represents a new request to login.
type LoginUserRequest struct {
	Email    string `json:"email" binding:"required,email"`
	Password string `json:"password" binding:"required"`
}

// NewLoginUserRequest retuns a new NewLoginUserRequest type
func NewLoginUserRequest() *LoginUserRequest {
	return &LoginUserRequest{}
}

// OTPRequest represents a request to generate new OTP.
type OTPRequest struct {
	Email string `json:"email" binding:"required,email"`
}

// PasswordRequestEmail represents request to reset user password.
type PasswordRequestEmail struct {
	Email string `json:"email" binding:"required,email"`
}

// OTPValidateRequest represents a request used to validate OTP.
type OTPValidateRequest struct {
	// The OTP sent to the user email
	OTP string `json:"otp" binding:"required"`
	// A 16 to 40 character long string with random unique data
	Nonce string `json:"nonce" binding:"required"`
	// transaction id
	ID string `json:"id" binding:"required"`
}

// ChangePasswordRequest is used reset password for authenticated users.
type ChangePasswordRequest struct {
	Old string `json:"old" binding:"required"`
	New string `json:"new" binding:"required"`
}

// NewChangePasswordRequest returns a new ChangePasswordRequest
func NewChangePasswordRequest() *ChangePasswordRequest {
	return &ChangePasswordRequest{}
}

// UpdateListingRequest represents a request to update individual listing
type UpdateListingRequest struct {
	Title                  string   `bson:"title" json:"title"`
	Description            string   `bson:"description" json:"description"`
	PropertyAddress        string   `bson:"propertyAddress" json:"property_address"`
	Zip                    string   `bson:"zip" json:"zip"`
	RentAmount             string   `bson:"rentAmount" json:"rent_amount"`
	BondAmount             string   `bson:"bondAmount" json:"bond_amount"`
	PropertyType           string   `bson:"propertyType" json:"property_type"`
	ListingType            string   `bson:"listingType" json:"listing_type"`
	CurrentNumberOfTenants int      `bson:"currentNoOfTenants" json:"current_no_of_tenants"`
	ImageUrls              []string `bson:"imageUrls" json:"image_urls"`
	ContactName            string   `bson:"contactName" json:"contact_name"`
	ContactEmail           string   `bson:"contactEmail" json:"contact_email"`
	ContactPhoneNumber     string   `bson:"contactPhoneNumber" json:"contact_phone_number"`
	IsIncludeBills         bool     `bson:"isIncludeBills" json:"is_include_bills"`
	AvailableFrom          string   `bson:"availableFrom" json:"available_from"`
	PreferredTenant        string   `bson:"preferredTenant" json:"preferred_tenant"`
	ContactMethod          string   `bson:"contactMethod" json:"contact_method"`
	NumberOfBathRooms      int      `bson:"noOfBathRooms" json:"no_of_bath_rooms"`
	NumberOfBedRooms       int      `bson:"noOfBedRooms" json:"no_of_bed_rooms"`
	NumberOfParking        int      `bson:"numberOfParking" json:"no_of_parking_space"`
	FloorType              string   `bson:"floorType" json:"floor_type" `
	NoticePeriodInWeeks    int      `bson:"noticePeriodInWeeks" json:"notice_period_in_weeks"`
	ListingStatus          string   `bson:"listingStatus" json:"listing_status"`
	AdditionalFeatures     []string `bson:"additionalFeatures" json:"additional_features"`
}

// NewUpdateListingRequest returns a new UpdateListingRequest type.
func NewUpdateListingRequest() *UpdateListingRequest {
	return &UpdateListingRequest{}
}

// UpdateUserRequest is used to update user account
// UpdateUserRequest cannot be used concurrently in multiple goroutine.
type UpdateUserRequest struct {
	FirstName   string `bson:"firstName" json:"first_name"`
	LastName    string `bson:"lastName" json:"last_name"`
	Email       string `bson:"email" json:"email"`
	PhoneNumber string `bson:"phoneNumber" json:"phone_number"`
	Address     string `bson:"address" json:"address"`
	AvatarURL   string `bson:"avatarUrl" json:"avatar_url"`
}

// NewUpdateUserRequest returns a new UpdateUserRequest type
func NewUpdateUserRequest() *UpdateUserRequest {
	return &UpdateUserRequest{}
}

// ForgetPasswordRequest is used to send password reset email link for unauthenticated users
type ForgetPasswordRequest struct {
	Email string `json:"email" binding:"required,email"`
}

// NewForgetPasswordRequest returns a new ForgetPasswordRequest
func NewForgetPasswordRequest() *ForgetPasswordRequest {
	return &ForgetPasswordRequest{}
}

// ResetPasswordRequest represents a request to change password for unauthenticated users.
type ResetPasswordRequest struct {
	Token       string `json:"token" binding:"required"`
	Email       string `json:"email" binding:"required,email"`
	NewPassword string `json:"new" binding:"required"`
}

// NewResetPasswordRequest returns a new ResetPasswordRequest
func NewResetPasswordRequest() *ResetPasswordRequest {
	return &ResetPasswordRequest{}
}
```