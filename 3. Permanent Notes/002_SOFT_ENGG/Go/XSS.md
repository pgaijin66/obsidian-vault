
```
package main
import (
	"github.com/gin-gonic/gin"
)
func main() {
	// Create a new Gin router
	r := gin.New()
	// Use the gin.SanitizePath middleware on the router
	r.Use(gin.SanitizePath())
	// Define a route that is protected against XSS attacks
	r.GET("/:param", func(c *gin.Context) {
		// The path parameter will be automatically sanitized by the
		// gin.SanitizePath middleware, so it is safe to use in your code
		param := c.Param("param")
		// Handle the request here
	})
	// Start the server
	r.Run(":8080")
}
```