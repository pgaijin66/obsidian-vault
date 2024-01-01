
```
import (
	"crypto/rand"
	"encoding/base64"
	"io"
)

// GetSessionID returns a new random sessionID.
func GetSessionID() string {
	newByte := make([]byte, 32)

	if _, err := io.ReadFull(rand.Reader, newByte); err != nil {
		return ""
	}

	// encode to fixed lenght
	return base64.URLEncoding.EncodeToString(newByte)
}
```