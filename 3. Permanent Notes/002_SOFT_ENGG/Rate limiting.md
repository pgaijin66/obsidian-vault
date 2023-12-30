Implement followings

-   `X-RateLimit-Limit:[10]` The maximum number of requests available in the current time frame.
- `X-Ratelimit-Remaining:[9]` :The number of remaining requests in the current time frame.
- `X-Ratelimit-Reset:[12123871823]` : A [UNIX timestamp](https://en.wikipedia.org/wiki/Unix_time) of the expected time when the rate limit will reset.

If within the cap
`Send 200 OK`

If goes over the cap
`429 Too Many Requests`