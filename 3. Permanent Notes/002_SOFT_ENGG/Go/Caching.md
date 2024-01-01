```
var cache = make(map[string]interface{})
func myHandler(c *gin.Context) {
    // Retrieve the key from the request
    key := c.Query("key")
    // Check if the key exists in the cache
    if value, ok := cache[key]; ok {
        // Return the value from the cache
        c.JSON(http.StatusOK, value)
        return
    }
    // Retrieve the value from the data source
    // (e.g. using a database query)
    // ...
    // Save the value in the cache
    cache[key] = value
    // Return the value to the client
    c.JSON(http.StatusOK, value)
}
func main() {
    // Initialize the Gin router
    r := gin.Default()
    // Define the route
    r.GET("/my-route", myHandler)
    // Start the web server
    r.Run()
}
```