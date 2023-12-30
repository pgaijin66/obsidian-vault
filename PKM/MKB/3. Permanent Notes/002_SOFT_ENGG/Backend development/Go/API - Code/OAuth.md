

```
func NewOAuthConfig(clientID string, clientSecret string, redirectURL string, scopes []string, endpoint oauth2.Endpoint) (*oauth2.Config, error) {
	oauthConfig := &oauth2.Config{
		ClientID:     clientID,
		ClientSecret: clientSecret,
		RedirectURL:  redirectURL,
		Scopes:       scopes,
		Endpoint:     endpoint,
	}

	return oauthConfig, nil
}
```

in server.go add dependency as

```
googleOauthConfig, err := oauth.NewOAuthConfig(
		conf.OAuth.Google.ClientID,
		conf.OAuth.Google.ClientSecret,
		conf.OAuth.Google.RedirectURL,
		conf.OAuth.Google.Scopes,
		google.Endpoint,
	)
	if err != nil {
		return nil, fmt.Errorf("could not create google oauth config: %v", err)
	}
```


```
server := &Server{
		conf:                conf,
		tokenMaker:          tokenMaker,
		logger:              logger,
		redisClient:         redisClient,
		store:               *store,
		googleOauthConfig:   googleOauthConfig,
		linkedinOauthConfig: linkedinOauthConfig,
	}
```