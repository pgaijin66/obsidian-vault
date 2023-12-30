1.  Use a library to build an OAuth 2 authorization server. Implement the various flows according to the OAuth spec.
2.  Scrape data from the web. Clean it up. Load it into a data analysis tool. Then, build some visualizations. For example, try making word clouds of Wikipedia articles.
3.  Create a Dockerfile & docker-compose.yml for a web application with a database, cache layer, nginx/Apache server, backend API, & JavaScript frontend.
4.  Write a bash/zsh script that traverses directories, iterates through files, and runs some commands against those files. Make sure the script can take arguments and settings flags.
5.  Develop a frontend component that opens a popup, accepts user input, and passes the information back to the main page. Also, try developing an `iframe` that can be embedded on a site and pass information back.
6.  Set a secure session cookie (server-only) that contains information about a user’s allowed actions as a JWT.
7.  Pass sensitive information back and forth between two APIs, using an HMAC to sign the data.
8.  Implement a Sudoku solver that uses concurrency/threads to quickly complete the matrix.
9.  Implement a queue that supports popping from the left and right using manual memory allocation in a close-to-the-metal language.
10.  Run regression models against census data to make predictions about the U.S. population.
11.  Get a Raspberry Pi and implement a simple web server to turn on/off a light with an API request.
12.  Write and orchestrate Airflow tasks to pull recent tweets from Twitter (using a schedule or a command), concurrently for various Twitter handles. Save the tweets to a data store. Bonus points for adding a map-reduce step for analysis.
13.  Build a timeseries database and API for accepting and storing logs from an application. Handle concurrent requests with some type of queue. Implement Elasticsearch for quick searches.
14.  Write and install your own simple program for the command line. Add your program to `$PATH` . Bonus points: host it online and support `wget` , `curl` , or even `brew install`
15.  Deploy any/all of the above projects to a major cloud provider (AWS, GCP, Azure, Digital Ocean, etc) and learn about how the various cloud services work together, provisioning instances, etc. See if you can set up CI/CD to the cloud. (Warning: this can be expensive if you’re not careful, so make sure you do your research!)
16.  Write unit, integration, and end-to-end tests for your applications. Practice mocking, parametrization, and small tests that run quickly. Run the tests on a CI provider like CircleCI, TravisCI, Jenkins, etc.
17.  Learn PostGIS and start running SQL commands against geospatial data. Return the data in GeoJSON format. Use something like MapboxJS to render the results.
18.  Expose a GraphQL API for a data set. Or, add an Apollo data layer to a frontend project to buffer requests to a REST API.
19.  Create an AI that always wins or draws at tic-tac-toe (never loses). Could use a simple min-max algorithm to win the game. Test your API programmatically with all possible user inputs (basically, play all possible games).
20.  Write a program that opens an image, finds the most prevalent color, performs a flood fill on that color to another color, and writes the output. Now, make the program work concurrently on many images.
21. Built multiple microservices to migrate legacy monolith applications over to Kubernetes in AWS EKS which made feature updates faster and improved developer experience.  
- Designed and developed APIs for IAS pre-bid and post-bid platform using Spring boot with a MySQL backend running in Tomcat. This project enabled IAS’ clients to integrate with our targeting segments quickly.  
- Created a new microservice in AWS via ECS/Fargate. It was called Reporting API and it allowed clients to request reports programmatically.  
- Worked on integrating systems developed by the acquired company (Admantx). We had to receive their data and incorporate it as part of our pre-pid feature set.  
- Created multiple mock services with Spring Boot and Cucumber framework to improve development in non-production environment and enhance automated tests.  
- Mentored and helped new engineers integrate in IAS as part of the co-pilot program.  
- Created automated test-suites in Python for regression and acceptance tests.  
- Dockerized an existing monolith Spring application to improve developer experience. This project won in the company hackathon.  
- Conducted bi-weekly Lunch and Learn sessions for the team to present and discuss new technologies, and brainstorm improvement for our current tech stack.
- - Created headless XVFB and Chrome/Firefox test framework to speed up testing time by 80%.  
- Created a reusable testing environment for different products using Puppet that spins up AWS EC2 environments on demand.  
- Refactored legacy regression suite and cut testing time from 90 to 25 minutes.  
- Released products to production.