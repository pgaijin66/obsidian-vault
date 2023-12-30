
How to ensure that system is consistent even in the face of failures

1. Use transactions
2. Make service idempotent
	1. generate unique identified for each request
	2. check idempotenty : check if unique identifies has been processed before, if yes then return a response indicating the operation has already been completed
3. Logging and auditing
4. Error handling and recovery
5. Scheduled integrity check
6. Rollback and compensation actions
7. 