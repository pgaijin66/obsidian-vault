
Struct
```
type Server struct {
	// Configuration
	conf *configs.Configuration
	// Token maker interface to generate and validate token
	tokenMaker token.Maker
	// Router
	router *gin.Engine
	// Application JSON logger
	logger zerolog.Logger
	// Datastore
	// db *firestore.Client
	// db client ( mongo )
	dbClient *mongo.Client
	store    repository.Store
	// redisClient
	redisClient *redis.Client
	// oauth
	googleOauthConfig   *oauth2.Config
	linkedinOauthConfig *oauth2.Config
}

```


New server
```
func NewServer(conf *configs.Configuration) (*Server, error) {
	logger := zerolog.New(os.Stderr).With().Timestamp().Logger()

	zerolog.TimeFieldFormat = zerolog.TimeFormatUnix

	// Default level for this example is info, unless debug flag is present
	zerolog.SetGlobalLevel(zerolog.InfoLevel)
	if os.Getenv("LOG_LEVEL") == "debug" {
		zerolog.SetGlobalLevel(zerolog.DebugLevel)
	}

	tokenMaker, err := token.NewPasetoMaker(conf.Token.SymmetricKey)
	if err != nil {
		return nil, fmt.Errorf("%v : %v", errors.ErrCouldNotCreateTokenMaker.Message(), err)
	}

	redisClient, err := db.NewRedisClient(conf.Redis.URL, conf.Redis.Username, conf.Redis.Password)
	if err != nil {
		return nil, fmt.Errorf("could not create redis client: %v", err)
	}

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

	linkedinOauthConfig, err := oauth.NewOAuthConfig(
		conf.OAuth.Linkedin.ClientID,
		conf.OAuth.Linkedin.ClientSecret,
		conf.OAuth.Linkedin.RedirectURL,
		conf.OAuth.Linkedin.Scopes,
		linkedin.Endpoint,
	)
	if err != nil {
		return nil, fmt.Errorf("could not create google oauth config: %v", err)
	}

	connectionString := os.Getenv("MONGO_URI")
	store, err := repository.NewStore(connectionString)
	if err != nil {
		log.Fatalf("Could not create a new database connection: %s", err)
	}

	server := &Server{
		conf:                conf,
		tokenMaker:          tokenMaker,
		logger:              logger,
		redisClient:         redisClient,
		store:               *store,
		googleOauthConfig:   googleOauthConfig,
		linkedinOauthConfig: linkedinOauthConfig,
	}

	router := gin.New()

	// Attach the CSRF middleware to the router

	router.Use(
		// OWASP: Sensitive Data Exposure: Attaching recovery middleware to automatically log and recover from panics
		gin.Recovery(),
		middlewares.CORS(server.conf),
		// Attach RequestID middleware is used to identify each request using unique identifier for logging and correlation
		requestid.New(),
		// OWASP: Sensitive Data Exposure: Enforcing HTTPS on server
		secure.New(secure.Config{
			// AllowedHosts: conf.TLSConfig.AllowedHosts,
			SSLRedirect: conf.TLSConfig.SSLRedirect,
			// SSLHost:      conf.TLSConfig.SSLHost,
			// STSSeconds:   conf.TLSConfig.STSSeconds,
			// STSIncludeSubdomains: conf.TLSConfig.STSIncludeSubdomains,
			// FrameDeny:             conf.TLSConfig.FrameDeny,
			ContentTypeNosniff: conf.TLSConfig.ContentTypeNosniff,
			BrowserXssFilter:   conf.TLSConfig.BrowserXSSFilter,
			// ContentSecurityPolicy: conf.TLSConfig.ContentSecurityPolicy,
			// IENoOpen:              conf.TLSConfig.IENoOpen,
			// ReferrerPolicy:        conf.TLSConfig.ReferrerPolicy,
			// SSLProxyHeaders:       conf.TLSConfig.SSLProxyHeaders,
		}),
		middlewares.TransportJSONLoggerMiddleware(),
		middlewares.Prometheus(),
	)

	// Prevent from calling methods not implemented.
	router.HandleMethodNotAllowed = true

	router.NoRoute(func(c *gin.Context) {
		c.JSON(http.StatusNotFound, gin.H{"code": "PAGE_NOT_FOUND", "message": "404 page not found"})
	})

	router.NoMethod(func(c *gin.Context) {
		c.JSON(http.StatusMethodNotAllowed, gin.H{"code": "METHOD_NOT_ALLOWED", "message": "405 method not allowed"})
	})

	server.router = router

	server.registerRoutes()

	dbClient, err := db.CreateConnection(connectionString)
	if err != nil {
		log.Fatalf("Could not create a new database connection: %s", err)
	}

	server.dbClient = dbClient
	return server, nil
}
```


```
// Run starts the server on defined HOST and PORT using HTTP
func (server *Server) Run() error {
	if err := server.router.Run(server.conf.Server.Host + ":" + server.conf.Server.Port); err != nil {
		return fmt.Errorf("could not start the server in HTTP mode")
	}

	return nil
}

// RunTLS starts the server defined on HOST and PORT using HTTPS. To run server in HTTPS mode, TLS cert and key must be present
func (server *Server) RunTLS() error {
	if !validators.IsDefined(server.conf.TLSConfig.Cert) && !validators.IsDefined(server.conf.TLSConfig.Key) {
		return fmt.Errorf("TLS key and cert files path not defined")
	}

	if !validators.IsFileExists(server.conf.TLSConfig.Cert) && !validators.IsDefined(server.conf.TLSConfig.Key) {
		return fmt.Errorf("TLS key and cert file file not present")
	}

	if err := server.router.RunTLS(server.conf.Server.Host+":"+server.conf.Server.Port, server.conf.TLSConfig.Cert, server.conf.TLSConfig.Key); err != nil {
		return fmt.Errorf("could not start the server in HTTPS mode")
	}

	return nil
}
```