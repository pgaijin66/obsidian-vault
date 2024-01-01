



access token valid 15 min  
refresh token 24 hrif access token expired then refresh token is sent to that enpoint to get new access tokengenerate both againpically for JWTs you'll have an access token, that's valid for ~15 minutes, and a refresh token that is valid for longer (e.g. 24 hours).To access API end points, the browser sends only the access token. If it receives a 401 HTTP status, it then refreshes it's token by submitting the refresh token to a specified end point, retrieves two new tokens (both refresh and access) and continues along.Here's the wonderful thing. Both tokens have expiry dates, both tokens are signed -- hence you should always validate all tokens (regardless of access or refresh) using the same logic, before performing token specific validation.


```
#!/bin/bash# Set the API endpoint URL  
API_ENDPOINT="[https://api.example.com](https://api.example.com/)"# Set the maximum number of retries  
MAX_RETRIES=5# Set the initial number of retries  
NUM_RETRIES=0# Set a flag to indicate whether the API call was successful  
SUCCESS=0# Call the API in a loop, retrying up to MAX_RETRIES times  
while [ $SUCCESS -eq 0 ] && [ $NUM_RETRIES -lt $MAX_RETRIES ]; do  
  # Call the API  
  response=$(curl "$API_ENDPOINT")  # Check the HTTP status code of the response  
  if [ $response -eq 200 ]; then  
    # Set the SUCCESS flag if the status code is 200 (OK)  
    SUCCESS=1  
  else  
    # Increment the number of retries  
    NUM_RETRIES=$((NUM_RETRIES+1))    # Wait for 2 seconds before retrying  
    sleep 2  
  fi  
done# Check if the API call was successful  
if [ $SUCCESS -eq 1 ]; then  
  # Handle the successful API response  
  echo "API call was successful: $response"  
else  
  # Handle the failed API response  
  echo "API call failed after $MAX_RETRIES retries"  
fi
```


```
`#!/bin/bash``# Set the maximum number of retries`  
`MAX_RETRIES=5``# Set the initial number of retries`  
`NUM_RETRIES=0``# Set a flag to indicate whether the API call was successful`  
`SUCCESS=0``# Define a function to make an API call`  
`make_api_call() {`  
`# Call the API`  
`response=$(curl "$1")` `# Check the HTTP status code of the response`  
`if [ $response -eq 200 ]; then`  
`# Set the SUCCESS flag if the status code is 200 (OK)`  
`SUCCESS=1`  
`else`  
`# Increment the number of retries`  
`NUM_RETRIES=$((NUM_RETRIES+1))` `# Wait for 2 seconds before retrying`  
`sleep 2`  
`fi`  
`}``# Define a function to handle a successful API response`  
`handle_success() {`  
`# Print a success message`  
`echo "API call was successful: $response"`  
`}``# Define a function to handle a failed API response`  
`handle_failure() {`  
`# Print an error message`  
`echo "API call failed after $MAX_RETRIES retries"`  
`}``# Call the API in a loop, retrying up to MAX_RETRIES times`  
`while [ $SUCCESS -eq 0 ] && [ $NUM_RETRIES -lt $MAX_RETRIES ]; do`  
`# Call the make_api_call function with the API endpoint URL as an argument`  
`make_api_call "https://api.example.com"`  
`done``# Check if the API call was successful`  
`if [ $SUCCESS -eq 1 ]; then`  
`# Call the handle_success function`  
`handle_success`  
`else`  
`# Call the handle_failure function`  
`handle_failure`  
`fi`
```


```
`#!/bin/bash``# Set the umask to 077 to prevent any created files from being world-readable`  
`umask 077``# Use the readonly command to make important variables and functions read-only`  
`readonly API_KEY="secret-api-key"`  
`readonly SALT="random-string"``# Use the set -o nounset option to prevent the script from using unset variables`  
`set -o nounset``# Use the set -o errexit option to exit immediately if a command returns a non-zero exit status`  
`set -o errexit``# Use the set -o pipefail option to cause a pipeline to fail if any of the commands in the pipeline return a non-zero exit status`  
`set -o pipefail``# Use the set -o noclobber option to prevent redirecting output from overwriting existing files`  
`set -o noclobber``# Use the set -o noglob option to prevent file globbing from occurring`  
`set -o noglob``# Use the set -o histexpand option to prevent history expansion from occurring`  
`set -o histexpand``# Use the set -o xtrace option to enable the script to print each command before it is executed (for debugging purposes)`  
`# set -o xtrace``# Define a function that makes an API call using the API_KEY variable and the SALT variable`  
`make_api_call() {`  
`curl "https://api.example.com/endpoint?api_key=$API_KEY&salt=$SALT"`  
`}``# Call the make_api_call function`  
`make_api_call`
```


```
There are several different backup techniques that can be used, and the appropriate technique to use will depend on the specific needs of the system and the data being backed up. Some common backup techniques include:Full backup: A full backup is a complete copy of all data in the system. It typically includes all files, folders, and settings, and can be used to restore the system to its original state. This technique is useful for creating a complete snapshot of the system and can be used to recover from data loss or corruption.  
Incremental backup: An incremental backup only includes data that has changed since the last full or incremental backup. This technique is more efficient than a full backup, but it requires the previous full or incremental backup in order to restore the system. Incremental backups are useful for regularly backing up data that changes frequently, such as databases or user files.  
Differential backup: A differential backup includes all data that has changed since the last full backup. This technique is more efficient than a full backup, but it requires the previous full backup in order to restore the system. Differential backups are useful for regularly backing up data that changes frequently, but less so than with incremental backups.  
Mirror backup: A mirror backup, also known as a disk-image backup, creates an exact copy of the system's hard drive, including all files, folders, and settings. This technique can be used to restore the system to its exact state at the time the backup was made, and is useful for creating a complete snapshot of the system.  
Cloud backup: A cloud backup is a type of backup that uses cloud storage to store data. This technique allows the data to be accessed from any location and protects against data loss due to local disasters. Cloud backups are useful for protecting data against disasters and for making data accessible from anywhere.  
In general, it is recommended to use a combination of different backup techniques in order to ensure that data is protected against various types of failures and disasters. For example, you might use a full backup to create a complete snapshot of the system, and then use incremental backups to regularly back up data that changes frequently. You might also use a cloud backup to protect against data loss due to local disasters.
```


pagination

```
`func myHandler(c *gin.Context) {`  
`// Retrieve the "page" and "page_size" query parameters from the request`  
`page := c.Query("page")`  
`pageSize := c.Query("page_size")` `// Convert the "page" and "page_size" parameters to integers`  
`pageInt, err := strconv.Atoi(page)`  
`if err != nil {`  
`// Handle the error here (e.g. return a default value)`  
`}`  
`pageSizeInt, err := strconv.Atoi(pageSize)`  
`if err != nil {`  
`// Handle the error here (e.g. return a default value)`  
`}` `// Calculate the offset and limit`  
`offset := (pageInt - 1) * pageSizeInt`  
`limit := pageSizeInt` `// Retrieve the relevant data using the offset and limit`  
`// (e.g. using a database query with OFFSET and LIMIT)`  
`// ...` `// Return the paged data to the client`  
`c.JSON(http.StatusOK, data)`  
`}``func main() {`  
`// Initialize the Gin router`  
`r := gin.Default()` `// Define the route`  
`r.GET("/my-route", myHandler)` `// Start the web server`  
`r.Run()`  
`}`
```


otp
```
`func sendOTP(c *gin.Context) {`  
`// Retrieve the user's phone number from the request`  
`phoneNumber := c.Query("phone_number")` `// Generate a random 6-digit OTP`  
`otp := rand.Intn(1000000)` `// Save the OTP in Firebase for later verification`  
`// (e.g. using the Firebase Realtime Database or Cloud Firestore)`  
`// ...` `// Send the OTP to the user's phone number using Firebase Cloud Messaging`  
`// (e.g. using the Firebase Admin SDK)`  
`// ...` `// Return a success message to the client`  
`c.JSON(http.StatusOK, gin.H{"message": "OTP sent successfully"})`  
`}``func main() {`  
`// Initialize the Gin router`  
`r := gin.Default()` `// Define the route`  
`r.GET("/send-otp", sendOTP)` `// Start the web server`  
`r.Run()`  
`}`
```

sorting

```
func myHandler(c *gin.Context) {  
    // Retrieve the "sort" and "order" query parameters from the request  
    sort := c.Query("sort")  
    order := c.Query("order")    // Sort the data in ascending or descending order,  
    // depending on the value of the "order" parameter  
    if order == "asc" {  
        // Sort the data in ascending order  
        // (e.g. using Go's built-in "sort" package)  
        // ...  
    } else if order == "desc" {  
        // Sort the data in descending order  
        // (e.g. using Go's built-in "sort" package)  
        // ...  
    } else {  
        // Return an error message if the "order" parameter is not recognized  
        c.JSON(http.StatusBadRequest, gin.H{"error": "Invalid 'order' parameter"})  
        return  
    }    // Return the sorted data to the client  
    c.JSON(http.StatusOK, data)  
}func main() {  
    // Initialize the Gin router  
    r := gin.Default()    // Define the route  
    r.GET("/my-route", myHandler)    // Start the web server  
    r.Run()  
}
```


```
`var cache = make(map[string]interface{})``func myHandler(c *gin.Context) {`  
`// Retrieve the key from the request`  
`key := c.Query("key")` `// Check if the key exists in the cache`  
`if value, ok := cache[key]; ok {`  
`// Return the value from the cache`  
`c.JSON(http.StatusOK, value)`  
`return`  
`}` `// Retrieve the value from the data source`  
`// (e.g. using a database query)`  
`// ...` `// Save the value in the cache`  
`cache[key] = value` `// Return the value to the client`  
`c.JSON(http.StatusOK, value)`  
`}``func main() {`  
`// Initialize the Gin router`  
`r := gin.Default()` `// Define the route`  
`r.GET("/my-route", myHandler)` `// Start the web server`  
`r.Run()`  
`}`
```

```
`version: '3.7'`  
`services:`  
`elasticsearch:`  
`image: elasticsearch:latest`  
`environment:`  

-   `discovery.type=single-node`

`ports:`  

-   `"9200:9200"`

`volumes:`  

-   `elasticsearch_data:/usr/share/elasticsearch/data`

`restart: unless-stopped`  
`logstash:`  
`image: logstash:latest`  
`volumes:`  

-   `./logstash/pipeline:/usr/share/logstash/pipeline`
-   `logstash_data:/usr/share/logstash/data`

`ports:`  

-   `"5000:5000"`

`restart: unless-stopped`  
`kibana:`  
`image: kibana:latest`  
`ports:`  

-   `"5601:5601"`

`environment:`  

-   `ELASTICSEARCH_HOSTS=http://elasticsearch:9200`

`restart: unless-stopped`  
`volumes:`  
`elasticsearch_data:`  
`logstash_data:`
```




refresh token
```
`package main``import (`  
`"net/http"`  
`"strings"` `"github.com/gin-gonic/gin"`  
`)``// RefreshTokenMiddleware is a middleware that checks for the presence of`  
`// a refresh token in the request header and, if it exists, refreshes the`  
`// access token and adds it to the response header`  
`func RefreshTokenMiddleware() gin.HandlerFunc {`  
`return func(c *gin.Context) {`  
`// Check if the refresh token exists in the request header`  
`refreshToken := c.GetHeader("Refresh-Token")`  
`if refreshToken == "" {`  
`// If not, continue with the request`  
`c.Next()`  
`return`  
`}` `// If the refresh token exists, refresh the access token`  
`accessToken, err := refreshAccessToken(refreshToken)`  
`if err != nil {`  
`// If there is an error refreshing the access token,`  
`// return a 401 Unauthorized response`  
`c.AbortWithStatus(http.StatusUnauthorized)`  
`return`  
`}` `// Add the refreshed access token to the response header`  
`c.Header("Access-Token", accessToken)`  
`c.Next()`  
`}`  
`}``// refreshAccessToken is a placeholder function that refreshes the access`  
`// token using the provided refresh token. In a real application, this`  
`// function would call an authentication service to refresh the access token.`  
`func refreshAccessToken(refreshToken string) (string, error) {`  
`// This is just a placeholder implementation that returns a hard-coded`  
`// access token. In a real application, this function would call an`  
`// authentication service to refresh the access token.`  
`return "refreshed_access_token", nil`  
`}``func main() {`  
`// Create a new Gin router`  
`r := gin.New()` `// Use the RefreshTokenMiddleware as a middleware on the router`  
`r.Use(RefreshTokenMiddleware())` `// Define a route that requires an access token`  
`r.GET("/", func(c *gin.Context) {`  
`// Check if the access token exists in the request header`  
`accessToken := c.GetHeader("Access-Token")`  
`if accessToken == "" {`  
`// If not, return a 401 Unauthorized response`  
`c.AbortWithStatus(http.StatusUnauthorized)`  
`return`  
`}` `// If the access token exists, return a 200 OK response`  
`c.String(http.StatusOK, "OK")`  
`})` `// Start the server`  
`r.Run(":8080")`  
`}`
```


csrf

```
`package main``import (`  
`"github.com/gin-gonic/gin"`  
`)``func main() {`  
`// Create a new Gin router`  
`r := gin.New()` `// Use the gin.CheckCSRF middleware on the router`  
`r.Use(gin.CheckCSRF())` `// Define a route that generates a CSRF token and sets it in the response`  
`r.GET("/", func(c *gin.Context) {`  
`// Generate a CSRF token`  
`token := c.MustGet("csrf").(string)` `// Set the token in the response`  
`c.Set("csrf", token)` `// Render the HTML template`  
`c.HTML(http.StatusOK, "index.tmpl", gin.H{`  
`"csrf": token,`  
`})`  
`})` `// Start the server`  
`r.Run(":8080")`  
`}`
```


```
To protect against CSRF (Cross-Site Request Forgery) attacks in a Golang Gin application, you can use the gin.CheckCSRF middleware. This middleware automatically checks the CSRF token in requests and returns a 400 Bad Request response if the token is missing or invalid.Here is an example of how to use the gin.CheckCSRF middleware in a Gin application:Copy code  
package mainimport (  
"[github.com/gin-gonic/gin](http://github.com/gin-gonic/gin)"  
)func main() {  
// Create a new Gin router  
r := gin.New() // Use the gin.CheckCSRF middleware on the router  
r.Use(gin.CheckCSRF()) // Define a route that requires a valid CSRF token  
r.POST("/", func(c *gin.Context) {  
// If the CSRF token is valid, the request will be handled here  
// If the token is missing or invalid, a 400 Bad Request response  
// will be automatically returned by the gin.CheckCSRF middleware  
}) // Start the server  
r.Run(":8080")  
}  
In this example, the gin.CheckCSRF middleware is used on the Gin router. This middleware will automatically check the CSRF token in any POST requests made to the server and return a 400 Bad Request response if the token is missing or invalid.To generate a CSRF token in a Gin application, you can use the c.Set method in a Gin handler and the c.Get method in your HTML templates to set and retrieve the token:Copy code  

package main

import (  
"[github.com/gin-gonic/gin](http://github.com/gin-gonic/gin)"  
)

func main() {  
// Create a new Gin router  
r := gin.New() // Use the gin.CheckCSRF middleware on the router  
r.Use(gin.CheckCSRF()) // Define a route that generates a CSRF token and sets it in the response  
r.GET("/", func(c *gin.Context) {  
// Generate a CSRF token  
token := c.MustGet("csrf").(string) // Set the token in the response  
c.Set("csrf", token) // Render the HTML template  
c.HTML(http.StatusOK, "index.tmpl", gin.H{  
"csrf": token,  
})  
}) // Start the server  
r.Run(":8080")  
}  

In this example, the / route generates a CSRF token and sets it in the response. The token is then passed to the HTML template where it can be included in form tags to protect against CSRF attacks.To include the CSRF token in a form, you can use the c.Get method in your HTML template to retrieve the token and include it in a hidden input field:


```

xss protext

````package main``import (`  
`"github.com/gin-gonic/gin"`  
`)``func main() {`  
`// Create a new Gin router`  
`r := gin.New()` `// Use the gin.SanitizePath middleware on the router`  
`r.Use(gin.SanitizePath())` `// Define a route that is protected against XSS attacks`  
`r.GET("/:param", func(c *gin.Context) {`  
`// The path parameter will be automatically sanitized by the`  
`// gin.SanitizePath middleware, so it is safe to use in your code`  
`param := c.Param("param")` `// Handle the request here`  
`})` `// Start the server`  
`r.Run(":8080")`  
`}`
```