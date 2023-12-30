
## Table of Contents
- [[#Websocket|Websocket]]
- [[#pion/webrtc|pion/webrtc]]
- [[#SDP information|SDP information]]
- [[#ICE candidates|ICE candidates]]
- [[#ICE Agent|ICE Agent]]
- [[#ICE restart|ICE restart]]
- [[#Trickle ICE|Trickle ICE]]
- [[#STUN (Session Traversal Utilities for NAT)|STUN (Session Traversal Utilities for NAT)]]
- [[#TURN (Traversal Using Relays around NAT)|TURN (Traversal Using Relays around NAT)]]
- [[#mDNS|mDNS]]
- [[#API endpoints `/offer`, `/answer`, `/ice`**:|API endpoints `/offer`, `/answer`, `/ice`**:]]
- [[#Signaling server server|Signaling server server]]



### Websocket
- used to establish secure bi directional communication channel between client ( browser) and server
- WS is used for signaling purpose, allow peers to exchange control message related to webRTC session
- two way communication interactive
- with WS we can send messages to server and receive event driven responses without having to poll the server for a reply


### WebRTC
- used for realtime communication
- it is a P2P connection
- Works by using **offers** and answers **between** 2 peers
- negotiation is called **signalling**
- after signaling p2p connectivity information using something called **ICE candidates**
- offers and answers are expressed using Session Description Protocol ( SDP )
-

### WebRTC lifecycle
![[Screenshot 2023-10-13 at 9.28.03 AM.png]]




### pion/webrtc
- Go library for handling webrtc communication
- Provides functionality for creating and managing webrtc connection between peers

### SDP ( Session Description Protocol )
- SDP used to describe multimedia sessions including codes, media types, transport info
- The configuration of an endpoint on a webRTC connection is called SDP
- It includes information about the kind od media being sent, its format the transfer protocol being used, the endpint's IP address and port
- Offers and answers are special description
- each peer keeps two descriptions on hand
	- local description: describing itself
	- remote description: describe the other end of the call
- SDP is used to negotiate capabilities and settings between the peers, such as type of media to be transmitted

### ICE candidates
- ICE stands for interactive connectivity establishment
- ice candidates are network endpoints used by webRTC to establish a direct connection between peers
- ICE candidates detail the available method the peer is able to communicate
- each peer will propose its best candidates first, making their way down the link toward their worse candidate
- Ideally it used UDP but ICE standard allows TCP candidates as well
- They are exchanged between peers to discover the best paths for communication especially when both are behind NAT

![[Screenshot 2023-10-13 at 9.37.45 AM.png]]


### ICE Agent
- ICE agent is a component of webrtc stack responsible for gathering ICE candidates adn managing the ICE process
- It helps to establish direct connection between peers, bypassing NATs and Firewalls

### ICE restart
-  ICE restart occurs when the ICE agent initiates a new gathering process for ICE candidates.
- This might be necessary if the initial candidates fail to establish a connection.

### Trickle ICE
- Trickle ICE is a technique where ICE candidates are exchanged as soon as they are available, rather than waiting for the entire list to be gathered.
- This allows for faster connection establishment, especially in scenarios with multiple network interfaces.

### STUN (Session Traversal Utilities for NAT)
- STUN servers are used to discover a client's public IP address and port, which can be used for establishing peer-to-peer connections.
- They help bypass NAT restrictions.

### TURN (Traversal Using Relays around NAT)
- TCP, UDP, DTLS and TLS )
- TURN servers are used as a fallback when direct peer-to-peer connections cannot be established (e.g., due to restrictive firewalls or symmetric NATs).
- - They relay data between peers to ensure communication.
- TURN supports multiple protocols for communication: TCP, UDP, DTLS, and TLS.

### mDNS
- mDNS is used for service discovery on a local network.
- In the context of WebRTC, it can be used to discover other peers on the same local network without relying on a central server.

### API endpoints `/offer`, `/answer`, `/ice`**:
- These API endpoints are used to facilitate the exchange of SDP information, ICE candidates, and other signaling data between the peers.
- The `/offer` endpoint is used to initiate a call, `/answer` is used to respond to the call, and `/ice` is used to exchange ICE candidates.

### Signaling server server
- The signaling server acts as a middleman, allowing peers to exchange signaling data (SDP, ICE candidates) needed to establish a WebRTC connection.
- - It provides a common platform for peers to find each other and initiate the call.

## References
https://www.youtube.com/watch?v=JTIm3ChI-6w