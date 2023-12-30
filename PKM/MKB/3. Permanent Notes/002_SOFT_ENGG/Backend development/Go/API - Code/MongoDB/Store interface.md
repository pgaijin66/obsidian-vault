
```
package repository

import (
	"context"
	"fmt"

	"github.com/dhiki-labs/chhaaano__backend/internal/db"
	"go.mongodb.org/mongo-driver/mongo/readpref"
)

func NewStore(connectionString string) (*Store, error) {
	dbClient, err := db.CreateConnection(connectionString)
	if err != nil {
		return nil, fmt.Errorf("could not create a new database connection: %w", err)
	}
	ctx := context.Background()

	if err := dbClient.Ping(ctx, readpref.Primary()); err != nil {
		return nil, fmt.Errorf("error connecting to the database: %w", err)
	}

	return &Store{
		ListingStore: *NewListingStore(dbClient),
		UserStore:    *NewUserStore(dbClient),
	}, nil
}

type Store struct {
	ListingStore
	UserStore
}
```


in server.go

```
type Server struct {
	// Datastore
	// db *firestore.Client
	// db client ( mongo )
	dbClient *mongo.Client
}
```

```
connectionString := os.Getenv("MONGO_URI")
	store, err := repository.NewStore(connectionString)
	if err != nil {
		log.Fatalf("Could not create a new database connection: %s", err)
	}
```