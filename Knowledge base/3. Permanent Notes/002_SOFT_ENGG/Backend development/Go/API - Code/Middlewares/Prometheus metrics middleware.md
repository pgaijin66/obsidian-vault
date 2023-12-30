

```
var totalRequests = promauto.NewCounterVec(
	prometheus.CounterOpts{
		Name: "http_requests_total",
		Help: "Number of get requests",
	},
	[]string{"path"},
)

// Prometheus middleware is used to generate and expose TSDB metrics
func Prometheus() gin.HandlerFunc {
	return func(c *gin.Context) {
		totalRequests.WithLabelValues(c.Request.URL.Path).Inc()
		c.Next()
	}
}
```