Type: #Note
Tags:  #caching
# caching .
Created date: 2023-09-21 06:10

Caching strategies
- Cache aside Or Lazy loading ( reactive approach )
- Write through ( a proactive approach )
- Read through
- Wite behind

Caching types
- in-memory
- distributed caching -> Redis, Memcached
- client side caching

Measuring cache effectiveness
- calculate cache hit rate
- analyze cache eviction rate
- monitor data consistency
- determine the right cache expiration time


**cache aside**
- if data exists in cache ( cache hit), return to the caller
- If data does not exists in cache ( cache miss), cache is populated with data retrieved from database and returned to caller
Advantage:
- Easy to implement
- cost effective
Disadvantage:
- Adds latency or overhead to initial response because of additional roundtrips to cache and database


**write through**
- reverse order of how cache are populated, instead of lazy loading after data in cache after a cache miss
- When application, batch or backend process updated primary database
- immediately afterwards, data is also updated in cache
- Always implemented with lazy loading
Advantages:
- Since it it up to date with database, much greater likely hood data will be found in cache, better performance
- database performs well as fewer request to database reads
Disadvantages:
- infrequently requested data is also written in cache resulting in larger and more expensive cache

A proper caching strategy includes both write-through and lazy loading of your data and setting an appropriate expiration for data to keep it relevant

**Write behind**
- Here data is written the cache first and then to the database
- Since data is initially written to cache, there might be some inconsistencies
- Write operation will be faster but can lead to inconsistencies if not managed properly

**read through**
- data is fetched directly from cache
- if not present, cache is designed to fetch from underlying database, populate cache with this data and return to the caller
- Useful: Operations where read operations are more frequent and data is relatively stable
- effective when overhead of fetching data from underlying data source is significant compared to cache access
- Pro
	- reduces number of direct request to database
	- can help minimize latency for frequently accessed data
- Cons
	- possibly of stale data if underlying data is updated frequently


### cache invalidation
- LRU
	- It i used to remove the least recently used item first when cache reaches its capacity
	- It keeps track of the order in which items are accessed and ensures that the most recently accessed items are kept in the cache
- LFU
	- remove least frequently accessed items from the cache when it reaches capacity
	- LFU focues on removing items that have been accessed fewest number of times
- FIFO
	- First in First out
- ARC
	- Adaptive replacement cache
- Belady's Algorithm
- Segmented LRU
- Write Once, Read Many ( WORM )
- 2 Q
- Clock Algorithm
- LIRS
	- Low inter reference recency set
- MRU
- MFU
- Size based eviction
- 




## References
1. https://medium.com/@mmoshikoo/cache-strategies-996e91c80303
2. https://docs.aws.amazon.com/whitepapers/latest/database-caching-strategies-using-redis/cache-validity.html