USE YourDatabaseName; -- Replace with your database name

DECLARE @SchemaName NVARCHAR(128) = 'YourSchemaName'; -- Replace with your schema name

-- Create a temporary table to store results
CREATE TABLE #TableInfo (
    TableName NVARCHAR(128),
    ColumnCount INT,
    RowCount INT
);

-- Iterate through tables in the specified schema
DECLARE @TableName NVARCHAR(128);
DECLARE @SQL NVARCHAR(MAX);

DECLARE table_cursor CURSOR FOR
SELECT table_name
FROM information_schema.tables
WHERE table_schema = @SchemaName AND table_type = 'BASE TABLE';

OPEN table_cursor;

FETCH NEXT FROM table_cursor INTO @TableName;

WHILE @@FETCH_STATUS = 0
BEGIN
    -- Count columns
    SET @SQL = N'SELECT @ColumnCount = COUNT(*)
                 FROM information_schema.columns
                 WHERE table_schema = @SchemaName
                 AND table_name = @TableName';
    
    DECLARE @ColumnCount INT;
    EXEC sp_executesql @SQL, N'@ColumnCount INT OUTPUT, @SchemaName NVARCHAR(128), @TableName NVARCHAR(128)', @ColumnCount OUTPUT, @SchemaName, @TableName;

    -- Count rows
    SET @SQL = N'SELECT @RowCount = COUNT(*)
                 FROM ' + QUOTENAME(@SchemaName) + '.' + QUOTENAME(@TableName);

    DECLARE @RowCount INT;
    EXEC sp_executesql @SQL, N'@RowCount INT OUTPUT', @RowCount OUTPUT;

    -- Insert into temporary table
    INSERT INTO #TableInfo (TableName, ColumnCount, RowCount)
    VALUES (@TableName, @ColumnCount, @RowCount);

    FETCH NEXT FROM table_cursor INTO @TableName;
END;

CLOSE table_cursor;
DEALLOCATE table_cursor;

-- Select the results
SELECT * FROM #TableInfo;

-- Drop the temporary table
DROP TABLE #TableInfo;
