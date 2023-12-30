
# HTTP Get Request
Created date: 2023-06-09 12:33


### Method 1 ( When you do not know what to expect )
```go
	url := "https://dhikilabs.us.auth0.com/userinfo"

	req, err := http.NewRequest("GET", url, nil)
	if err != nil {
		ctx.JSON(http.StatusInternalServerError, "invalid url ")
		return
	}
	req.Header.Add("authorization", "Bearer "+token.AccessToken)

	res, err := http.DefaultClient.Do(req)
	if err != nil {
		fmt.Println(err.Error())
		ctx.JSON(http.StatusInternalServerError, "could not sent request")
		return
	}
	body, err := ioutil.ReadAll(res.Body)
	if err != nil {
		fmt.Println(err.Error())
		ctx.JSON(http.StatusInternalServerError, "could not read body")
		return
	}

	fmt.Println(res)
	fmt.Println(string(body))
```


### Method  ( For decode into predefined type )

```go
	c := http.Client{Timeout: time.Duration(1) * time.Second}
	resp, err := client.Get("https://dhikilabs.us.auth0.com/userinfo")
	if err != nil {
		ctx.JSON(http.StatusInternalServerError, "invalid access token")
		return
	}

	fmt.Println(resp.Status)

	var j map[string]interface{}

	if err := json.NewDecoder(resp.Body).Decode(&j); err != nil {
		ctx.JSON(http.StatusInternalServerError, "could not decode response body")
		return
	}
	fmt.Println(j)
```

Parse response body to JSON

```
	var j interface{}
	err = json.NewDecoder(resp.Body).Decode(&j)
	if err != nil {
		return "", "", "", "", nil
	}
	fmt.Printf("%s", j)
```


parse response body if body if on different format `demo={}`

```

	body, err := ctx.GetRawData()
	if err != nil {
		ctx.JSON(http.StatusBadRequest, gin.H{"error": "Failed to read request body"})
		return
	}
	data := string(body)

	data = strings.TrimPrefix(data, "payload=")

	// Decode URL-encoded string
	decoded, err := url.QueryUnescape(data)
	if err != nil {
		ctx.JSON(http.StatusOK, "Failed to decode. Something wrong")
		return
	}
	fmt.Println(decoded)
```

## References
1. https://pkg.go.dev/net/http