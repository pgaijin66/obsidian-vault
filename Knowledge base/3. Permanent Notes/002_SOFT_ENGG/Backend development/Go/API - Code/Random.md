
```
package random

import (
	"crypto/rand"
	"encoding/base64"
	"fmt"
)

// RandStringBytes generates slice of random bytes. It takes size as an input. The size specifies the length and capacity of the slice.
func RandStringBytes(size int) ([]byte, error) {
	s := make([]byte, size)

	_, err := rand.Read(s)
	if err != nil {
		return nil, fmt.Errorf("could not read random bytes: %v", err)
	}

	return s, nil
}

// GenerateRandomString generates a random string as per the length provided.
func GenerateRandomString(length int) (string, error) {
	b, err := RandStringBytes(length)
	if err != nil {
		return "", fmt.Errorf("could not generate random bytes: %v", err)
	}

	// Note that the length of the resulting string is not guaranteed to be exactly 12 characters,
	// since the base64 encoding can add padding characters (equals signs) at the end of the string.
	// Hence, we only take the first chars equal to the passed length from the output to generate random string of fixed
	// length
	s := base64.RawURLEncoding.EncodeToString(b)[:length]

	return s, nil
}
```