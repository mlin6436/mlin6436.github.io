---
layout: post
title: "Database Index Defragmentation"
tags: ["database", "defragmentation", "index", "sql", "t-sql"]
---

<div class="message">
The benefit of using index in SQL is to improve the performance when searching through tables without having to scan for each table referenced in a query. Yet as the table grows, the index expands. More devastatingly, the index becomes fragmented, which actually creates unnecessary overhead for queries.
</div>

In order to resolve the fragmentation problem on tables and indexes, I created the following T-SQL to look through all tables and indexes and attempt to defragment them.

{% highlight sql %}
USE MASTER
  ---------------- CREATE TEMPORARY TABLES ----------------DECLARE @DatabaseName NVARCHAR(100) DECLARE @TableName NVARCHAR(100)DECLARE @IndexName NVARCHAR(100)IF OBJECT_ID('tempdb..#DatabaseList') IS NOT NULL BEGIN
   DROP TABLE #DatabaseList END
CREATE TABLE #DatabaseList (
   [DatabaseName] NVARCHAR(100) NOT NULL,
) IF OBJECT_ID('tempdb..#DatabaseTableIndexList') IS NOT NULLBEGIN
   DROP TABLE #DatabaseTableIndexList END
CREATE TABLE #DatabaseTableIndexList (
   [DatabaseName] NVARCHAR(100) NOT NULL,
   [TableName] NVARCHAR(100) NULL,
   [IndexName] NVARCHAR(200) NULL,
   [Rows] BIGINT) IF OBJECT_ID('tempdb..#DatabaseTableIndexFragmentation') IS NOT NULL BEGIN
   DROP TABLE #DatabaseTableIndexFragmentationEND
CREATE TABLE #DatabaseTableIndexFragmentation(
   [ObjectName] NVARCHAR(200),
   [ObjectId] INT,
   [IndexName] NVARCHAR(200),
   [IndexId] INT,
   [Level] INT,
   [Pages] INT,
   [Rows] INT,
   [MinimumRecordSize] INT,
   [MaximumRecordSize] INT,
   [AverageRecordSize] INT,
   [ForwardedRecords] INT,
   [Extents] INT,
   [ExtentSwitches] INT,
   [AverageFreeBytes] NUMERIC(16, 10),
   [AveragePageDensity] NUMERIC(16, 10),
   [ScanDensity] NUMERIC(16, 10),
   [BestCount] INT,
   [ActualCount] INT,
   [LogicalFragmentation] NUMERIC(16, 10),
   [ExtentFragmentation] NUMERIC(16, 10)
) IF OBJECT_ID('tempdb..#DatabaseTableIndexResult') IS NOT NULL BEGIN
   DROP TABLE #DatabaseTableIndexResult END
CREATE TABLE #DatabaseTableIndexResult (
   [DatabaseName] NVARCHAR(100) NOT NULL,
   [TableName] NVARCHAR(100) NULL,
   [IndexName] NVARCHAR(200) NULL,
   [Rows] BIGINT,
   [Pages] BIGINT,
   [Density] FLOAT,
   [Fragmentation] FLOAT)---------------- GET DATABSAES ----------------INSERT INTO #DatabaseList (DatabaseName) SELECT LTRIM(RTRIM(Name))FROM MASTER.dbo.sysdatabasesWHERE Name NOT IN ( 'master', 'model', 'msdb', 'tempdb' ) ---------------- GET TABLES AND INDEXES ---------------- WHILE EXISTS ( SELECT TOP 1 1 FROM #DatabaseList ) BEGIN
   SELECT TOP 1 @DatabaseName = DatabaseName
   FROM #DatabaseList

   EXEC('
USE [' + @DatabaseName + ']
INSERT INTO #DatabaseTableIndexList ([DatabaseName], [TableName], [IndexName], [Rows])
SELECT ''' + @DatabaseName + ''', so.name, si.name, si.rows
FROM sysobjects so
INNER JOIN sysindexes si ON so.ID = si.ID
WHERE so.xtype = ''U'' AND si.rows > 1000 AND si.indid > 0 AND si.indid < 255 AND (si.status & 64)=0
')

   DELETE FROM #DatabaseList
   WHERE DatabaseName = @DatabaseName END
DROP TABLE #DatabaseList ---------------- GET INDEX FRAGMENTATION ----------------WHILE EXISTS ( SELECT TOP 1 1 FROM #DatabaseTableIndexList) BEGIN
   SELECT TOP 1
       @DatabaseName = DatabaseName,
       @TableName = TableName,
       @IndexName = IndexName
   FROM #DatabaseTableIndexList

   INSERT INTO #DatabaseTableIndexFragmentation
   EXEC('USE [' + @DatabaseName + '] DBCC SHOWCONTIG ( [' + @TableName + '] , [' + @IndexName + '] ) WITH TABLERESULTS, NO_INFOMSGS, FAST ')

   INSERT INTO #DatabaseTableIndexResult (DatabaseName, TableName, IndexName, [Rows], [Pages], [Density], [Fragmentation])
   SELECT dti.DatabaseName, dti.TableName, dti.IndexName, dti.Rows, frag.Pages, frag.ScanDensity, frag.LogicalFragmentation
   FROM #DatabaseTableIndexList dti
   INNER JOIN #DatabaseTableIndexFragmentation frag ON frag.ObjectName = dti.TableName AND frag.IndexName = dti.IndexName
   WHERE dti.DatabaseName = @DatabaseName AND dti.TableName = frag.ObjectName AND dti.IndexName = frag.IndexName

   DELETE FROM #DatabaseTableIndexList
   WHERE DatabaseName = @DatabaseName AND TableName = @TableName AND IndexName = @IndexNameEND

DROP TABLE #DatabaseTableIndexList DROP TABLE #DatabaseTableIndexFragmentation ---------------- FIX FRAGMENTATION ---------------- WHILE EXISTS ( SELECT TOP 1 1 FROM #DatabaseTableIndexResult) BEGIN
   SELECT TOP 1
       @DatabaseName =  DatabaseName,
       @TableName = TableName,
       @IndexName = IndexName
   FROM #DatabaseTablleIndexResult

   EXEC ( 'DBCC INDEXDEFRAG ( [' + @DatabaseName + '], [' + @TableName + '], [' + @IndexName + '] ) WITH NO_INFOMSGS ' )

   DELETE FROM #DatabaseTableIndexResult
   WHERE DatabaseName = @DatabaseName AND TableName = @TableName AND IndexName = @IndexName END

DROP TABLE #DatabaseTableIndexResult
{% endhighlight %}

##NOTE

- For defragmenting large tables, I will rather break them down in the `GET DATABASES` section to avoid long running job and risks of database block.

##IMPROVEMENTS

- Time stamps can be added around `FIX FRAGMENTATION` section to monitor the time consumption.
- The `#DatabaseTableIndexResult` table can be stored in a table as a record for auditing or monitoring purposes.
- A density lookup can be created to filter out the tables and indexes with low density, which means needing to defragment more urgently.
- Each section can be broken down to individual functions or stored procedures to increase reusability, i.e., separate out `FIX FRAGMENTATION` so that it can be triggered whenever necessary.

##TIPS

A query to find all indexes on a database.  

{% highlight sql %}
SELECT
   o.name,
   indexname=i.name,
   i.index_id,
   user_seeks,
   user_scans,
   user_lookups,
   reads=user_seeks + user_scans + user_lookups,
   writes =  user_updates,
   rows =(SELECT SUM(p.rows) FROM sys.partitions p WHERE p.index_id = s.index_id AND s.OBJECT_ID = p.OBJECT_ID),
   CASE WHEN s.user_updates < 1 THEN 100 ELSE 1.00 *(s.user_seeks + s.user_scans + s.user_lookups) / s.user_updates END AS reads_per_write,
   'DROP INDEX ' + QUOTENAME(i.name) + ' ON ' + QUOTENAME(c.name) + '.' + QUOTENAME(OBJECT_NAME(s.OBJECT_ID)) AS 'drop statement',
   (
       SELECT (8 * SUM(aa.used_pages))/1024 AS 'Indexsize(MB)'
       FROM sys.indexes AS ii JOIN sys.partitions AS pp ON pp.OBJECT_ID = ii.OBJECT_ID AND pp.index_id = ii.index_id
       JOIN sys.allocation_units AS aa ON aa.container_id = pp.partition_id
       WHERE ii.name = i.name
       GROUP BY ii.OBJECT_ID,ii.index_id,ii.name
   ) AS size FROM sys.dm_db_index_usage_stats s INNER JOIN sys.indexes i ON i.index_id = s.index_id AND s.OBJECT_ID = i.OBJECT_ID INNER JOIN sys.objects o ON s.OBJECT_ID = o.OBJECT_ID INNER JOIN sys.schemas c ON o.schema_id = c.schema_id WHERE OBJECTPROPERTY(s.OBJECT_ID,'IsUserTable') = 1 AND s.database_id = DB_ID()  --AND i.type_desc = 'nonclustered'AND i.is_primary_key = 0 AND i.is_unique_constraint = 0 --AND (SELECT SUM(p.rows) FROM sys.partitions p  WHERE p.index_id = s.index_id AND s.object_id = p.object_id) > 10000
--AND (user_seeks + user_scans + user_lookups)=0 ORDER BY reads_per_write, name
{% endhighlight %}
