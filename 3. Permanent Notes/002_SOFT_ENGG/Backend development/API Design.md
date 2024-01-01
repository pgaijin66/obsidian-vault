## 3 main traits of good well designed API

1.  Easy to read and work with
    
2.  Hard to misuse
    
3.  Complete and concise
    

## Things you could do to design a effective API

1.  Decide what kind of API is it
    
2.  Identify **resource** and **sub-resources**. whether it is singleton or collection i.e `customer and customers` or collection and sub-collections i.e `customers and accounts` . Eg: take an example of a bsooks api. Here there will be two resource at minimum. Bsooks can have many book options
    
    1.  Bsooks
    2.  Bsook
        
3.  Create **URI based on those resource** figured above.
    
    `1/v1/rooms 2/v1/rooms/{roomId}`
    
4.  Decide how you want to produce data. Generally accepted format is **JSON format.**
    
5.  **Assign HTTP Methods to the URI** created above.
    
    `1HTTP GET /rooms 2HTTP GET /rooms/{roomId}`
    
6.  Use **versioned API** for scalability.
    
7.  Do not use **CRUD names in API endpoints**
    
    `1Wrong: 2ENDPOINT/createUser 3ENDPOINT/deleteUser 4 5Correct: 6HTTP POST ENDPOINT/user/{userID} 7HTTP DELETE ENDPOINT /user/{userID}`
    
8.  Make API **idempotent**. API consumer will make mistake, they might write code in such a way that it results in duplicate request. Make sure API is idempotent.
    
    1.  POST is not idempotent.
        
    2.  GET, PUT, DELETE, HEAD, OPTIONS, TRACE are idempotent operations
        
9.  API exposed in response should be **JSON encoded.**
    
10.  Should only support **required HTTP Methods**. All request other than allowed methods, should return 405 Method not allowed status code.
    
11.  Use **camelCase** for keys.
    
12.  Know when to use **PUT** and when to use **POST**.
    
    1.  PUT is idempotent. Use PUT to update a single resource i.e PUT replaces the resources entirety. use PUT for update operations.
        
        1.  Use PATCH is only certain part of resource needs to be updated.
            
    2.  POST is not idempotent. Use it for create operations
        
13.  Should **provide json response back** to the API caller. i.e 200-type response. Consider following things:
    
    1.  Do not put redundant information in the api response. For example, there is no point adding `isSuccess: true` or `success: true` or `message: sucess`. You will know if the request was success or not via the HTTP status code. You can put anything in message.
        
    2.  The client application behaved erroneously (client error - 4xx response code)
        
        `1Response header: 2< HTTP/2 404 3< content-type: application/json; charset=utf-8 4< content-length: 59 5... 6 7Response body: 8{ 9 "data": "null", 10 "message": "listing not found.", 11 "status": 404 12}`
        
    3.  The API behaved erroneously (server error - 5xx response code)
        
        `1{ 2 "data": "null", 3 "message": "Could not find the item.", 4 "status": 500 5}`
        
    4.  The client and API worked (success - 2xx response code)  
        
        `1Response header: 2< HTTP/2 404 3< content-type: application/json; charset=utf-8 4< content-length: 59 5... 6 7Response body: 8{ 9 "data": { 10 "listing_id": "lFUlOVI=", 11 "title": "1 bed room house", 12 "property_address": "8/12 Bond street", 13 "rent_amount": 2512, 14 "current_number_of_tenants": 2, 15 "property_type": "Unit", 16 "available_form": "2022/2/12", 17 "image_urls": [ 18 "https://i.pinimg.com/736x/70/5d/43/705d4385d27f937f86636934b04a89d9.jpg" 19 ], 20 "features": [ 21 "pet friendly", 22 "parking", 23 "internet" 24 ], 25 "author": "sthapaprabesh2020@gmail.com", 26 "number_of_bathRooms": 2, 27 "number_of_bedRooms": 1, 28 "notice_period_in_weeks": 2, 29 "preferred_tenant": "single", 30 "description": "good house", 31 "listing_status": 1, 32 "preferred_contact_method": "Phone", 33 "created_at": "2022-11-10T10:25:21.438118-08:00" 34 }, 35 "message": "created", 36 "status": 200 37}`
        
14.  Should **do one and only one thing**. Each feature can have multiple services working together to make it happen meaning, log feature needs to have two service UI service, auth service to make it possible.
    
15.  Should have **CORS implemented** for your service. it's important to have the OPTIONS check since CORS check uses OPTIONS. If HTTP request OPTIONS failed, it would fail the CORS.
    
    `1Access-Control-Allow-Credentials: true 2Access-Control-Allow-Methods: GET, POST, 3Access-Control-Allow-Headers: Content-Type,access-control-allow-origin, access-control-allow-headers 4Access-Control-Allow-Origin: *`
    
16.  No **abbreviations**
    
17.  Use **nouns and plural nouns** as required.
    
18.  No **Verbs** in the URL.
    
19.  Be careful while **nesting**. At most two functions could be tested into an endpoint.
    
20.  Should **expose metrics via** **“/metrics”** endpoint into TSDB format so that application like Prometheus can consume it for analysis and visualization.
    
21.  Should allow **filtering**, **sorting**, **field selection** and **paging.**
    
22.  Implement **Caching** ( in-memory ).
    
23.  Should not **handle request from non-desired services.** According to `RFC 7231 section 6.5.5` method not allowed should send back allowed methods to the user.
    
    `1< HTTP/2 405 2< Allow: GET 3< content-type: text/plain 4< content-length: 22 5< date: Thu, 10 Nov 2022 19:02:48 GMT`
    
24.  Should have endpoint called **“/health”** for **health check**. To understand this, first understand what is a healthy API or what do you consider an application to be in healthy state. At least **make sure health check is exposed only after internal dependency testing is verified**. You application cannot be called healthy if it runs but cannot fetch data from database.
    
    `1% curl -s ENDPOINT/health 2 3{ 4 "code": 200, 5 "message": "OK" 6}`
    
25.  Should **log properly** in a standard format format. Recommendation: **JSON** as lot of log parsing application aspect log messages in JSON format.
    
    `1{ 2 "method": "GET", 3 "path": "/health", 4 "remote_addr": "127.0.0.1", 5 "response_time": "31.356µs", 6 "start_time": "2022/11/10 - 10:42:38", 7 "status_code": 200 8}`