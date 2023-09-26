# Impact_notes

# Spark customer data repo
	Historical - 70 million processed in 30 mins
	Incremental - 1 million per month in 5-10 mins

# Company wide ELK platform setup for log management

# 20% CPU load reduction -  Tabular business data module 
	a) query tuning using pt-query-digest tool report
	b) scheduled job to flag any out of sync/missing index across multiple running multitenant deployments

# JVM Heap tuning
300 MB RAM savings out of 4GB heap JVM in production using String Deduplication in G1 feature with acceptable overhead of 4 min total pause time in 13 days of uptime.

# Bias for action driven increase sales of B2C MVP Use case 
Fasten sales conversion cycle for AutoFill address integration with shopify merchants using plug n play shopify in house custom app.

# IMPACT-Details
SQL queries tuning
	How to tune
	1) STRAIGHT_JOIN based control of exec. plan
	2) while using group by over one column, prefer order by over same column
	3) on querying facility specific data, apply facility specific filter condition
	4) change column order in index to improve index leaf node scan performance 
	5) all  "columns" fetched in query returned from index instead of table lookup.
	6) removing some unnecessary joins corrected the queryplan to join tables in performant order
	How to monitor gain
	1) flow level http response time percentiles over a 2 week period before/after
	2)

Scheduled job to regularly look for tables that need their index statistics updated.
	1) query basis last_update threshold
	SELECT database_name, table_name, n_rows, last_update
	          FROM mysql.innodb_table_stats
	         WHERE database_name = 'uniware';

	2) control over the quality of the statistics estimate using - "innodb_stats_transient_sample_pages"         


String dedup notes
	1) focussed on de duplicating long-lived strings @ JVM startup (within JVM cache/configuration layer)
	2) deduplicate candidate threshold was set to 15 instead of default 3 to delay de duplication as far as possible just to further delay pause time.
	3) Workflow to test the savings
		1) for both with and without "dedup string " jvm flag:
			1.1 capture all objects heap histo and live objects heap histo


Spark
needed different read side partitions(too many sources) and write site partitions(single sink) - used coalesce() over repartition()
no of tasks(for corresponding spark job->stage) === equal to no of cores available to spark cluster/sc.defaultParallelism
On OOM, increase the numbers of partitions beyond the number of available executors

DataFrame over RDD
Project Tungsten + Catalyst optimizer for Query optimization
Serialization happens in memory in binary format, no java serialization/deser + 
Avoids garbage collection when creating or destroying object

use coalese over repartition when decreasing no of partitions
minimized data movement





