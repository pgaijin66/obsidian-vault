```
func myHandler(c *gin.Context) {
    // Retrieve the "page" and "page_size" query parameters from the request
    page := c.Query("page")
    pageSize := c.Query("page_size")
    // Convert the "page" and "page_size" parameters to integers
    pageInt, err := strconv.Atoi(page)
    if err != nil {
        // Handle the error here (e.g. return a default value)
    }
    pageSizeInt, err := strconv.Atoi(pageSize)
    if err != nil {
        // Handle the error here (e.g. return a default value)
    }
    // Calculate the offset and limit
    offset := (pageInt - 1) * pageSizeInt
    limit := pageSizeInt
    // Retrieve the relevant data using the offset and limit
    // (e.g. using a database query with OFFSET and LIMIT)
    // ...
    // Return the paged data to the client
    c.JSON(http.StatusOK, data)
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