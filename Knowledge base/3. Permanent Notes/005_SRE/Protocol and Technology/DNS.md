Type: #Note
Tags: 
# DNS
Created date: 2023-10-12 13:48

- [[#Things to know about DNS|Things to know about DNS]]
- [[#DNS records|DNS records]]
- [[#CNAME vs DNAME|CNAME vs DNAME]]
- [[#DNS query types|DNS query types]]
	- [[#DNS query types#Iterative queries|Iterative queries]]
	- [[#DNS query types#Recursive type|Recursive type]]
- [[#DNS Servers|DNS Servers]]
	- [[#DNS Servers#Local DNS Cache|Local DNS Cache]]
	- [[#DNS Servers#Root DNS server|Root DNS server]]
	- [[#DNS Servers#DNS forwarder|DNS forwarder]]
	- [[#DNS Servers#Authoritative DNS server|Authoritative DNS server]]
	- [[#DNS Servers#Recursive DNS resolver|Recursive DNS resolver]]
- [[#What is DNSSEC ?|What is DNSSEC ?]]
	- [[#What is DNSSEC ?#DNSSEC records|DNSSEC records]]
	- [[#What is DNSSEC ?#RRset|RRset]]
- [[#Protecting DNS queries|Protecting DNS queries]]
	- [[#Protecting DNS queries#DNS Over [[TLS]]|DNS Over [[TLS]]]]
	- [[#Protecting DNS queries#DNS Over [[HTTP]]|DNS Over [[HTTP]]]]
	- [[#Protecting DNS queries#DNS over [[QUIC]]|DNS over [[QUIC]]]]
- [[#DNS Tunnelling|DNS Tunnelling]]
- [[#DNS attacks|DNS attacks]]
	- [[#DNS attacks#DNS rebind attack|DNS rebind attack]]
	- [[#DNS attacks#DNS amplification attacks|DNS amplification attacks]]
	- [[#DNS attacks#DNS cache poisoning|DNS cache poisoning]]
- [[#References|References]]

## Things to know about DNS
- DNS uses port 53 and UDP protocol by default.
- If the DNS packet size exceeds the maximum UDP size ( 65,535 bytes ) then it uses TCP as it allows larger packets ( eg: for DNSSEC, Zone transfers ).
- TTL determines how long DNS information is cached.
- DNSSEC provides authentication and integrity for DNS records.
- PTR is used to lookup Reverse DNS record. Maps IP to a domain.
- Any cast is used to route traffic to nearest DNS server for improved performance and resilience.
## DNS records
1. CNAME
	1. Alias for one domain to another
	2. allow multiple alias to point to same canonical real domain
2. A
	1. maps domain to IP address
3. AAAA
	1. maps domain to IPv6 address
4. PTR
	1. reverse dns lookup i.e resolve DNS name from IP
5. MX
	1. shows mail servers responsible for receiving email on behalf of that domain
6. TXT
	1. used to add arbitrary text 
	2. Used for certification, authentication, ownership
7. RRSIG
	1. digital signature of RRset
8. DNAME
	1. used for domain aliasing for entire domain
9. SOA
	1. contains administrative information ( primary authoritative dns server, email of admin, TTL etc)
## CNAME vs DNAME
- Operates at host level vs operates at domain level
- Creates alias for specific hostname within a domain vs used for redirecting entire domain to another domain
- Redirects individual hostname within a domain vs redirects all subdomains within a domain
## DNS query types

### Iterative queries
- Resolver follows a referral chain
- Multiple queries might be involved
- slower process
- used by recursive DNS server
### Recursive type
- server perform the entire resolution process
- single query from the resolver
- potentially faster
- used by DNS server to resolve queries on behalf of resolvers

## DNS Servers
### Local DNS Cache
Local cache in your computer

### Root DNS server


### DNS forwarder


### Authoritative DNS server
DNS server that holds DNS record for that domain or zone.

### Recursive resolver
DNS can be ISP provided DNS server or something like 8.8.8.8

### Stub resolver
DNS client ( browser, OS )



## What is DNSSEC ?
DNSSEC adds a layers of trust on top of DNS by providing authentication. It secures by adding crypto signatures to existing DNS records.
You can verify these signatures to make sure requested DNS records comes from its authoritative name servers and was not altered in route.

**Note: DNSSEC is not used to protect DNS queries, it is just used to make sure the response you got was from owner of the zone.
To protect DNS queries, you would need to use something like DoT or DoH**

### DNSSEC records
RRSIG - contains crypto signatures
DNSKEY - contains a public signing key
DS - contains hash of DNSKEY
NSEC. and NSEC3 - explicit denial of existence of DNS record
CDNSKEY ( to remember read as "C DNS KEY", some people get confuse it as DNS S KEY) and CDS - for child zone requesting update to DS in parent zone

### RRset
First step in DNSSEC is grouping of same type of DNS record into a resource record set ( RRset)

These RRset are digitally signed


## Protecting DNS queries
### DNS Over [[TLS]]
- Encrypt DNS queries
- Runs on top of TLS i.e port 853
- Network admin can see and block malicious DNS queries
- From network standpoint: this is better
- No need to make
- Since it uses TLS, initial handshake might take longer and can introduce latency
### DNS Over [[HTTP]]
- Encrypts DNS queries
- Runs on top of HTTP i.e port 443
- Network admin cannot see the query and hence cannot block it.
- From web standpoint: this is better
- Need to make sure client has implemented HTTP

### DNS over [[QUIC]]
- combination of DoT with speed and performance benefit of QUIC
- queries sent over QUIC
- 0-RTT allows faster low latency DNS lookups
- Provides source address verification during QUIC handshake procedure

## DNS attacks

### DNS rebind attack

### DNS amplification attacks
DOSing open DNS resolvers so that le

### DNS cache poisoning

### DNS Tunnelling
- Technique that allows non-DNS traffic to be encapsulated within DNS packet.



## References
1. 


