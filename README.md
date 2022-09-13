# Backup_Recovery
set nocount on 
create database test -- large data file, 1MB log
go
create table bigtable (ColA char(8000))
GO
create proc DoTest(@RunNumber INT, @iterations INT)
AS
DECLARE @start DATETIME = GETDATE()
	, @end DATETIME
	, @counter INT = 0
WHILE @counter < @iterations
	BEGIN
		insert bigtable values ('A')
		SET @counter += 1
	END

delete bigtable

SET @end = GETDATE()

PRINT 'End #' + CAST(@RunNumber AS VARCHAR) 
	+ ' - ' + CAST(DATEDIFF(second, @start, @end) AS VARCHAR)
	+ ' seconds'
GO

EXEC doTest 1,50000

-- Open a new window:
checkpoint
dbcc sqlperf('logspace')

EXEC doTest 2,50000

backup database test to disk = 'nul'

EXEC doTest 3,50000

use master
drop database test
