
```
package utils

import (
	"bytes"
	"crypto/hmac"
	"crypto/rand"
	"crypto/sha256"
	"encoding/base64"
	"encoding/hex"
	"fmt"
	"html/template"
	"log"
	"strconv"
	"strings"
	"time"

	"github.com/gin-gonic/gin"
	"github.com/google/uuid"
	"golang.org/x/text/cases"
	"golang.org/x/text/language"
)

// ToBool takes string as an input and converts it into bool. It returns a bool.
func ToBool(str string) bool {
	boolValue, err := strconv.ParseBool(str)
	if err != nil {
		log.Fatal(err)
	}
	return boolValue
}

// This OTP chars should not contain '0' as leading 0 will be understood in whole number and be ignored
const otpChars = "123456789"

// GenerateOTP generate a new OTP number as string. It takes length as input.
func GenerateOTP(length int) (string, error) {
	buffer := make([]byte, length)
	_, err := rand.Read(buffer)
	if err != nil {
		return "", err
	}

	otpCharsLength := len(otpChars)
	for i := 0; i < length; i++ {
		buffer[i] = otpChars[int(buffer[i])%otpCharsLength]
	}

	return string(buffer), nil
}

// ParseTemplate parses html template file and return a string buffer.
func ParseTemplate(templateFileName string, data interface{}) (string, error) {
	t, err := template.ParseFiles(templateFileName)
	if err != nil {
		return "", err
	}
	buf := new(bytes.Buffer)
	if err = t.Execute(buf, data); err != nil {
		return "", err
	}
	return buf.String(), nil
}

// GetDurationInMillseconds takes a start time and returns a duration in milliseconds
func GetDurationInMillseconds(start time.Time) float64 {
	end := time.Now()
	duration := end.Sub(start)
	milliseconds := float64(duration) / float64(time.Millisecond)
	rounded := float64(int(milliseconds*100+.5)) / 100
	return rounded
}

// GetClientIP gets the correct IP for the end client instead of the proxy
func GetClientIP(c *gin.Context) string {
	// first check the X-Forwarded-For header
	requester := c.Request.Header.Get("X-Forwarded-For")
	// if empty, check the Real-IP header
	if len(requester) == 0 {
		requester = c.Request.Header.Get("X-Real-IP")
	}
	// if the requester is still empty, use the hard-coded address from the socket
	if len(requester) == 0 {
		requester = c.Request.RemoteAddr
	}

	// if requester is a comma delimited list, take the first one
	// (this happens when proxied via elastic load balancer then again through nginx)
	if strings.Contains(requester, ",") {
		requester = strings.Split(requester, ",")[0]
	}

	return requester
}

// GetUserID gets the current_user ID as a string
func GetUserID(c *gin.Context) string {
	userID, exists := c.Get("userID")
	if exists {
		return userID.(string)
	}
	return ""
}

// RandToken returns a random string token.
func RandToken() (string, error) {
	// Using UUID V5 for generating the Token
	id := uuid.New()
	UUIDtoken := id.String()
	return UUIDtoken, nil
}

// GenerateSignature returns a new HMAC string. It takes secretToken and payloadBody as input.
func GenerateSignature(secretToken, payloadBody string) string {
	// TODO: Research more on HMAC-SHA256 vs HMAC-SHA1
	// While SHA1 is faster than SHA256 but it is succeptible to security
	// attacks. For now we will keep using SHA256 for unless there is an explicit
	// requirement to do so or significant performance impact.
	mac := hmac.New(sha256.New, []byte(secretToken))
	mac.Write([]byte(payloadBody))
	expectedMAC := mac.Sum(nil)
	return "sha256=" + hex.EncodeToString(expectedMAC)
}

// func verifySignature(secretToken, payloadBody string, signatureToCompareWith string) bool {
// 	signature := GenerateSignature(secretToken, payloadBody)
// 	return subtle.ConstantTimeCompare([]byte(signature), []byte(signatureToCompareWith)) == 1
// }

// GenerateNonce returns a nonce string. It takes length as an argument.
func GenerateNonce(length int) (string, error) {
	nonceBytes := make([]byte, length)
	_, err := rand.Read(nonceBytes)
	if err != nil {
		return "", fmt.Errorf("could not generate nonce")
	}

	return base64.URLEncoding.EncodeToString(nonceBytes), nil
}

// GenerateHMAC generated HMAC value from data. It returns a random HMAC string.
func GenerateHMAC(data string, hmacSecret string) string {
	h := hmac.New(sha256.New, []byte(hmacSecret))
	h.Write([]byte(data))
	sha := hex.EncodeToString(h.Sum(nil))
	return sha
}

// IsValidHMAC compares two HMAC values and returns bool
func IsValidHMAC(currentHMAC, expectedHMAC []byte) bool {
	return hmac.Equal(currentHMAC, expectedHMAC)
}

// IsEqual compares two string
func IsEqual(str1, str2 string) bool {
	return str1 == str2
}

// ToLowerCase converts any string to lowercase
func ToLowerCase(s string) string {
	return strings.ToLower(s)
}

func ToTitleCase(input string) string {
	// Create a new title case
	titleCase := cases.Title(language.English)

	// Convert the first letter to uppercase and the rest to lowercase
	sanitizedFirstName := titleCase.String(input)

	return sanitizedFirstName
}
```