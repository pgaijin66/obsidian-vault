
```
func (server *Server) registerRoutes() {
	// =============================
	// healthcheck endpoint
	// =============================
	server.router.GET("/health/live", server.handleCheckLive)
	server.router.GET("/health/ready", server.handleCheckReady)

	// =============================
	// Prometheus endpoint
	// =============================
	server.router.GET("/metrics", middlewares.RequiredBasicAuth(server.conf.Auth.Basic.Username, server.conf.Auth.Basic.Password), server.handleSetupPrometheus())

	v1 := server.router.Group("/v1")

	// =============================
	// listing service endpoints
	// =============================
	v1.GET("/listings", middlewares.RequiredBasicAuth(server.conf.Auth.Basic.Username, server.conf.Auth.Basic.Password), server.handleGetAllListings)
	v1.GET("/listings/:listing-id", middlewares.RequiredBasicAuth(server.conf.Auth.Basic.Username, server.conf.Auth.Basic.Password), server.handleGetListingByID)
	v1.POST("/listing", middlewares.AuthMiddleware(server.tokenMaker, server.logger), server.handleCreateListing)
	v1.DELETE("/listings/:listing-id", middlewares.AuthMiddleware(server.tokenMaker, server.logger), server.handleDeleteListing)
	v1.PATCH("/listings/:listing-id", middlewares.AuthMiddleware(server.tokenMaker, server.logger), server.handleUpdateListing)

	// =============================
	// media upload endpoints
	// =============================
	v1.POST("/avatar/upload", middlewares.AuthMiddleware(server.tokenMaker, server.logger), server.handleUploadAvatarImage)
	v1.POST("/listings/:listing-id/upload", middlewares.AuthMiddleware(server.tokenMaker, server.logger), server.handleUploadListingImage)

	// =============================
	// private endpoints
	// =============================
	v1.GET("/users/:user-id/listings", middlewares.AuthMiddleware(server.tokenMaker, server.logger), server.handleGetListingByAuthor)

	// =============================
	// OTP endpoints
	// =============================
	v1.POST("/otp/send", middlewares.AuthMiddleware(server.tokenMaker, server.logger), server.handleSendOTP)
	v1.GET("/otp/verify", middlewares.AuthMiddleware(server.tokenMaker, server.logger), server.handleVerifyOTP)

	// =============================
	// User endpoints
	// =============================
	v1.POST("/user", server.handleCreateUser)
	v1.GET("/users/:user-id/lock-user", middlewares.AuthMiddleware(server.tokenMaker, server.logger), server.handleLockUser)
	v1.GET("/users/:user-id", middlewares.AuthMiddleware(server.tokenMaker, server.logger), server.handleGetUserByID)
	v1.GET("/users", middlewares.AuthMiddleware(server.tokenMaker, server.logger), server.handleGetAllUsers)
	v1.PATCH("/users/:user-id", middlewares.AuthMiddleware(server.tokenMaker, server.logger), server.handleUpdateUser)
	v1.DELETE("/users/:user-id", middlewares.AuthMiddleware(server.tokenMaker, server.logger), server.handleDeleteUser)

	// =============================
	// Auth endpoints
	// =============================
	v1.POST("/auth/login", server.handleLogin)
	v1.GET("/auth/logout", server.handleLogout)

	//======================================
	// OAuth2
	//======================================
	// Google
	v1.GET("/auth/oauth2/gl/login", server.handleOAuthGoogleLogin)
	v1.GET("/auth/oauth2/gl/callback", server.handleOAuthGoogleCallback)
	// Facebook
	v1.GET("/auth/oauth2/ln/login", server.handleOAuthLinkedinLogin)
	v1.GET("/auth/oauth2/ln/callback", server.handleOAuthLinkedinCallback)

	v1.GET("/auth/check-auth", middlewares.AuthMiddleware(server.tokenMaker, server.logger), server.handleTokenVerification)

	//======================================
	// Reset Password
	//======================================
	// For unauthenticated user
	v1.POST("/forgot_password", server.handleForgotPassword)
	v1.PUT("/reset_password", server.handleAuthenticatedUserResetPassword)
	v1.POST("/change_password", server.handleUnAuthenticatedUserResetPassword)

	// For authenticated user
	v1.PUT("/account/password", middlewares.AuthMiddleware(server.tokenMaker, server.logger), server.handleAuthenticatedUserResetPassword)
}
```