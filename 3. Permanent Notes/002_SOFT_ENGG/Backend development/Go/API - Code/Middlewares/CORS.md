

```
// CORS middleware configures and allows CORS headers on all requests
func CORS(conf *configs.Configuration) gin.HandlerFunc {
	return func(c *gin.Context) {
		c.Writer.Header().Set("Access-Control-Allow-Origin", conf.CORS.AllowedOrigins)
		c.Writer.Header().Set("Access-Control-Allow-Credentials", strconv.FormatBool(conf.CORS.AllowCredentials))
		c.Writer.Header().Set("Access-Control-Allow-Headers", conf.CORS.Allowedheaders)
		c.Writer.Header().Set("Access-Control-Allow-Methods", conf.CORS.AllowedMethods)

		if c.Request.Method == "OPTIONS" {
			c.AbortWithStatus(204)
			return
		}

		c.Next()
	}
}
```