
```
type Server struct {
	// Application JSON logger
	logger zerolog.Logger
}



func NewServer(conf *configs.Configuration) (*Server, error) {
	logger := zerolog.New(os.Stderr).With().Timestamp().Logger()

	zerolog.TimeFieldFormat = zerolog.TimeFormatUnix

	// Default level for this example is info, unless debug flag is present
	zerolog.SetGlobalLevel(zerolog.InfoLevel)
	if os.Getenv("LOG_LEVEL") == "debug" {
		zerolog.SetGlobalLevel(zerolog.DebugLevel)
	}
server := &Server{
		logger:              logger,
	}
}
```