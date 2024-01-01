### Sliding window mechanism
#TODO



#### TCP window scaling


### TCP Segmentation Offload ( TSO )

### TCP large Receive Offload ( LRO )

###


### HOL blocking
#TODO 

### Multiplexing


### TCP slow start threshold
slow start is an algorithm used to detect the available bandwidth for packet transmission, and balances the speed of a network connection.
### TCP congestion Control

1. Client chooses congestion window `cwnd` based on maximum segment size `MSS`
2. For each packet ack, `cwnd` is doubled in size until it reaches `slow start threshold`. In some implementation threshold is adaptive.
3. After reaching slow start threshold, window increases additively for each packet ack. If packet is dropped, window is reduces exponentially until another packer is ack'd.


### Different types of congestion control mechanism
1. Fast Recovery : 
	1. cwnd reduced by 50% along with ssthresh value
	2. This would allow the network to come out of the congestion state easily.
	3. Used Reno before , overcomed by New Reno
	4. The trick was to let know the sender that it needs to reduce the cwnd by 50% for all the packets loss that happened in the same congestion window. This was done using partial ACK.
2. Selective ACK
	1. SACK is a sender and receiver side optimization to [TCP](https://www.geeksforgeeks.org/tcp-congestion-control/).
	2. Sender and receiver should support SACK
	3. Uses SACK blog to add additional information
	4. TCP SACK is particularly useful in scenarios where packet loss occurs, but not all packets are lost. It helps in reducing unnecessary retransmissions, which can lead to improved network performance.
3. Proportional Rate Reduction ( PRR )
