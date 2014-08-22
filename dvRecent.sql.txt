/******************************************************************************
 *
 * Create or update dvRecent5, the view for the first partition limited to the first 5 strings
 *
 *****************************************************************************/
 
DECLARE @iIsFirst INT
DECLARE @vchStmt nvarchar(MAX)
DECLARE @vchPartitionId NCHAR(36)
DECLARE cPartition cursor FOR
	SELECT top 1 PartitionId FROM dtPartition ORDER BY PartitionCloseTime DESC
 
SET @iIsFirst = 1
SET @vchStmt = N'create view [AdtServer].dvRecent5 as'
 
OPEN cPartition
fetch NEXT FROM cPartition INTO @vchPartitionId
while @@fetch_status = 0
BEGIN
    IF @iIsFirst = 0
        SET @vchStmt =  @vchStmt + N' union all'
    SET @vchStmt =  @vchStmt + N' select * from dvAll5_' + @vchPartitionId
    SET @iIsFirst = 0
    fetch NEXT FROM cPartition INTO @vchPartitionId
END
close cPartition
deallocate cPartition
 
IF EXISTS (
    SELECT * FROM dbo.sysobjects WHERE
        id = object_id(N'[AdtServer].dvRecent5') AND
        objectproperty(id, N'IsView') = 1)
    DROP VIEW [AdtServer].dvRecent5
 
EXEC (@vchStmt)
GO
 
 
/******************************************************************************
 *
 * Create or update dvRecent, the view for the first partition
 *
 *****************************************************************************/
 
DECLARE @iIsFirst INT
DECLARE @vchStmt nvarchar(MAX)
DECLARE @vchPartitionId NCHAR(36)
DECLARE cPartition cursor FOR
	SELECT top 1 PartitionId FROM dtPartition ORDER BY PartitionCloseTime DESC
 
SET @iIsFirst = 1
SET @vchStmt = N'create view [AdtServer].dvRecent as'
 
OPEN cPartition
fetch NEXT FROM cPartition INTO @vchPartitionId
while @@fetch_status = 0
BEGIN
    IF @iIsFirst = 0
        SET @vchStmt =  @vchStmt + N' union all'
    SET @vchStmt =  @vchStmt + N' select * from dvAll_' + @vchPartitionId
    SET @iIsFirst = 0
    fetch NEXT FROM cPartition INTO @vchPartitionId
END
close cPartition
deallocate cPartition
 
IF EXISTS (
    SELECT * FROM dbo.sysobjects WHERE
        id = object_id(N'[AdtServer].dvRecent') AND
        objectproperty(id, N'IsView') = 1)
    DROP VIEW [AdtServer].dvRecent
 
EXEC (@vchStmt)
GO