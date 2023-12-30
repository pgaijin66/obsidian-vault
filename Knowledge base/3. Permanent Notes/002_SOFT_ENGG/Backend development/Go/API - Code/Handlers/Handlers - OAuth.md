
```
package server

import (
	"net/http"
	"net/url"
	"os"

	"github.com/dhiki-labs/chhaaano__backend/internal/errors"
	"github.com/dhiki-labs/chhaaano__backend/internal/model"
	"github.com/dhiki-labs/chhaaano__backend/internal/service"
	"github.com/gin-contrib/requestid"
	"github.com/gin-gonic/gin"
	"golang.org/x/oauth2"
)

func (server *Server) handleOAuthLogin(ctx *gin.Context, oauth2Config *oauth2.Config) {
	url := oauth2Config.AuthCodeURL("state")
	ctx.Redirect(http.StatusTemporaryRedirect, url)
}

func (server *Server) handleOAuthCallback(ctx *gin.Context, oauth2Config *oauth2.Config, endpoint string, provider string) {
	code := ctx.Query("code")
	user, accessToken, expiresAt, err := service.OAuthCallback(
		code,
		oauth2Config,
		server.tokenMaker,
		server.conf.Token.AccessTokenDuration,
		&server.store,
		endpoint)
	if err != nil {
		server.logger.Error().
			Str("Error", err.Error()).
			Str("request-id", requestid.Get(ctx)).
			Msgf("could not handle %s oauth callback", provider)

		errorsListslice := make([]model.Error, 0, 1)
		errorsListslice = append(errorsListslice, model.Error{
			Code:    int(errors.ErrCouldNotLogin),
			Message: errors.ErrOperationFailed.Message(),
		})
		result := model.Result{}
		response := model.HTTPResponse{
			Errors:     errorsListslice,
			Result:     &result,
			StatusCode: http.StatusInternalServerError,
		}
		ctx.JSON(http.StatusInternalServerError, response)
		return
	}

	result := model.Result{}
	result["user"] = user
	result["access_token"] = accessToken
	result["expires_at"] = expiresAt
	result["message"] = "login successful"
	ctx.Writer.Header().Set("Content-Type", "application/json")
	redirectURL, _ := url.Parse(os.Getenv("WEB_HOST_URL"))
	// Add query parameters to the URL
	q := redirectURL.Query()
	q.Set("uid", user.ID.Hex())
	q.Set("access_token", accessToken)
	q.Set("expires_at", expiresAt.UTC().Format("2006-01-02T15:04:05Z07:00"))
	redirectURL.RawQuery = q.Encode()
	ctx.Redirect(http.StatusTemporaryRedirect, redirectURL.String())
}

func (server *Server) handleOAuthLinkedinLogin(ctx *gin.Context) {
	server.handleOAuthLogin(ctx, server.linkedinOauthConfig)
}

func (server *Server) handleOAuthGoogleLogin(ctx *gin.Context) {
	server.handleOAuthLogin(ctx, server.googleOauthConfig)
}

func (server *Server) handleOAuthLinkedinCallback(ctx *gin.Context) {
	server.handleOAuthCallback(ctx, server.linkedinOauthConfig, "https://api.linkedin.com/v2/userinfo", "linkedin")
}

func (server *Server) handleOAuthGoogleCallback(ctx *gin.Context) {
	server.handleOAuthCallback(ctx, server.googleOauthConfig, "https://www.googleapis.com/oauth2/v3/userinfo", "google")
}
```