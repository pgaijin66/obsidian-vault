```
info There appears to be trouble with your network connection. Retrying... 
```


```
 => [deps 5/5] RUN yarn install --frozen-lockfile                                                                                                                         86.5s
 => => # yarn install v1.22.19                                                                                                                                                 
 => => # [1/5] Validating package.json...                                                                                                                                      
 => => # [2/5] Resolving packages...                                                                                                                                           
 => => # [3/5] Fetching packages...                                                                                                                                            
 => => # info There appears to be trouble with your network connection. Retrying...                                                                                            
 => => # info There appears to be trouble with your network connection. Retrying...   
```

https://github.com/nodejs/docker-node/issues/602



Everytime the message `There appears to be trouble with your network connection. Retrying...`  
is output on console, there is a RST packet sent from my PC to npm server.  
You know, RST packet is to forcibly close TCP connection.  
But it is sent from client PC.  
I feel strange about this.

Next, I wondered what causes RST packet sent from my PC.  
I found that every time before RST packet is sent, there are packets indicating `TCP ZeroWindow`, meaning data receiving entity (in this case it is my client PC) tells to a sender to stop sending packet until the receiver allow to do it.

After sender receives packets indicating `TCP ZeroWindow`, client must send `TCP Window Update` to server in order to resume TCP communication.

But I couldn't find those `TCP Window Update` packet sent from my client PC.  
The npm server kept waiting to be allowed to send data but my client PC didn't tell to do so.  
Then it is timed out and RST packet was sent from my PC.

Apparently, the root cause is not to send `TCP Window Update` packet to resume communication from client.  
As I haven't had problem downloading large files from internet, I suspect the issue is in network code in node binary compiled for Windows.

https://stackoverflow.com/questions/65181012/does-alpine-have-known-dns-issue-within-kubernetes


