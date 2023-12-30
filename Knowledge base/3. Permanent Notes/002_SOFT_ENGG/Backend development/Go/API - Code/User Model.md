
```
package model

import (
	"time"

	"go.mongodb.org/mongo-driver/bson/primitive"
)

// User stroes the details for individual user
type User struct {
	ID                primitive.ObjectID `bson:"_id" json:"id"`
	FirstName         string             `bson:"firstName" json:"first_name"`
	LastName          string             `bson:"lastName" json:"last_name"`
	Email             string             `bson:"email" json:"email"`
	PhoneNumber       string             `bson:"phoneNumber" json:"phone_number"`
	Address           string             `bson:"address" json:"address"`
	HashedPassword    string             `bson:"hashedPassword" json:"-"`
	AvatarURL         string             `bson:"avatarUrl" json:"avatar_url"`
	HasRole           int                `bson:"hasRole" json:"-"`
	IsEmailVerified   bool               `bson:"isEmailVerified" json:"is_verified"`
	IsAccountDisabled bool               `bson:"isAccountDisabled" json:"-"`
	LastLogin         time.Time          `bson:"lastLogin" json:"last_login"`
	UpdatedAt         time.Time          `bson:"updatedAt" json:"updated_at"`
	CreatedAt         time.Time          `bson:"createdAt" json:"created_at"`
}

// NewUser returns
func NewUser() *User {
	return &User{}
}

// Role defines the role of each user
type Role int8

const (
	Author Role = iota
	Viewer
	Admin
)

func (r Role) String() string {
	switch r {
	case Author:
		return "AUTHOR"
	case Viewer:
		return "VIEWER"
	case Admin:
		return "	"
	default:
		return ""
	}
}
```