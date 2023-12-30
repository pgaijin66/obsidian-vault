
Here we are trying to get all listings created by that user. Since two values are on separate collection, we need to use aggregation

```
func (l *ListingStore) GetUserListings(email string) ([]model.Listing, error) {
	ctx := context.Background()

	database := l.dbClient.Database("auth-svc")
	usersCollection := database.Collection("users")

	matchStage := bson.D{{Key: "$match", Value: bson.D{{Key: "email", Value: email}}}}

	lookupStage := bson.D{{
		Key: "$lookup",
		Value: bson.D{
			{Key: "from", Value: "listings"},
			{Key: "localField", Value: "email"},
			{Key: "foreignField", Value: "author"},
			{Key: "as", Value: "listings"},
		},
	}}

	showLoadedCursor, err := usersCollection.Aggregate(ctx, mongo.Pipeline{matchStage, lookupStage})
	if err != nil {
		return nil, fmt.Errorf("error aggregating documents: %w", err)
	}

	var userListings []struct {
		Email    string `bson:"email"`
		Listings []model.Listing
	}

	if err = showLoadedCursor.All(ctx, &userListings); err != nil {
		return nil, fmt.Errorf("error fetching documents: %w", err)
	}

	if len(userListings) == 0 {
		return nil, fmt.Errorf("no documents found: %w", err)
	}

	return userListings[0].Listings, err
}
```