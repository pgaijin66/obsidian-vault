
```

const (
	authorizationHeaderKey  = "authorization"
	authorizationTypeBearer = "Bearer"
	AuthorizationPayloadKey = "authorization_payload"
)

// AuthMiddleware middleware is used to check if user is authenticated. It takes tokenMaker and logger as input.
func AuthMiddleware(tokenMaker token.Maker, logger zerolog.Logger) gin.HandlerFunc {
	return func(c *gin.Context) {
		authorizationHeader := c.GetHeader(authorizationHeaderKey)

		if len(authorizationHeader) == 0 {

			err := &model.Error{
				Code:    7002,
				Message: "authorization header not provided",
			}

			logger.Error().
				Str("request-id", requestid.Get(c)).
				Msg("authorization header not provided")

			errorsListslice := make([]model.Error, 0, 1)
			errorsListslice = append(errorsListslice, *err)

			result := m.Result{}

			response := model.HTTPResponse{
				Errors:     errorsListslice,
				Result:     &result,
				StatusCode: http.StatusBadRequest,
			}

			c.JSON(http.StatusUnauthorized, response)
			c.Abort()
			return
		}

		fields := strings.Fields(authorizationHeader)

		if len(fields) < 2 {

			err := &model.Error{
				Code:    7003,
				Message: "invalid authorization header format",
			}

			logger.Error().
				Str("request-id", requestid.Get(c)).
				Msg("invalid authorization header format")

			errorsListslice := make([]model.Error, 0, 1)
			errorsListslice = append(errorsListslice, *err)

			result := model.Result{}

			response := model.HTTPResponse{
				Errors:     errorsListslice,
				Result:     &result,
				StatusCode: http.StatusBadRequest,
			}

			c.JSON(http.StatusUnauthorized, response)
			c.Abort()
			return
		}

		authorizationType := strings.ToLower(fields[0])

		if authorizationType != strings.ToLower(authorizationTypeBearer) {

			err := &model.Error{
				Code:    7003,
				Message: "unsupported authorization type",
			}

			logger.Error().
				Str("request-id", requestid.Get(c)).
				Msg("unsupported authorization type")

			errorsListslice := make([]model.Error, 0, 1)
			errorsListslice = append(errorsListslice, *err)

			result := model.Result{}

			response := model.HTTPResponse{
				Errors:     errorsListslice,
				Result:     &result,
				StatusCode: http.StatusBadRequest,
			}

			c.JSON(http.StatusUnauthorized, response)
			c.Abort()
			return
		}

		accesToken := fields[1]

		payload, err := tokenMaker.VerifyToken(accesToken)
		if err != nil {

			logger.Error().
				Str("request-id", requestid.Get(c)).
				Msg(err.Error())

			err := &model.Error{
				Code:    8003,
				Message: err.Error(),
			}

			errorsListslice := make([]model.Error, 0, 1)
			errorsListslice = append(errorsListslice, *err)

			response := model.HTTPResponse{
				Errors:     errorsListslice,
				Result:     nil,
				StatusCode: http.StatusBadRequest,
			}

			c.JSON(http.StatusUnauthorized, response)
			c.Abort()
			return
		}

		c.Set(AuthorizationPayloadKey, payload)
		c.Next()
	}
}
```