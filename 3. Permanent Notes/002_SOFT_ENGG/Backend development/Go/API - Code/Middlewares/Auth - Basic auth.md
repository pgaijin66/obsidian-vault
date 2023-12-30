
```

// RequiredBasicAuth middleware is used to authenticate basic auth protected endpoint. It takes username and password as input.
func RequiredBasicAuth(username, password string) gin.HandlerFunc {
	return gin.BasicAuth(gin.Accounts{
		username: password,
	})
}

```