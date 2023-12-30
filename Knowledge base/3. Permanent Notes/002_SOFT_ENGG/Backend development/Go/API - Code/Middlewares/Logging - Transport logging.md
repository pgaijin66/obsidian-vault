

```
// TransportJSONLoggerMiddleware middleware is used to log transport requests into JSON format.
func TransportJSONLoggerMiddleware() gin.HandlerFunc {
	return gin.LoggerWithFormatter(
		func(params gin.LogFormatterParams) string {
			log := make(map[string]interface{})
			log["status_code"] = params.StatusCode
			log["path"] = params.Path
			log["method"] = params.Method
			log["start_time"] = params.TimeStamp.Unix()
			log["remote_addr"] = params.ClientIP
			log["response_time"] = params.Latency.String()
			log["request_id"] = params.Request.Header.Get("X-Request-Id")
			s, _ := json.Marshal(log)
			return string(s) + "\n"
		},
	)
}
```