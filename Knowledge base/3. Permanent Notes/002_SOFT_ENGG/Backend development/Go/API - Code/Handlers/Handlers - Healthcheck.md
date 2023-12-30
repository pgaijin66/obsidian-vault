
```
package server

import (
	"net/http"

	"github.com/dhiki-labs/chhaaano__backend/internal/model"
	"github.com/dhiki-labs/chhaaano__backend/internal/service"
	"github.com/dhiki-labs/chhaaano__backend/internal/version"
	"github.com/gin-gonic/gin"
)

func (server *Server) handleCheckLive(ctx *gin.Context) {
	result := model.Result{}

	result["message"] = "running"
	result["commit_id"] = version.CommitID
	result["branch"] = version.Branch
	result["tag"] = version.Tag

	resp := &model.HTTPResponse{
		StatusCode: http.StatusOK,
		Result:     &result,
		Errors:     nil,
	}

	ctx.JSON(http.StatusOK, resp)
}

// checkReady checks if all the dependencies for this service is running or not
func (server *Server) handleCheckReady(ctx *gin.Context) {
	result := model.Result{}

	servicesStatus := model.ServiceStatus{}

	if !service.CheckDBStatus() {
		servicesStatus.Database = "SHIT"
	}

	if !service.CheckRedisStatus(server.conf.Redis.URL, server.conf.Redis.Username, server.conf.Redis.Password) {
		servicesStatus.Redis = "SHIT"
	}

	if !service.CheckCloudinaryStatus() {
		servicesStatus.Cloudinary = "SHIT"
	}

	if servicesStatus.Database == "SHIT" || servicesStatus.Redis == "SHIT" || servicesStatus.Cloudinary == "SHIT" {
		ctx.AbortWithStatus(http.StatusServiceUnavailable)
		result["status"] = servicesStatus
		result["message"] = "health check completed"

		resp := &model.HTTPResponse{
			StatusCode: http.StatusServiceUnavailable,
			Result:     &result,
			Errors:     nil,
		}

		ctx.JSON(http.StatusServiceUnavailable, resp)
		return
	}

	servicesStatus.Database = "OK"
	servicesStatus.Redis = "OK"
	servicesStatus.Cloudinary = "OK"

	result["status"] = servicesStatus
	result["message"] = "health check completed"

	resp := &model.HTTPResponse{
		StatusCode: http.StatusOK,
		Result:     &result,
		Errors:     nil,
	}

	ctx.JSON(http.StatusOK, resp)
}
```