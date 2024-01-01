
```
package main
import (
	"github.com/gin-gonic/gin"
)
func main() {
	// Create a new Gin router
	r := gin.New()
	// Use the gin.CheckCSRF middleware on the router
	r.Use(gin.CheckCSRF())
	// Define a route that generates a CSRF token and sets it in the response
	r.GET("/", func(c *gin.Context) {
		// Generate a CSRF token
		token := c.MustGet("csrf").(string)
		// Set the token in the response
		c.Set("csrf", token)
		// Render the HTML template
		c.HTML(http.StatusOK, "index.tmpl", gin.H{
			"csrf": token,
		})
	})
	// Start the server
	r.Run(":8080")
}
```