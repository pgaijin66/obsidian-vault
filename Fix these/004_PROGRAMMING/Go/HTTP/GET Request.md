
### Method 1
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


### Method 2

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