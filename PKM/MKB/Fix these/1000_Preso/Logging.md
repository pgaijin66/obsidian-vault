
## Logging

Logs are basically record of events


1. Application logging
2. Transport logging
asd

---


### Application Logging

Application logging is used to <span style="color:red ">log certain important events in application</span>. These logs are used to to verify if the application is working as expected. 


---

### Transport logging

Transport logging is to used to log request coming in such as HTTP logs. These logs are used to see if the incoming request passed or failed.


---

## What to log
1. Status code
2. Endpoint
3. Method
4. Delta time ( start and response time )
5. Client IP address
6. Request ID

---

## Package

Different logging libraries

1. zerolog
2. logrus
3. zap
4. custom logger

For this project we will be using  <span style="color:red ">`zerolog`</span>