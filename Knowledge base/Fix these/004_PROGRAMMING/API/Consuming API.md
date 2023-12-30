
-   Pass api base URL as env var
-   Separate out `endpoint` from `base url`
-   Add timeout to api calls.
-   Retry failed request as needed i.e retry when `500`, and Do not retry when `200`, `4XX` status codes are received.
-   Add upper limit to retries to avoid retry storm.
-   `Content-Type: "application/json"`  header should be set for all request except for uploading image
- External APIs sometimes answer in headers when something is wrong (4xx) So make sure to log them on errors
- Use limits and pagination where possible, to avoid unbounded/long responses and excessive load on the server.
- If you receive a 429 then try to observe the “Retry-After” header.
- Enforce use of HTTPS and accept only the latest TLS versions. Enable certificate validation.
- Use the provided DNS name for the API - never assume that a static IP address will be ok. Consider resolving DNS again on retry, for an opportunity to get a different server IP address/instance.
- If a trace ID is provided in the response (especially errors) consider logging it, to help the service provider triage an issue.