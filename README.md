## 1. customer data repo using apache Spark 
Historical - 70 million (55 million unique users) processed in 30 mins
Incremental - 1.5 million records(1 million new users) per month in 5-10 mins

## 2. Company wide ELK platform for different log sources: (tomcat access logs, egress api hits logs, tenant triggered exports logs)

## 3. 20% CPU load reduction -  Tabular business data module 
##### 3.1 Query tuning using pt-query-digest tool report
1. Corner cases where mysql query plan optimiser choice of join order is not performant - STRAIGHT_JOIN based control of exec. plan
2. order by
https://use-the-index-luke.com/sql/sorting-grouping
   #### challenges
   1. sorting is cpu intensive
   2. need to read complete input before producing first output. Problem for large data sets.
   3. index range scans can complicate things further.  A FULL TABLE SCAN with an explicit sort operation might be even faster in this case
   4. where clause affects also needs to be throughly investigated.
   #### Solution
    1. Prefer using indexed column for order by will avoid sorting expensive operation as sql index is an ordered data structure. Also it is a pipeline operation and suits paginated query results.
       
4. while using group by over one column, prefer order by over same column
5.  on querying facility specific data, apply facility specific filter condition
6. change column order in index to improve index leaf node scan performance
7. all  "columns" fetched in query returned from index instead of table lookup.
8.  removing some unnecessary joins corrected the queryplan to join tables in performant order
	
##### How to monitor gain
1) monitor http response time percentiles over a 2 week period before/afte

#### 3.2 Scheduled job to regularly look for tables that need their index statistics updated.
	1) query basis last_update threshold
	SELECT database_name, table_name, n_rows, last_update
	          FROM mysql.innodb_table_stats
	         WHERE database_name = 'uniware';

	2) control over the quality of the statistics estimate using - "innodb_stats_transient_sample_pages"
	b) scheduled job to flag any out of sync/missing index across multiple running multitenant deployments

## 4. JVM Heap tuning
300 MB RAM savings out of 4GB heap JVM in production using String Deduplication in G1 feature with acceptable overhead of 4 min total pause time in 13 days of uptime.
1. focussed on de duplicating long-lived strings @ JVM startup (within JVM cache/configuration layer)
2. deduplicate candidate threshold was set to 15 instead of default 3 to delay de duplication as far as possible just to further delay pause time.
3. Workflow to test the savings
   1. for both with and without "dedup string " jvm flag: capture all objects heap histo and live objects heap histo

## 5. Bias for action driven increase sales of B2C MVP Use case 
Fasten sales conversion cycle for AutoFill address integration with shopify merchants using plug n play shopify in house custom app.
