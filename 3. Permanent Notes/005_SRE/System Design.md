
1. API
2. Database ( SQL vs NoSQL)
3. Scaling
4. CAP / PACLEC
5. Web Authentication / Basic security
6. Load balancers
7. Caching
8. Message Queue
9. indexing
10. Failovers
11. Replication
12. Consistent Hashing

System design framework
- Requirement
- Data types, access patterns and scale
- Design


**Requirement**
- input: Problem statement given by the interviewer
- Output: List of functional and Non-functional requirements
- Ask the right questions. Interviewer sometime intentionally with held information so that you ask the right questions
- If any random number or data is provided: It is important
**- Functional requirements**
	- core product features and use cases that system needs to support
	- Ask questions just enough to together use cases system ( Can user post, delete the tweet, update profile, follow other people, like tweet etc )
	- Treat is as a black box. 
	- Goal is to find out what needs to be built. Not how, Not scale just what
	- Identify main business object or entities and their relation
		- Media is used almost everywhere
	- Think about possible access patterns for these objects
		- given object A, get all related object B etc ( have pagination at the back of the mind )
	- Consider mutability
		- can object tweet be edited after they're published
		- can tweets be deleted, can accounts be deleted
		- what happens to tweets when an account is deleted
	- List all requirements and validate with interviewer
**- Non - Functional requirements** ( NFRs )
	- These specify how system should perform a certain function
	- Performance
		- Look into access pattern ( more reads, accessed frequently, synchronous )
	- Availability
		- 99.9999% i.e ( 6 minutes of downtime accepted )
	- Security
		- OAuth
		- Password / data encryption
		- HTTPS / TLS
	- Consistency
	- For any design decision, you should be able to talk about the trade-offs of your decision. Very important





**Date types, API and Scale**
- Input: 
	- Functional and non-functional requirements
	- Problem statement
- Output:
	- List of data types we need to store
	- Access patterns for these data types
	- Scale of the data and requests the system needs to serve
- What data types does system need to store
	- structured data: accounts, tweets, likes
	- Media and Blobs: images, videos, binary, tar, zip files
- What does API endpoint looks like
	- getTweets GET /{accountID}/tweets?page ( remember pagination )
- What volume of request do we need to support
	- Do back of envelope calculation
	- Is system read-heavy or write heavy ?

**Design**
- input
	- Functional and non functional requirement
	- problems tatement
	- list of data object or entiries
	- Access patterns ( API )
	- Scale of data and requests that system needs to handle
- Output
	- Data storage
		- Since we know "what" we are stroing, we need to know where we are storing it ?
		- blob storage ( do not name things if you do not know the answer )
		- database
			- relational vs non relational
			- entities to store
			- remember there is no right or wrong answer, just make sure to justify your decision
	- Microservice
		- How do we stora out data ?
		- How do we retrive it to the API
		- Caching
		- Load balancing
		- Queuing system
	- want speed
		- use cache
	- Want availability
		- use redundancy
	- start small


https://interviewing.io/guides/system-design-interview/part-three#about-this-3-step-framework

How
- Gather requirements
	- input
	- list functional and non functional requirement
- Data types, API and scale
	- input 
		- use function and non functional requirements
		- problem statement given by your interviewer
	- Output:
		- list data types we need to store
		- acces patterns for these data types
		- scale of data and requires the sysm needs to serve
- Design:
	- Functional and non-functional requirements.  
	- Problem statement given by your interviewer.  
	- List of Data Types we need to store.  
	- Access patterns for these data types (API).  
	- Scale of the data and requests the system needs to serve.
	- Output
		- Data storage microservice