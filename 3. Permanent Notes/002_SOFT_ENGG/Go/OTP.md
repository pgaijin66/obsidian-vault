```
func sendOTP(c *gin.Context) {
    // Retrieve the user's phone number from the request
    phoneNumber := c.Query("phone_number")
    // Generate a random 6-digit OTP
    otp := rand.Intn(1000000)
    // Save the OTP in Firebase for later verification
    // (e.g. using the Firebase Realtime Database or Cloud Firestore)
    // ...
    // Send the OTP to the user's phone number using Firebase Cloud Messaging
    // (e.g. using the Firebase Admin SDK)
    // ...
    // Return a success message to the client
    c.JSON(http.StatusOK, gin.H{"message": "OTP sent successfully"})
}
func main() {
    // Initialize the Gin router
    r := gin.Default()
    // Define the route
    r.GET("/send-otp", sendOTP)
    // Start the web server
    r.Run()
}
```