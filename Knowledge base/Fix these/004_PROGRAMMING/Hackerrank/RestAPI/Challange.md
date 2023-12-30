

```go
package main

import (
	"encoding/json"
	"fmt"
	"net/http"
	"strconv"
)

type Match struct {
	Team1      string `json:"team1"`
	Team2      string `json:"team2"`
	Team1Goals string `json:"team1goals"`
	Team2Goals string `json:"team2goals"`
}

type Response struct {
	Page       int     `json:"page"`
	PerPage    int     `json:"per_page"`
	Total      int     `json:"total"`
	TotalPages int     `json:"total_pages"`
	Data       []Match `json:"data"`
}

func getTotalGoals(team string, year int) (int, error) {
	baseURL := "https://jsonmock.hackerrank.com/api/football_matches"
	totalGoals := 0

	// Send request to fetch matches where the team was the home team
	homeURL := fmt.Sprintf("%s?year=%d&team1=%s", baseURL, year, team)
	homeGoals, err := getGoalsForURL(homeURL, team)
	if err != nil {
		return 0, err
	}
	totalGoals += homeGoals

	// Send request to fetch matches where the team was the visiting team
	awayURL := fmt.Sprintf("%s?year=%d&team2=%s", baseURL, year, team)
	awayGoals, err := getGoalsForURL(awayURL, team)
	if err != nil {
		return 0, err
	}
	totalGoals += awayGoals

	return totalGoals, nil
}

func getGoalsForURL(url string, team string) (int, error) {
	resp, err := http.Get(url)
	if err != nil {
		return 0, err
	}
	defer resp.Body.Close()

	var response Response
	err = json.NewDecoder(resp.Body).Decode(&response)
	if err != nil {
		return 0, err
	}

	totalGoals := 0
	for _, match := range response.Data {
		if match.Team1 == team {
			team1Goals, err := strconv.Atoi(match.Team1Goals)
			if err != nil {
				return 0, err
			}
			totalGoals += team1Goals
		}
		if match.Team2 == team {
			team2Goals, err := strconv.Atoi(match.Team2Goals)
			if err != nil {
				return 0, err
			}
			totalGoals += team2Goals
		}
	}

	return totalGoals, nil
}

func main() {
	team := "Barcelona"
	year := 2011

	totalGoals, err := getTotalGoals(team, year)
	if err != nil {
		fmt.Printf("Error: %v\n", err)
		return
	}

	fmt.Println(totalGoals)
}
```




```go
package main

import (
	"encoding/json"
	"fmt"
	"net/http"
)

type Match struct {
	Team1      string `json:"team1"`
	Team2      string `json:"team2"`
	Team1Goals string `json:"team1goals"`
	Team2Goals string `json:"team2goals"`
}

type Response struct {
	Page       int     `json:"page"`
	PerPage    int     `json:"per_page"`
	Total      int     `json:"total"`
	TotalPages int     `json:"total_pages"`
	Data       []Match `json:"data"`
}

func getNumDraws(year int) (int, error) {
	baseURL := "https://jsonmock.hackerrank.com/api/football_matches"
	page := 1
	totalDraws := 0

	for {
		url := fmt.Sprintf("%s?year=%d&page=%d", baseURL, year, page)
		resp, err := http.Get(url)
		if err != nil {
			return 0, err
		}
		defer resp.Body.Close()

		var response Response
		err = json.NewDecoder(resp.Body).Decode(&response)
		if err != nil {
			return 0, err
		}

		for _, match := range response.Data {
			if match.Team1Goals == match.Team2Goals {
				totalDraws++
			}
		}

		// Check if there are more pages to fetch
		if response.Page == response.TotalPages {
			break
		}

		// Move to the next page
		page++
	}

	return totalDraws, nil
}

func main() {
	year := 2011

	numDraws, err := getNumDraws(year)
	if err != nil {
		fmt.Printf("Error: %v\n", err)
		return
	}

	fmt.Println(numDraws)
}

```