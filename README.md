SELECT DISTINCT
    OBJECT_NAME(so.object_id) AS ObjectName,
    so.type_desc AS ObjectType,
    sm.definition
FROM sys.sql_modules sm
JOIN sys.objects so ON sm.object_id = so.object_id
WHERE sm.definition LIKE '%OtherDatabaseName.%';