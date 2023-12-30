Tags: #mongdb #tuning


1. Indexing
2. Monitoring available connections
3. MongoDB profiling
4. Transparent huge pages (THP) on Linux
5. Use SSD based service to store files
6. Use explain keywords to determine which query planner fields consume the most resource
7. Use tailable cursors where connections have to remain open
8. Monitor locking fields globalLock.currentQueue.total and globalLocal.totalTime
	1. If it is continuously high, investigate the resource and queries
2. For read heavy apps, increase size of your replicaset
3. For write heavy apps, deploy shardings
4. Update MongoDB drivers, as it impacts efficiency of the connections
5. Install every MongoDB instance on a new system, multiple database instances and should not run on a single host
6. Take frequent backup and test by restoring them
7. 