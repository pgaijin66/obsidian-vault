Type: #MOC

# SRE

1. SRE non technical skills
	1. Ability to lead
	2. Take charge
	3. Expression opinions in non conflicting way
	4. Lead
2. Cloud Computing and Infrastructure:
	1. Understanding cloud computing concepts (AWS, Azure, Google Cloud Platform)
		1. AWS
			1. EC2
			2. VPC
			3. Route53
			4. Auto scaling
			5. EKS
			6. EBS
			7. S3
			8. Lambda function
			9. API Gateway
			10. ECS
			11. 
		2. GCP
		3. Azure
	2. On-prem and Hardware
		1. Baremetal configuration
		2. File systems
			1. ZFS
			2. NTFS
			3. EXT4
			4. RAID
		3. RAM
	3. Virtualization and containerization 
		1. Docker
			1. 
		2. [[Kubernetes]]
	4. CI/CD
		1. Jenkins
		2. [[ArgoCD]]
		3. Github Actions
	5. Infrastructure as Code
		1. Terraform
	6. Configuration Management 
		1. [[Ansible]]
3. Operating System
	1. Linux
	2. Memory management
	3. Process management
	4. Commands
	5. Netfilter and Iptables
	6. Compiler
4. Site Reliability Engineering (SRE) principles and best practices
	1. [[Eliminating Toil]]
	2. Service Level Objectives (SLOs) and Error Budgets.
	3. Incident management and post-incident analysis.
	4. Root Cause Analysis
	5. 
5. Programming and Testing
	1. Programming
		1. Python
		2. [[Go]]
		3. Rust
		4. Typescript
	2. Testing
		1. [[TDD ( Test Driven Development )]]
		2. [[Table Driven Testing]]
		3. 
6. Monitoring and Observability:
	1. Setting up monitoring systems (Prometheus, Grafana)
	2. Log management and analysis (ELK stack, Splunk).
	3. Distributed tracing (Jaeger, OpenTelemetry).
	4. Automation and Scripting:
	5. Scripting languages (Python, Bash, PowerShell).
		1. [[Bash]]
		2. Fish
		3. Zsh
	6. Configuration management (Chef, Puppet, SaltStack).
	7. Continuous Integration/Deployment (CI/CD) pipelines (Jenkins, GitLab CI/CD).
7. Networking 
	1. TCP/IP networking fundamentals.
		1. DHCP
		2. DNS
			1. [[CoreDNS]]
		3. [[TLS]]
		4. [[TCP]]
		5. IP
		6. UDP
		7. ICMP
		8. IGMP
		9. 
	2. Routing
		1. OSPF
		2. RIP
		3. FRR
		4. EIGRP
		5. BGP
	3. Addressing
		1. Unicast
		2. Anycast
			1. [[CDN]]
		3. Broadcast
			1. DHCP
			2. ARP
		4. Multicast
			1. Video streaming
			2. mDNS
			3. IGMP = Used to report multicast group membership to neighbors.
			4. IGMP Snooping: Enables switches to intelligently forward multicast traffic to the ports where there are interested receivers
	4. Load balancing and reverse proxy (NGINX, HAProxy).
		1. Load balancing types
			1. Round robin
				1. Simple
				2. Weighted Round Robin: based on servers traffic handling capacity
				3. Dynamic Round Robin: Weight assigned to server dynamically
			2. Session affinity or sticky session
			4. IP Address affinity
			5. Geo location
			6. Fastest: LB keeps track of response time to each web servers
			7. Least connection: LB keeps track of connections to web servers
		2. Load balancing on each layer
			1. L3
				1. ECMP
			2. L4
				1. TCP/UDP load balancer
			3. L7
				1. HTTP Load balancing
		3. Proxies
			1. Forward proxy
			2. Reverse proxy
				1. Nginx
				2. Traefik
				3. HAProxy
				4. Envoy
	5. Security best practices (SSL/TLS, firewalls, intrusion detection).
		1. [[Tech talk Oct 7 2023]]
		2. TLS
			1. [[TLS]]
			2. [[mutualTLS]]
	6. CDN
	7. VPN
		1. Wireguard
		2. OVPN
		3. IPSsec
8. Databases and Storage:
	1. Relational databases (MySQL, PostgreSQL, Oracle).
	2. NoSQL databases (MongoDB, Cassandra, Redis).
		1. Mongo (CA)
		2. Postgres ( CP )
		3. Cassandra ( AP )
		4. Redis
	3. ACID
	4. CAP
	5. Failure modes
	6. N+1 Problem
	7. Normalization
	8. ORM
	9. Profiling performance
	10. Distributed storage systems (Amazon S3, Google Cloud Storage).
9. Performance Optimization and Scalability:
	1. Application performance monitoring and tuning.
	2. Horizontal and vertical scalability techniques.
	3. [[1. Fleeting Notes/Caching]]
10. Incident Response and Disaster Recovery:
	1. Incident response planning and execution.
	2. Business continuity and disaster recovery planning.
	3. [[Backup and restoration strategies.]]
11. Branching strategies
	1. [[Branching strategies]]
12. Leadership and Communication:
	1. Team leadership and collaboration
	2. Effective communication and presentation skills.
	3. Project management methodologies (Agile, Scrum).
13. Professional Development:
	1. Stay updated with the latest industry trends and technologies.
	2. Attend relevant conferences, webinars, and workshops.
	3. Engage in online communities and forums.
14. [[SRE Interview Questions ( MOC )]]
15. [[Web ( MOC )]]
16. [[Software Engineering ( MOC )]]




