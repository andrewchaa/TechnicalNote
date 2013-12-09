#SQL Server

## Sql job
### Creating a job

* Go to sql server agent
* Right click on Job and create a new job

#### Job
* Name: EventPublisher Pulse
* Description: Calls the stored procedure PulseMessage, that inserts a 'alive' message in the EventQueue
* Owner: Domain/Job
* Every 2 minute
Steps
* Name: Call PulseMessage
* Database: Measure
* Advanced / On success action: Quit the job reporting success
* Advanced / On failure action: Quit the job reporting failure
Schedule
* Name: Schedule
* Frequency: daily, every 2 minutes

## Service broker

### Check if it is enabled
```sql
SELECT name, is_broker_enabled, * FROM sys.databases WHERE name = 'IdentityTestDatabase'
SELECT name, is_broker_enabled, * FROM sys.databases
```

### Enable it
```sql
ALTER DATABASE Huddle SET ENABLE_BROKER WITH ROLLBACK IMMEDIATE 
```

### View message 
```sql
SELECT CAST(message_body AS varchar(MAX)) message, * FROM dbo.Identity_EventStreamQueue
```

### Turn off poison message handling
```sql
ALTER QUEUE [dbo].[Identity_EventStreamQueue] WITH STATUS = ON , RETENTION = OFF, POISON_MESSAGE_HANDLING (STATUS = OFF) 
```

### To drain all the messages
```sql
RECEIVE CONVERT(VARCHAR(MAX), message_body) AS message, message_type_name, conversation_handle FROM Identity_EventStreamQueue
```