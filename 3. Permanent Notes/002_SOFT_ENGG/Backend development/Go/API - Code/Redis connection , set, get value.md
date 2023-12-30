
```
package db

func NewRedisClient(address, username, password string) (*redis.Client, error) {
	redisClient := redis.NewClient(&redis.Options{
		Addr:     address,
		Username: username,
		Password: password, // no password set
		DB:       0,        // use default DB
	})

	_, err := redisClient.Ping(redisClient.Context()).Result()
	if err != nil {
		return nil, fmt.Errorf("could not create new redis client: %v", err)
	}

	return redisClient, nil
}

func SetRedisValue(
	redisClient *redis.Client,
	key string,
	value string,
	expiry time.Duration,
) error {
	ctx := context.Background()

	err := redisClient.Set(ctx, key, value, expiry).Err()
	if err != nil {
		return fmt.Errorf("could not set value to redis: %v", err)
	}

	return nil
}

func GetRedisValue(redisClient *redis.Client, key string) (string, error) {
	ctx := context.Background()

	_, err := redisClient.Get(ctx, "a").Result()
	if err != nil {
		log.Info().Msg(err.Error())
	}

	// value, err := redisClient.Get(ctx,key).Result()
	// if err != nil {
	// 	return "", fmt.Errorf("could not set value to redis: %v", err)
	// }
	// return value, nil

	return "", nil
}

func InvalidateRedisValue(
	redisClient *redis.Client,
	key string,
	value string,
	expiry time.Duration,
) error {
	ctx := context.Background()

	err := redisClient.Set(ctx, key, value, expiry).Err()
	if err != nil {
		return fmt.Errorf("could not set value to redis: %v", err)
	}

	return nil
}
```


in server.go add as
```
type Server struct {
	// redisClient
	redisClient *redis.Client
}
```

```
redisClient, err := db.NewRedisClient(conf.Redis.URL, conf.Redis.Username, conf.Redis.Password)
	if err != nil {
		return nil, fmt.Errorf("could not create redis client: %v", err)
	}
```