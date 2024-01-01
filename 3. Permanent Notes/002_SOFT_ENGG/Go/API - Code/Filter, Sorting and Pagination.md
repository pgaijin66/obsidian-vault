
```
func (l *ListingStore) GetAllListings(filters map[string]string, sort string, page int, pageSize int, defaultPage int, defaultPageSize int) ([]model.Listing, int64, error) {
	database := l.dbClient.Database("auth-svc")
	listingCollection := database.Collection("listings")

	var listings []model.Listing

	ctx := context.Background()

	// making sure only published listings are shown
	filter := bson.M{"listingStatus": "published"}

	// set up options for the find operation. This will return data in order of created date in descending order
	findOptions := options.Find().SetSort(bson.D{{Key: "createdAt", Value: -1}})

	if filters["zip"] != "" {
		filter["zip"] = filters["zip"]
	}

	bedRooms, _ := strconv.Atoi(filters["bedRooms"])
	if bedRooms != 0 {
		filter["noOfBedRooms"] = bedRooms
	}

	bathRooms, _ := strconv.Atoi(filters["bathRooms"])
	if bathRooms != 0 {
		filter["noOfBathRooms"] = bathRooms
	}

	if filters["priceMin"] != "" && filters["priceMax"] != "" {
		priceMinInt, _ := strconv.Atoi(filters["priceMin"])
		priceMaxInt, _ := strconv.Atoi(filters["priceMax"])

		filter["rentAmount"] = bson.M{
			"$gte": priceMinInt,
			"$lt":  priceMaxInt,
		}
	}

	if filters["propertyType"] != "" {
		filter["propertyType"] = bson.M{
			"$regex": primitive.Regex{
				Pattern: filters["propertyType"],
				Options: "i",
			},
		}
	}

	if filters["listingType"] != "" {
		filter["listingType"] = bson.M{
			"$regex": primitive.Regex{
				Pattern: filters["listingType"],
				Options: "i",
			},
		}
	}

	// Sorting by rent_amount
	if sort != "" {
		if sort == "asc" {
			findOptions.SetSort(bson.D{{Key: "rentAmount", Value: 1}})
		}
		if sort == "desc" {
			findOptions.SetSort(bson.D{{Key: "rentAmount", Value: -1}})
		}
	}

	// Pagination
	findOptions.SetSkip((int64(page) - 1) * int64(pageSize))
	findOptions.SetLimit(int64(pageSize))

	total, _ := listingCollection.CountDocuments(ctx, filter)

	cursor, err := listingCollection.Find(ctx, filter, findOptions)
	if err != nil {
		return listings, total, err
	}

	defer func() {
		if err := cursor.Close(ctx); err != nil {
			log.Error().Msg("could not close the cursor")
		}
	}()

	for cursor.Next(ctx) {
		listing := model.NewListing()
		err := cursor.Decode(&listing)
		if err != nil {
			return listings, total, err
		}

		if listing.ListingStatus == "published" {
			listings = append(listings, *listing)
		}
	}

	return listings, total, nil
}
```