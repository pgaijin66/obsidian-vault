

maker.go

```
package token

import "time"

// Maker is an interface for managing tokens
type Maker interface {
	// CreateToken creates a new token for a specific username and duration
	CreateToken(email string, duration time.Duration) (string, time.Time, error)

	// VerifyToken checks if the input token is valid or not
	VerifyToken(token string) (*Payload, error)
}
```


payload.go

```
package token

import (
	"errors"
	"fmt"
	"time"

	"github.com/google/uuid"
)

// Different types of errors returned by the VerifyToken function
var (
	ErrInvalidToken = errors.New("well, well, well, looks like your token took a vacation in the land of invalidity")
	ErrExpiredToken = errors.New("that's an expired access token, or you just made it up")
)

// Payload contains the payload data of the token
type Payload struct {
	ID        uuid.UUID `json:"id"`
	Email     string    `json:"email"`
	IssuedAt  time.Time `json:"issued_at"`
	ExpiredAt time.Time `json:"expired_at"`
}

// NewPayload creates a new token payload which specific username and duration
func NewPayload(email string, duration time.Duration) (*Payload, time.Time, error) {
	tokenID, err := uuid.NewRandom()
	if err != nil {
		return nil, time.Now(), fmt.Errorf("could not create a new UUID: %v", err)
	}

	payload := &Payload{
		ID:        tokenID,
		Email:     email,
		IssuedAt:  time.Now(),
		ExpiredAt: time.Now().Add(duration),
	}

	expiresAt := time.Now().Add(duration)
	return payload, expiresAt, nil
}

func (payload *Payload) Valid() error {
	if time.Now().After(payload.ExpiredAt) {
		return ErrExpiredToken
	}
	return nil
}
```


jwt_maker.go

```
package token

import (
	"errors"
	"fmt"
	"time"

	"github.com/golang-jwt/jwt/v4"
)

const (
	minSecretKeySize = 32
)

// JWTMaker is a JSON web token maker
type JWTMaker struct {
	secretKey string
}

// NewJWTMaker creates a new JWTMaker
func NewJWTMaker(secretKey string) (Maker, error) {
	if len(secretKey) < minSecretKeySize {
		return nil, fmt.Errorf("invalid key size: must be atleast %d characters", minSecretKeySize)
	}

	return &JWTMaker{secretKey}, nil
}

// CreateToken creates a new token for a specific username and duration
func (maker *JWTMaker) CreateToken(email string, duration time.Duration) (string, time.Time, error) {
	payload, expiresAt, err := NewPayload(email, duration)
	if err != nil {
		return "", time.Now(), fmt.Errorf("coudl not create new payload: %v", err)
	}

	jwtToken := jwt.NewWithClaims(jwt.SigningMethodHS256, payload)
	signedJWTToken, err := jwtToken.SignedString([]byte(maker.secretKey))
	return signedJWTToken, expiresAt, err
}

// VerifyToken checks if the input token is valid or not
func (maker *JWTMaker) VerifyToken(token string) (*Payload, error) {
	KeyFunc := func(token *jwt.Token) (interface{}, error) {
		_, ok := token.Method.(*jwt.SigningMethodHMAC)
		if !ok {
			return nil, ErrInvalidToken
		}
		return []byte(maker.secretKey), nil
	}
	jwtToken, err := jwt.ParseWithClaims(token, &Payload{}, KeyFunc)
	if err != nil {
		verr, ok := err.(*jwt.ValidationError)
		if ok && errors.Is(verr.Inner, ErrExpiredToken) {
			return nil, ErrExpiredToken
		}
		return nil, ErrInvalidToken
	}

	payload, ok := jwtToken.Claims.(*Payload)
	if !ok {
		return nil, ErrInvalidToken
	}

	return payload, nil
}
```


paseto_maker.go

```
package token

import (
	"fmt"
	"time"

	"github.com/aead/chacha20poly1305"
	"github.com/o1egl/paseto"
)

// PasetoMaker is a PASETO token maker
type PasetoMaker struct {
	paseto       *paseto.V2
	symmetricKey []byte
}

// NewPasetoMaker creates a new PasetoMaker
func NewPasetoMaker(symmetricKey string) (Maker, error) {
	if len(symmetricKey) != chacha20poly1305.KeySize {
		return nil, fmt.Errorf("invalid key size: must be exactly %d characters", chacha20poly1305.KeySize)
	}

	maker := &PasetoMaker{
		paseto:       paseto.NewV2(),
		symmetricKey: []byte(symmetricKey),
	}
	return maker, nil
}

// CreateToken creates a new token for a specific username and duration
func (maker *PasetoMaker) CreateToken(email string, duration time.Duration) (string, time.Time, error) {
	payload, expiresAt, err := NewPayload(email, duration)
	if err != nil {
		return "", time.Now(), fmt.Errorf("coudl not create new payload: %v", err)
	}
	// TODO: handle error
	token, _ := maker.paseto.Encrypt(maker.symmetricKey, payload, nil)
	return token, expiresAt, err
}

// VerifyToken checks if the input token is valid or not
func (maker *PasetoMaker) VerifyToken(token string) (*Payload, error) {
	payload := &Payload{}

	err := maker.paseto.Decrypt(token, maker.symmetricKey, payload, nil)
	if err != nil {
		return nil, ErrInvalidToken
	}

	err = payload.Valid()
	if err != nil {
		return nil, err
	}

	return payload, nil
}
```


in server.go

```
tokenMaker, err := token.NewPasetoMaker(conf.Token.SymmetricKey)
	if err != nil {
		return nil, fmt.Errorf("%v : %v", errors.ErrCouldNotCreateTokenMaker.Message(), err)
	}
```