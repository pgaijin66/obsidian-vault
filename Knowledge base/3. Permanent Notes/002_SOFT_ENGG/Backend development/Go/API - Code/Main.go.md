
```
package main

import (
	"fmt"
	"log"

	"github.com/dhiki-labs/chhaaano__backend/internal/configs"
	"github.com/dhiki-labs/chhaaano__backend/internal/server"
)

const (
	defaultConfigPath = "data"
	configName        = "config"
	configType        = "yaml"
)

func main() {
	if defaultConfigPath == "" {
		log.Fatal("config path not provided.")
	}

	conf, err := configs.ConfigSetup(defaultConfigPath, configName, configType)
	if err != nil {
		log.Fatal(fmt.Errorf("could not complete config setup: %v", err))
	}

	server, err := server.NewServer(conf)
	if err != nil {
		log.Fatal(fmt.Errorf("could not create a new server: %v", err))
	}

	switch conf.Server.Protocol {
	case "https":
		err := server.RunTLS()
		if err != nil {
			log.Fatal(fmt.Errorf("server is not running: %v", err))
		}
	case "http":
		err := server.Run()
		if err != nil {
			log.Fatal(fmt.Errorf("server is not running: %v", err))
		}
	default:
		panic(fmt.Sprintf("server type '%s' not supported.", conf.Server.Protocol))
	}
}
```