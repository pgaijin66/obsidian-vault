
```
package service

import (
	"context"

	"github.com/cloudinary/cloudinary-go"
	"github.com/dhiki-labs/chhaaano__backend/internal/db"
	"go.mongodb.org/mongo-driver/mongo/readpref"
)

func CheckDBStatus() bool {
	ctx := context.Background()
	client := db.GetDB()
	err := client.Ping(ctx, readpref.Primary())
	return err == nil
}

func CheckRedisStatus(address, username, password string) bool {
	ctx := context.Background()
	client, err := db.NewRedisClient(address, username, password)
	if err != nil {
		return false
	}
	string, err := client.Ping(ctx).Result()
	if err != nil {
		return false
	}
	return string == "PONG"
}

func CheckCloudinaryStatus() bool {
	ctx := context.Background()
	cld, err := cloudinary.New()
	if err != nil {
		return false
	}

	res, err := cld.Admin.Ping(ctx)
	if err != nil {
		return false
	}
	return res.Status == "ok"
}
```