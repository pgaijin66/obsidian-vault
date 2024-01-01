
Parse response body to JSON

```go
	var j interface{}
	err = json.NewDecoder(resp.Body).Decode(&j)
	if err != nil {
		return "", "", "", "", nil
	}
	fmt.Printf("%s", j)
```



parse response body if body is on different format `demo={}`

```go

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