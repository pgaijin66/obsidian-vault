1. What happens when you type google.com into your browser's address box and press enter ?
	1. when 'g' is pressed , auto completion fires if enabled and based on search history, bookmark, cookies it provides some suggestion. Once we are press enter,  
	2. Parses URL, checks if it is a URL or a search term ? If no protocol or a valid domain is given, browsers uses its default search engine to search for given information
	3. Checks HSTS check. This one is interesting, as browsers are loaded up with HSTS list. These are the list of websites that have requested to be contacted using HTTPS only.
	4. DNS lookup
		1. Checks DNS in local cache i.e browser cache
		2. If not found, checks `gethostbyname` to do a domain lookup against the OS hosts file
		3. If not found, request is send to DNS sever configured in the network stack. This is typically the gateway router.
		4. Once server is found, UDP connection is established at port 53. 
		5. If response is too large, TCP is used.
		6. If local DNS server /gateway does not have it, then recursive DNS query is requested and follows up the list of DNS servers until SOA is reached and answer is returned.
	5. TCP connection ( opens a socket )
		1. Once server is found, a TCP socket stream is established using 3 way handshake. 
		2. Request for socket is sent to Transport layer where **segment** is crafted. The destination port is added to the header, and a source port is chosen from within the kernel's dynamic port range (ip_local_port_range in Linux)
		3. Segment is sent to Network layer which adds additional IP header. The IP address of the destination server as well as that of the current machine is inserted to form a **packet**.
		4. Packet is then send to Data Link layer where frame header is added where MAC address of the machine's NIC as well as the MAC address of the gateway (local router). As before, if the kernel does not know the MAC address of the gateway, it must broadcast an ARP query to find it.
		5. Now packet is ready to be sent
		6. packet is sent to gateway router, and then to border routes with other AS numbers and finally to destination server.
		7. TTL decreases at each hop and packet is dropped if TTL reaches Zero.
		8. This process happens multiple times as per the TCP handshake
		9. (SYN) Client chooses ISN ( Initial Sequence Number ) and sends packet to server with SYN bit to indicate it is setting the ISN
		10. (SYN+ACK) Server receives SYN and chooses its own ISN. Server sets SYN to indicate it is choosing ISN as well. Server also copies clients ISN+1 to its ACK fiend and adds ACK flags to indicate it is acknowledging receipt of the first packet
		11. (ACK) Client then acknowledges the connection by sending a packet. Increases its own ISN and increases received ACK number.
		12. Now connection is established and data is ready to be transferred.
		13. Once data is transferred, closer sends a FIN packet. 
		14. Other side ACKs the FIN with its own FIN packet
		15. Closer ACKs other's FIN with its own ACK
		16. If packet is dropped at any point, then [[TCP]] congestion control mechanism kicks in. Newer OS uses `New Reno` or `cubic`
	6. TLS handshake ( Read in detail in TLS section )
		1. client sends ClientHello with its TLS version, list of cipher suits, algorithm and compression methods
		2. Server then replies with `ServerHello` with TLS version, selected cipher, selection algorithm and compression mechanism. Server also sends its public key.
		3. Client verifies server certs with its trusted CAs, if trust is established based on CA, the client generates a pseudo random bytes and encrypts with server public key.
		4. Server decrypts the random bytes using its private key and uses these bytes to generate its own copy of symmetric key
		5. Both client and server compute shared symmetric key independently.
		6. Client sends a `Finished` message to server, encrypts a hash of transmission up to this point with the symmetric key
		7. Server generates its own hash, then decrypts the client sent hash to verify it matches. If it does, it sends it own `Finished` message to client encrypting with own symmetric key.
		8. Now all data transfer is encrypted using the shared symmetric key
	7. HTTP request
		1. Sends request GET, POST etc with headers and parameters
		2. Gets response back in response `body` with HTTP headers
	8. Rendering
		1. HTML parses
		2. Creates DOM layout, page layout, composition, rendering 
		3. Repaint reflow
2. Which linux distribution
	1. Debian ( Home Lab )
		1. Stable and Reliable
		2. Does not change much
		3. Debian social contract
3. What happens when you type `ls` on terminal ?
	1.  Shell reads what we typed using `getline()` function called `strtok()`
	2. Tokenized the command and checks if 1st token `ls` is a shell alias or not
	3. If it not a built-in function,. shell will find `PATH` directory and checks for `ls`
	4. Once it finds ls, program is loaded into memory and a system call `fork()` is made.
	5. This created a child process as ls and shell will be parent process
	6. `fork()` returns 0 to child process so it knows it has to act as a child and returns PID of child to parent process
	7. `ls` process executed system call `execve()` that will give it a brand new address space within the program that i has to run.
	8. `ls` can start running its program
	9. `ls` utility uses a function to read directories and files from the disk by consulting the underlying filesystem's `inode` entries
	10. once `ls` process is done executing, it calls `_exit()` system call with an integer `0` that denotes a normal execution and kernel will free up its resources.
4. Explain linux inodes
5. Crash vs Panic
6. Explain /proc filesystem
7. When i get filesystem full error but df shows free space
8. Performance monitoring tools
9. Explain linux file system
10. Kernel space vs user space
11. How would you troubleshoot high i/o issue
12. What are processes and threads ?
13. Explain kernel memory management
14. Different types of task status
15. Linux concurrency and race conditions ?
16. Stack vs heap in OS
17. Explain Memory leak
18. How linux handles interrupts ?
19. Explain load average 
20. What happens when you type ssh until login prompt. Also mention DNS resolution
21. TCP and UDP headers


###   
Linux and Networking

-   Memory management in Linux: Deep dive into physical and virtual memory. How kernel interacts with memory? What happens in case of page fault? How to deal with dirty pages?
    
-   Handling memory issues:
    
-   Getting alerted on DIMM chip failures
    
-   Keeping track of used memory
    
-   Preparing for OOM events
    
-   Getting alerted on memory issues 
    
-   Discussion on critical interview questions:
    
-   What is thrashing?
    
-   What kind of memory pages will thrash depending on whether you have swap enabled or not?
    
-   How do you tell if a host is computationally-bound or I/O bound?
    
-   Deep dive into CPU and processes: Metrics to track CPU performance. Why disk I/O is important?
    
-   Crack bash scripting questions: Learn pro tips and trick questions
    
-   Get efficient with command line: Pro tips on pipes, Tmux, nc, and file redirection  
    

2

### Containers and Orchestration

-   Comprehensive coverage of Docker and Kubernetes architecture: Learn how to perform a live upgrade of an application with zero downtime
    
-   Deep dive into k8s: Horizontal Scaling, Load Balancing, Crash Protection, Tiered Networking, Resource Control, and Optimization and Security
    
-   How to approach common interview questions such as:
    
-   Usage of Docker volume for persisting data
    
-   How to evaluate systems’ tolerance for failures/outages?
    
-   What are the different techniques to scale a relational database?
    
-   Application deployment: Local vs. Managed k8s 
    
-   Kubernetes patterns for designing web applications: Sidecar pattern, Ambassador pattern, etc.
    
-   Important questions and pro tips on troubleshooting Kubernetes
    
-   How to set customer expectations? Deep dive into Service-Level Objectives and Service-Level Indicators
    

3

### Deployment & Configuration Management

-   A top-down view of modern software release: In-depth understanding of how CI/CD works (Continuous Integration and Continuous Deployment). How automation helps achieve CI/CD?
    
-   Deep dive into Jenkins: Installation and configuration, Jenkins Plugins, Blue Ocean & Jenkinsfile, and managing and scaling Jenkins 
    
-   Comprehensive coverage of critical interview questions:
    
-   Jenkins user authentication and security measures?
    
-   What happens when the underlying node of a particular job is offline? 
    
-   Best practices and pro tips in Jenkins node allocation
    
-   How to design a system responsible for continuous integration and deployment?
    
-   Comprehensive coverage of configuration management: Compare different tools available in the market, their advantages and features 
    
-   Infrastructure as code: Why, when, how?
    

4

### Non-Abstract Large System Design

-   How to design large-scale distributed systems like Google Adwords. Deep dive into the architecture, building blocks of scalable systems, scalability, and reliability
    
-   Interesting follow-up questions on the fundamentals of modern software systems: Servers, agents, load balancer, Storage, indexer, consensus, pipeline, queues, sharding, replication, caching, batching, and scatter-gather
    
-   Deep-dive discussion of SRE-specific interview questions:
    
-   How do SLOs (service-level objectives) impact designs?
    
-   How to do capacity estimates?
    
-   How to design for fault tolerance?
    

5

### Monitoring & Troubleshooting

-   Monitoring and alerting: Key metrics and four golden signals (errors, saturation, latency, and traffic)
    
-   Derive SLO of a system from SLI and learn how to implement a proactive SLO for an application for alerting purposes
    
-   Deep dive into Prometheus, an open-source monitoring tool
    
-   Questions on logging and log management:
    
-   How to manage logs for various use cases? How to budget for long-term log storage?
    
-   Design a logging framework for an organization: Depth of logging, retention, access and audit controls, and encryption
    
-   Incident management: Lifecycle of an incident, KPIs like MTTD, MTTI and MTTR, and pro tips for incident management process 
    
-   Testing for failure: Understand the importance of Smoke tests, Stress tests, Perf tests, etc. 
    
-   Various troubleshooting scenarios and strategies: Leverage utilities like top, vmstat, iostat, mpstat, netstat, ping, sar, tcpdump, traceroute, dig, nslookup, etc.
    

6

### Cloud Computing & AWS Services

-   AWS Compute Services (EC2, EKS, Lambda)
    
-   AWS Storage and Database Services (S3, RDS, Aurora, Dynamo and ElastiCache)
    
-   AWS Management and Governance services (CloudWatch, CloudFormation)
    
-   Networking Architecture_‍_