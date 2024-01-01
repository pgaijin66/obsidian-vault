
```yaml
# This file maintains the state of the given application. It can also be 
# use to customize certain aspects of the application.
#
# You must restart the application to reload manual changes to config.yml
#
#
environment: "dev"  # Available options: dev | stage | prod

# Server configuration
server:
  host: "0.0.0.0" # prod: api.chhaano.com
  port: "9090"
  protocol: http # Available options : http | https

# HTTPS configuration
tlsConfig:
  cert: certs/cert.crt
  key: certs/privKey.key
  # Allow insecure URL
  insecure: false
  # AllowedHosts is a list of fully qualified domain names that are allowed. Default is empty list, which allows any and all host names.
  allowedHosts: ["localhost:9090"]
  # SSLHost is the host name that is used to redirect http requests to https. Default is "", which indicates to use the same host.
  sslHost: localhost:9090
  # SSLRedirect is set to true, then only allow https requests. Default is false.
  sslRedirect: false
  # STSSeconds is the max-age of the Strict-Transport-Security header. Default is 0, which would NOT include the header.
  stsSeconds: 315360000
  # If STSIncludeSubdomains is set to true, the `includeSubdomains` will be appended to the Strict-Transport-Security header. Default is false.
  stsIncludeSubdomains: true
  # f FrameDeny is set to true, adds the X-Frame-Options header with the value of `DENY`. Default is false.
  frameDeny: true
  # If ContentTypeNosniff is true, adds the X-Content-Type-Options header with the value `nosniff`. Default is false.
  contentTypeNosniff: true
  # If BrowserXssFilter is true, adds the X-XSS-Protection header with the value `1; mode=block`. Default is false.
  browserXssFilter: true
  # ContentSecurityPolicy allows the Content-Security-Policy header value to be set with a custom value. Default is "".
  contentSecurityPolicy: default-src 'none'
  ieNoOpen: true
  referrerPolicy: strict-origin-when-cross-origin
  # SSLProxyHeaders is set of header keys with associated values that would indicate a valid https request. Useful when using Nginx: `map[string]string{"X-Forwarded-Proto": "https"}`. Default is blank map.
  sslProxyHeaders: {"X-Forwarded-Proto": "https"}

# By default logger will log to STDOUT. If you need to log to file for debugging then change 
# `log_to_file` from false to true.
logger:
  name: "chhaano__backend.log"
  path: "chhaano__backend/logs"
  logToFile: false

firebase:
  pathToCredsFile: "data/chhaano-api-firebase.json"

token:
  symmetricKey: TOKEN_SYMMETRICKEY
  accessTokenDuration: 60m

auth:
  basic:
    username: AUTH_BASIC_USERNAME
    password: AUTH_BASIC_PASSWORD

crypto:
  hmac:
    secret: CRYPTO_HMAC_SECRET

passwordResetPageUrl: PASSWORDRESETPAGEURL
passwordResetPageEndpoint: "/reset-password"
passwordResetEmailLinkDuration: 3h # 3 hour

cors:
  allowedOrigins: "*"
  allowedMethods: "POST, OPTIONS, PATCH, GET, PUT, DELETE"
  exposedheaders:
  allowedHeaders: "Content-Type, Content-Length, Accept-Encoding, X-CSRF-Token, Authorization, accept, origin, Cache-Control, X-Requested-With" 
  AllowCredentials: true
  maxAge: 600

redis:
  version: "6"
  url: REDIS_URL
  username: REDIS_USERNAME
  password: REDIS_PASSWORD

otp:
  expirationTime: 5m

pagination:
  defaultPage: 1
  defaultPageSize: 8

webhook:
  revalidation:
    endpoint: WEBHOOK_REVALIDATION_ENDPOINT
    secret: WEBHOOK_REVALIDATION_SECRET

oAuth:
  google:
    clientID: OAUTH_GOOGLE_CLIENTID
    clientSecret: OAUTH_GOOGLE_CLIENTSECRET
    redirectURL: OAUTH_GOOGLE_REDIRECTURL
    scopes: ["profile", "email", "openid"]
    
  linkedin:
    clientID: OAUTH_LINKEDIN_CLIENTID
    clientSecret: OAUTH_LINKEDIN_CLIENTSECRET
    redirectURL: OAUTH_LINKEDIN_REDIRECTURL
    scopes: ["openid", "profile", "email"]
```