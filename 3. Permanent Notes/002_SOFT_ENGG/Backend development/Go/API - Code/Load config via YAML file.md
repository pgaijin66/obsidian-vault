
```
type Configuration struct {
	Environment string
	Server      struct {
		Host     string
		Port     string
		Protocol string
	}
	TLSConfig struct {
		Cert                  string
		Key                   string
		Insecure              bool
		AllowedHosts          []string
		SSLHost               string
		SSLRedirect           bool
		STSSeconds            int64
		STSIncludeSubdomains  bool
		FrameDeny             bool
		ContentTypeNosniff    bool
		BrowserXSSFilter      bool
		ContentSecurityPolicy string
		IENoOpen              bool
		ReferrerPolicy        string
		SSLProxyHeaders       map[string]string
	}
	Database struct {
		Driver   string
		Name     string
		User     string
		Password string
		Port     string
		Host     string
	}
	Logger struct {
		Name      string
		Path      string
		LogToFile bool
	}
	Firebase struct {
		PathToCredsFile string
	}
	Token struct {
		SymmetricKey        string
		AccessTokenDuration time.Duration
	}
	Crypto struct {
		HMAC struct {
			Secret string
		}
	}
	Auth struct {
		Basic struct {
			Username string
			Password string
		}
	}
	PasswordResetPageURL           string
	PasswordResetPageEndpoint      string
	PasswordResetEmailLinkDuration time.Duration
	CORS                           struct {
		AllowedOrigins   string
		AllowedMethods   string
		ExposedHeaders   string
		Allowedheaders   string
		AllowCredentials bool
		MaxAge           int
	}
	Redis struct {
		Version  string
		URL      string
		Username string
		Password string
	}
	OTP struct {
		ExpirationTime time.Duration
	}
	Pagination struct {
		DefaultPage     int
		DefaultPageSize int
	}
	Webhook struct {
		Revalidation struct {
			Endpoint string
			Secret   string
		}
	}
	OAuth struct {
		Google struct {
			ClientID     string
			ClientSecret string
			RedirectURL  string
			Scopes       []string
		}
		Linkedin struct {
			ClientID     string
			ClientSecret string
			RedirectURL  string
			Scopes       []string
		}
	}
}

func ConfigSetup(configPath, configName, configType string) (*Configuration, error) {
	var config *Configuration

	viper.AddConfigPath(configPath)
	viper.SetConfigName(configName)
	viper.SetConfigType(configType)
	viper.AutomaticEnv()

	// Below statement is required if you want to fetch environment
	// Eg: To fetch from env var from database.driver, set env var as
	// DATABASE_DRIVER
	viper.SetEnvKeyReplacer(strings.NewReplacer(`.`, `_`))
	err := viper.ReadInConfig()
	if err != nil {
		return nil, fmt.Errorf("error loading config file: %s", err)
	}

	err = viper.Unmarshal(&config)
	if err != nil {
		return nil, fmt.Errorf("error reading config file: %s", err)
	}

	return config, nil
}
```