# 1. Active account's balance to the average balance of all actie account of the same type.
```
select a.id, a.type, a.status, a.amount, (t.type_avg-a.amount) difference
from  account a 
 left outer join (select type
            ,sum(amount) as type_sum
            ,avg(amount) as type_avg
        from account 
        where status = 'Active'
    group by type) t on t.type = a.type
where a.status = 'Active';
```
```
 	a.id	a.type	a.status	a.amount	difference
1	A100000	Basic Checking	Active	44539	5529
2	A100002	Basic Checking	Active	11483	38585
3	A100003	Student Checking	Active	26132	24127.631578947367
4	A100007	Student Checking	Active	71661	-21401.368421052633
5	A100009	Savings	Active	65536	-17498.36363636364
6	A100010	Savings	Active	40778	7259.6363636363603
7	A100011	Savings	Active	57953	-9915.3636363636397
8	A100013	Basic Checking	Active	45654	4414
9	A100016	Basic Checking	Active	639	49429
10	A100018	Basic Checking	Active	63563	-13495
```

# 2. Create external table
```
CREATE EXTERNAL TABLE employee(
 id 		int,
 fname 		string,
 lname		string,
 address 	string,
 city 		string,
 state 		string,
 zip		string,
 birthday	string,
 hireday	string
)  
row format delimited 
fields terminated by ','
location 'hdfs://localhost:8020/user/training/problem2/data/employee';
```
# 3. nagative account balances

select a.custid, c.fname , c.lname, c.hphone
from account a 
    ,customer c
where a.amount < 0.0 
and a.custid = c.id;
```
 	a.custid	c.fname	c.lname	c.hphone
1	10001	Sybil	Wiley	(504) 780-0366
2	10010	Brittany	Martinez	(341) 462-0222
3	10011	Merritt	Roth	(341) 344-3753
4	10015	Amaya	Weeks	(979) 852-8703
5	10023	Jermaine	Barnes	(245) 674-2743
6	10026	Lacy	Rios	(227) 499-8358
7	10036	Laith	Kirk	(312) 466-1766
8	10045	Chancellor	Hart	(913) 147-2294
9	10046	Olga	Hall	(615) 645-2920
10	10053	Jonah	Alvarado	(578) 584-0882
```

# 4. Combine the two customer list into a single dataset
```
CREATE EXTERNAL TABLE employee1 (
id string,
fname string,
lname string,
address string,
city string,
state string,
zip string
) 
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t;'
LOCATION 'hdfs://localhost:8020/user/training/problem4/data/employee1'
;

select * from employee1 limit 10;
```
```

 	employee1.id	employee1.fname	employee1.lname	employee1.address	employee1.city	employee1.state	employee1.zip
1	10000000	Olga	Booker	Ap #643-2741 Proin Street	Gresham	OR	42593-0000
2	10000001	Raja	Spence	P.O. Box 765, 7700 Eros Rd.	Duluth	MN	67110-0000
3	10000002	Meredith	Schwartz	3414 At Road	San Antonio	TX	35713-0000
4	10000003	Thor	Lloyd	2703 Amet, Road	Rock Springs	WY	73861-0000
5	10000004	Arden	Mooney	Ap #888-1187 Aliquam Road	Rockville	MD	82478-0000
6	10000005	Kenneth	Lucas	Ap #637-8746 Feugiat Av.	Boston	MA	84047-0000
7	10000006	Leonard	Goodwin	Ap #465-7707 Aliquam Avenue	Rock Springs	WY	91337-0000
8	10000007	Elton	Conrad	P.O. Box 787, 8889 Nam Street	Auburn	ME	28280-0000
9	10000008	Ryan	Keith	6857 Et Ave	Springfield	MA	58872-0000
10	10000009	Griffin	Combs	Ap #266-8677 Nulla Street	Ketchikan	AK	99904-0000

 ```
CREATE EXTERNAL TABLE employee2 (
id string,
num string,
lname string,
fname string,
address string,
city string,
state string,
zip string
) 
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
LOCATION 'hdfs://localhost:8020/user/training/problem4/data/employee2'
;

```
 	employee2.id	employee2.num	employee2.lname	employee2.fname	employee2.address	employee2.city	employee2.state	employee2.zip
1	10010000	656	HULL	WARREN	7593 Pede. Rd.	Kansas City	MO	55725
2	10010001	142	HAYES	KASPER	425-3365 Feugiat Rd.	Springfield	MO	27927
3	10010002	417	BARRETT	SYBILL	Ap #367-7227 Eu Street	Hartford	CT	83690
4	10010003	925	VALDEZ	THOR	"212-6890 Risus	 St."	Springfield	MO
5	10010004	832	BALLARD	WYNNE	748-4292 Vel Road	Hillsboro	OR	53903
6	10010005	536	GLASS	ZOE	Ap #565-7061 Massa. Rd.	Bear	DE	83658
7	10010006	987	BEARD	HIRAM	"P.O. Box 997	 6136 Ut Ave"	Lakewood	CO
8	10010007	832	DANIEL	MAYA	"P.O. Box 780	 6779 Mattis. St."	Springfield	MO
9	10010008	150	BEARD	COLBY	"P.O. Box 986	 6923 Nam St."	Aurora	IL
10	10010009	164	OLSEN	HEIDI	"P.O. Box 643	 1513 Et	 St."	Warren

```
```
CREATE EXTERNAL TABLE solution (
id string,
fname string,
lname string,
address string,
city string,
state string,
zip string
) 
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t;'
LOCATION 'hdfs://localhost:8020/user/training/problem4/solution'
;

INSERT OVERWRITE TABLE solution
select * from (
select id, initcap(fname), initcap(lname), address, city, state, substr(zip, 1, 5) as zip
from employee1 a where a.state = 'CA'
union all
select id, initcap(fname), initcap(lname), address, city, state, substr(zip, 1, 5) as zip
from employee2 b where b.state = 'CA'
) ab ;

select * from solution limit 10;
```
```
 	solution.cid	solution.fname	solution.lname	solution.address	solution.city	solution.state	solution.zip
1	10000063	Burton	Hayes	Ap #720-4012 Vivamus Avenue	San Diego	CA	96066
2	10000068	Ria	Herman	2974 Cras St.	San Francisco	CA	95310
3	10000073	Daquan	Roy	7636 Et Rd.	Los Angeles	CA	96606
4	10010024	Preston	Wood	2782 Amet Street	San Francisco	CA	92124
5	10010032	Bo	Rojas	Ap #445-6043 Massa Av.	San Diego	CA	92363
6	10010037	Zane	Banks	4778 Interdum. St.	San Diego	CA	92120
7	10010079	Ursa	Justice	3321 Risus. Ave	San Jose	CA	96850
8	10010088	Declan	Booth	4048 Nunc Rd.	San Francisco	CA	96324
```

# 5. customers and employees who live in Palo Alto, CA
```
CREATE EXTERNAL TABLE solution (
fname string,
lname string,
city string,
state string
) 
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t;'
LOCATION 'hdfs://localhost:8020/user/training/problem5/solution'
;

INSERT OVERWRITE TABLE solution
Select fname,lname, city, state 
from customer 
where  city = 'Palo Alto'
and state = 'CA'
union all
select fname,lname, city, state
from employee
where  city = 'Palo Alto'
and state = 'CA'
;
```
```
 	solution.fname	solution.lname	solution.city	solution.state
1	Farrah	Preston	Palo Alto	CA
2	Brielle	Hudson	Palo Alto	CA
3	Nelle	Kim	Palo Alto	CA
4	Tatum	Jacobs	Palo Alto	CA
5	Ivan	Gentry	Palo Alto	CA
6	Naida	Tran	Palo Alto	CA
7	Alden	Daniels	Palo Alto	CA
8	Maggy	Mcdaniel	Palo Alto	CA
9	Griffin	Pate	Palo Alto	CA
10	Burton	Hayes	Palo Alto	CA
```
# 6.  remoe any age information from the employee ( month/day/year->month/day)
```
CREATE EXTERNAL TABLE solution (
	  id 		int, 	  
	  fname string, 
	  lname string, 
	  address string, 
	  city string, 
	  state string, 
	  zip string, 
	  birthday string)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t;'
LOCATION	  'hdfs://localhost:8020/user/training/problem6/solution';

INSERT OVERWRITE TABLE solution 
select id,
    fname,
    lname,
    address,
    city,
    state,
    zip,
    substr(birthday, 1,5)
from employee;

select * from solution limit 10;
```
```
 	solution.id	solution.fname	solution.lname	solution.address	solution.city	solution.state	solution.zip	solution.birthday
1	10000000	Deanna	Lane	900-1514 Vitae, Rd.	Lafayette	LA	97827	08/31
2	10000001	Hall	Garrett	9656 Urna Avenue	Tucson	AZ	86511	08/24
3	10000002	Lucian	Dotson	P.O. Box 277, 4808 Fusce St.	Kearney	NE	57731	08/12
4	10000003	Yuri	Sherman	Ap #399-8275 Molestie Road	Kapolei	HI	16943	08/26
5	10000004	Jaime	Griffin	Ap #647-2123 Quis Rd.	Madison	WI	51394	08/13
6	10000005	Zorita	Weber	747-9424 Orci, Av.	Hattiesburg	MS	90262	08/09
7	10000006	Mara	Meadows	517-4594 Ac, Rd.	Huntsville	AL	35374	08/11
8	10000007	Evan	Richard	P.O. Box 223, 8182 Non, Av.	College	AK	99682	08/25
9	10000008	Briar	Anderson	Ap #548-6452 Nunc Road	Cleveland	OH	90704	08/18
10	10000009	Cole	Odom	P.O. Box 962, 2496 Sodales St.	Boston	MA	27282	08/21
```

# 7. Generate report (seattle, fname, space, lname)
```
select concat(fname," ", lname) as name 
from employee 
where city = 'Seattle' 
order by name asc;
```
```
 	name
1	Lucian Dotson
2	Zeph Horn
3	Zeph Mcclain
```
# 8. Sqoop to export customer data from HDFS into MySQL

##  8-1. data export

sqoop export --connect jdbc:mysql://localhost/problem8 --username cloudera --password cloudera --table solution --export-dir hdfs://localhost:8020/user/training/problem8/data/customer --input-fields-terminated-by '\t'

```
[training@localhost ~]$ sqoop export --connect jdbc:mysql://localhost/problem8 --username cloudera --password cloudera --table solution --export-dir hdfs://localhost:8020/user/training/problem8/data/customer --input-fields-terminated-by '\t'
19/06/19 00:13:44 INFO sqoop.Sqoop: Running Sqoop version: 1.4.6-cdh5.8.0
19/06/19 00:13:44 WARN tool.BaseSqoopTool: Setting your password on the command-line is insecure. Consider using -P instead.
19/06/19 00:13:44 INFO manager.MySQLManager: Preparing to use a MySQL streaming resultset.
19/06/19 00:13:44 INFO tool.CodeGenTool: Beginning code generation
19/06/19 00:13:45 INFO manager.SqlManager: Executing SQL statement: SELECT t.* FROM `solution` AS t LIMIT 1
19/06/19 00:13:45 INFO manager.SqlManager: Executing SQL statement: SELECT t.* FROM `solution` AS t LIMIT 1
19/06/19 00:13:45 INFO orm.CompilationManager: HADOOP_MAPRED_HOME is /usr/lib/hadoop-mapreduce
Note: /tmp/sqoop-training/compile/ba322172d2eb4f1e40bef9ed8f5b838c/solution.java uses or overrides a deprecated API.
Note: Recompile with -Xlint:deprecation for details.
19/06/19 00:13:47 INFO orm.CompilationManager: Writing jar file: /tmp/sqoop-training/compile/ba322172d2eb4f1e40bef9ed8f5b838c/solution.jar
19/06/19 00:13:47 INFO mapreduce.ExportJobBase: Beginning export of solution
19/06/19 00:13:47 INFO Configuration.deprecation: mapred.job.tracker is deprecated. Instead, use mapreduce.jobtracker.address
19/06/19 00:13:47 INFO Configuration.deprecation: mapred.jar is deprecated. Instead, use mapreduce.job.jar
19/06/19 00:13:47 INFO Configuration.deprecation: mapred.map.max.attempts is deprecated. Instead, use mapreduce.map.maxattempts
19/06/19 00:13:48 INFO Configuration.deprecation: mapred.reduce.tasks.speculative.execution is deprecated. Instead, use mapreduce.reduce.speculative
19/06/19 00:13:48 INFO Configuration.deprecation: mapred.map.tasks.speculative.execution is deprecated. Instead, use mapreduce.map.speculative
19/06/19 00:13:48 INFO Configuration.deprecation: mapred.map.tasks is deprecated. Instead, use mapreduce.job.maps
19/06/19 00:13:49 INFO client.RMProxy: Connecting to ResourceManager at /0.0.0.0:8032
19/06/19 00:13:49 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/06/19 00:13:49 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/06/19 00:13:49 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/06/19 00:13:50 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/06/19 00:13:50 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/06/19 00:13:50 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/06/19 00:13:50 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/06/19 00:13:50 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/06/19 00:13:50 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/06/19 00:13:50 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/06/19 00:13:50 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/06/19 00:13:50 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeInternal(DFSOutputStream.java:830)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:826)
19/06/19 00:13:50 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/06/19 00:13:50 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/06/19 00:13:50 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/06/19 00:13:50 WARN hdfs.DFSClient: Caught exception 
java.lang.InterruptedException
	at java.lang.Object.wait(Native Method)
	at java.lang.Thread.join(Thread.java:1245)
	at java.lang.Thread.join(Thread.java:1319)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.closeResponder(DFSOutputStream.java:862)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.endBlock(DFSOutputStream.java:600)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:789)
19/06/19 00:13:50 INFO input.FileInputFormat: Total input paths to process : 1
19/06/19 00:13:50 INFO input.FileInputFormat: Total input paths to process : 1
19/06/19 00:13:50 INFO mapreduce.JobSubmitter: number of splits:4
19/06/19 00:13:50 INFO Configuration.deprecation: mapred.map.tasks.speculative.execution is deprecated. Instead, use mapreduce.map.speculative
19/06/19 00:13:51 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1560923242283_0008
19/06/19 00:13:51 INFO impl.YarnClientImpl: Submitted application application_1560923242283_0008
19/06/19 00:13:51 INFO mapreduce.Job: The url to track the job: http://localhost:8088/proxy/application_1560923242283_0008/
19/06/19 00:13:51 INFO mapreduce.Job: Running job: job_1560923242283_0008
19/06/19 00:13:58 INFO mapreduce.Job: Job job_1560923242283_0008 running in uber mode : false
19/06/19 00:13:58 INFO mapreduce.Job:  map 0% reduce 0%
19/06/19 00:14:14 INFO mapreduce.Job:  map 100% reduce 0%
19/06/19 00:14:14 INFO mapreduce.Job: Job job_1560923242283_0008 completed successfully
19/06/19 00:14:15 INFO mapreduce.Job: Counters: 30
	File System Counters
		FILE: Number of bytes read=0
		FILE: Number of bytes written=571200
		FILE: Number of read operations=0
		FILE: Number of large read operations=0
		FILE: Number of write operations=0
		HDFS: Number of bytes read=18887
		HDFS: Number of bytes written=0
		HDFS: Number of read operations=19
		HDFS: Number of large read operations=0
		HDFS: Number of write operations=0
	Job Counters 
		Launched map tasks=4
		Data-local map tasks=4
		Total time spent by all maps in occupied slots (ms)=113218
		Total time spent by all reduces in occupied slots (ms)=0
		Total time spent by all map tasks (ms)=56609
		Total vcore-seconds taken by all map tasks=56609
		Total megabyte-seconds taken by all map tasks=28983808
	Map-Reduce Framework
		Map input records=100
		Map output records=100
		Input split bytes=676
		Spilled Records=0
		Failed Shuffles=0
		Merged Map outputs=0
		GC time elapsed (ms)=678
		CPU time spent (ms)=2180
		Physical memory (bytes) snapshot=476389376
		Virtual memory (bytes) snapshot=9048903680
		Total committed heap usage (bytes)=251920384
	File Input Format Counters 
		Bytes Read=0
	File Output Format Counters 
		Bytes Written=0
19/06/19 00:14:15 INFO mapreduce.ExportJobBase: Transferred 18.4443 KB in 26.0277 seconds (725.6507 bytes/sec)
19/06/19 00:14:15 INFO mapreduce.ExportJobBase: Exported 100 records.
```
## 8-2. show mysql table data
```
[training@localhost ~]$ mysql -u training -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1081
Server version: 5.1.73 Source distribution

Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use problem8;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+--------------------+
| Tables_in_problem8 |
+--------------------+
| solution           |
+--------------------+
1 row in set (0.00 sec)

mysql> select * from solution limit 10;
+----------+--------+----------+--------------------------------+-------------+-------+-------+------------+
| id       | fname  | lname    | address                        | city        | state | zip   | birthday   |
+----------+--------+----------+--------------------------------+-------------+-------+-------+------------+
| 10000000 | Deanna | Lane     | 900-1514 Vitae, Rd.            | Lafayette   | LA    | 97827 | 08/31/2016 |
| 10000001 | Hall   | Garrett  | 9656 Urna Avenue               | Tucson      | AZ    | 86511 | 08/24/2016 |
| 10000002 | Lucian | Dotson   | P.O. Box 277, 4808 Fusce St.   | Seattle     | WA    | 57731 | 08/12/2016 |
| 10000003 | Yuri   | Sherman  | Ap #399-8275 Molestie Road     | Kapolei     | HI    | 16943 | 08/26/2016 |
| 10000004 | Jaime  | Griffin  | Ap #647-2123 Quis Rd.          | Madison     | WI    | 51394 | 08/13/2016 |
| 10000005 | Zorita | Weber    | 747-9424 Orci, Av.             | Hattiesburg | MS    | 90262 | 08/09/2016 |
| 10000006 | Mara   | Meadows  | 517-4594 Ac, Rd.               | Huntsville  | AL    | 35374 | 08/11/2016 |
| 10000007 | Evan   | Richard  | P.O. Box 223, 8182 Non, Av.    | College     | AK    | 99682 | 08/25/2016 |
| 10000008 | Briar  | Anderson | Ap #548-6452 Nunc Road         | Cleveland   | OH    | 90704 | 08/18/2016 |
| 10000009 | Cole   | Odom     | P.O. Box 962, 2496 Sodales St. | Boston      | MA    | 27282 | 08/21/2016 |
+----------+--------+----------+--------------------------------+-------------+-------+-------+------------+
10 rows in set (0.00 sec)
```
# 9. Addition of the letter 'A' to Customer IDs

```
CREATE EXTERNAL TABLE solution (
	id 		    string,
    fname 		string,
    lname		string,
    address 	string,
    city 		string,
    state 		string,
    zip		string,
    birthday	string )
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t;'
LOCATION 'hdfs://localhost:8020/user/training/problem9/solution';

INSERT OVERWRITE TABLE solution
select concat("A",id) as id
       , fname
       , lname
       ,address
       ,city
       ,state
       ,zip
       ,birthday
from customer;

select * from solution limit 10;
```
```
 	solution.id	solution.fname	solution.lname	solution.address	solution.city	solution.state	solution.zip	solution.birthday
1	A1000000	Medge	Roach	P.O. Box 799, 6865 Nec Rd.	Racine	WI	56336	08/10/2016
2	A1000001	Nasim	Stone	P.O. Box 975, 759 Scelerisque Street	Tuscaloosa	AL	36696	08/16/2016
3	A1000002	Jolie	Schneider	P.O. Box 829, 9011 Vulputate St.	Kapolei	HI	59913	08/22/2016
4	A1000003	Lacota	Molina	Ap #831-2124 Pharetra. Avenue	Philadelphia	PA	84716	08/24/2016
5	A1000004	Blaine	Sweet	P.O. Box 151, 7847 Pede. St.	Topeka	KS	66452	08/21/2016
6	A1000005	Lesley	Bird	P.O. Box 569, 6635 Maecenas St.	Dallas	TX	87918	08/26/2016
7	A1000006	Sydnee	Howell	P.O. Box 147, 3714 Dignissim Street	Dallas	TX	94072	08/11/2016
8	A1000007	Jermaine	Griffin	Ap #859-2722 Donec Rd.	Baltimore	MD	13392	08/10/2016
9	A1000008	Brynn	Pennington	Ap #968-9936 Eleifend Avenue	Lincoln	NE	89536	08/23/2016
10	A1000009	Ava	Noble	P.O. Box 955, 1459 Urna St.	Baton Rouge	LA	34822	08/08/2016
```

# 10. CUSTOMER AND BILLING DATA JOIN
```
CREATE EXTERNAL TABLE solution(
id int, 
fname string, 
lname string, 
city string, 
state string, 
charge double, 
billdate string
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t;'
LOCATION 'hdfs://localhost:8020/user/training/problem10/solution'
;

INSERT OVERWRITE TABLE solution
select c.id
     , c.fname
     , c.lname
     , c.city
     , c.state
     , b.charge
     , substr(b.tstamp, 1, 10) as billdata
from customer c
    ,billing b
where c.id = b.id;
```
```
 	solution.id	solution.fname	solution.lname	solution.city	solution.state	solution.charge	solution.billdate
1	1000000	Medge	Roach	Racine	WI	15.789999999999999	2017-03-05
2	1000001	Nasim	Stone	Tuscaloosa	AL	57.729999999999997	2016-09-05
3	1000002	Jolie	Schneider	Kapolei	HI	556.03999999999996	2017-02-06
4	1000003	Lacota	Molina	Philadelphia	PA	654.63	2016-12-01
5	1000004	Blaine	Sweet	Topeka	KS	287.12	2017-05-30
6	1000005	Lesley	Bird	Dallas	TX	900.13999999999999	2016-10-26
7	1000006	Sydnee	Howell	Dallas	TX	347.88	2016-10-04
8	1000007	Jermaine	Griffin	Baltimore	MD	754.84000000000003	2017-08-01
9	1000008	Brynn	Pennington	Lincoln	NE	289.66000000000003	2017-03-26
10	1000009	Ava	Noble	Baton Rouge	LA	928.67999999999995	2016-12-12
```
