---
layout: post
title: "SQL 최적화를 위해선?"
subtitle: SQL 쿼리 최적화를 위한 방안들
date: 2020-06-17 2:32:28 -0400
cover-img: /assets/img/sql.jpg
tags: [sql, query]
---

# How to Optimize SQL Queries

This article sorts out some special techniques for optimizing SQL Queries
Pawan Jain
Pawan Jain
Follow
Jun 13 · 7 min read

In this article, I include several suggestions on optimizing SQL statements, most of which are summarized in the actual development. I hope it will be helpful to everyone.
We aim to make a query that performs acceptably and will continue to do so for a reasonable amount of time in the future

Photo by Nick Morrison on Unsplash
Update: I uploaded all optimization tricks in my GitHub Repo

1. Try not to use select _ to query SQL, but select specific fields.
   Counterexample:
   select _ from employee;
   Positive example:
   select id，name from employee;
   Reason:
   By using only the required fields we can save our resources and reduce network overhead.
   It is likely that the covered index will not be used, which will result in a query back to the table.
2. If you know that there is only one query result, it is recommended to use limit 1
   Suppose that there is an employee table, and you want to find a person named jay.
   CREATE TABLE employee (
   id int(11) NOT NULL,
   name varchar(255) DEFAULT NULL,
   age int(11) DEFAULT NULL,
   date datetime DEFAULT NULL,
   sex int(1) DEFAULT NULL,
   PRIMARY KEY (`id`) );
   Counter-example:
   select id，name from employee where name='jay';
   Positive example:
   select id，name from employee where name='jay' limit 1;
   Reason:
   After adding limit 1, when a corresponding record is found, it will not continue to scan down, and the efficiency will be greatly improved.
3. Try to avoid using or in the where clause to join conditions
   Create a new user table, it has a common index userId, the table structure is as follows:
   CREATE TABLE `user` (
   `id` int(11) NOT NULL AUTO*INCREMENT,
   `userId` int(11) NOT NULL,
   `age` int(11) NOT NULL,
   `name` varchar(255) NOT NULL,
   PRIMARY KEY (`id`),
   KEY `idx_userId` (`userId`) )
   Suppose now that you need to query users with a userid 1 or the age of 18, it is easy to have the following SQL.
   Counter-example:
   select * from user where userid = 1 or age = 18;
   Positive example:
   //Use union all
   select _ from user where userid=1
   union all
   select _ from user where age = 18;
   //Or write two separate SQL
   select \_ from user where userid=1;
   select \* from user where age = 18;
   Reason:
   The use of or may invalidate the index and thus it requires a full table scan.
   For the case of or without index, it is assumed that it has taken the index of userId, but when it comes to the age query condition, it has to perform a full table scan, which requires a three-step process: full table scan + index scan + merge
4. Optimize limit paging
   When we do daily paging, we usually use limits to achieve, but when the offset is particularly large, the query efficiency becomes low. Because Mysql does not skip the offset and directly fetch the data.
   Counter-example:
   select id，name，age from employee limit 10000，10;
   Positive example:
   //Solution 1: Return the largest record (offset) of the last query
   select id，name from employee where id>10000 limit 10;
   //Solution 2: order by + index
   select id，name from employee order by id limit 10000，10;
   Reason:
   If you use optimization solution 1, it returns the last query record (offset), so that you can skip the offset, efficiency has improved a lot.
   Option two uses order by + index, which can also improve query efficiency.
5. Optimize your like statement
   In daily development, if you use fuzzy keyword queries, it is easy to think of like, but like is likely to invalidate your index.
   Counter-example:
   select userId，name from user where userId like '%123';
   Positive example:
   select userId，name from user where userId like '123%';
   Reason: In THIS Story
6. Use where conditions to limit the data to be queried to avoid returning extra rows
   Suppose the scenario is Query whether a user is a member. I have seen the old implementation code like this.
   Counter-example:
   List<Long> userIds = sqlMap.queryList("select userId from user where isVip=1");
   boolean isVip = userIds.contains(userId);
   Positive example:
   Long userId = sqlMap.queryObject("select userId from user where userId='userId' and isVip='1' ")
   boolean isVip = userId！=null;
   Reason: Check what data is needed, avoid returning unnecessary data, and save expenses and computational overhead
7. You should avoid using the != or <> operator in the where clause as much as possible, otherwise the engine will give up using the index and perform a full table scan
   Counter-example:
   select age,name from user where age <>18;
   Positive example:
   //You can consider separate two sql write
   select age,name from user where age <18;
   select age,name from user where age >18;
   Reason: Using != and <> is likely to invalidate the index
8. If you insert too much data, consider bulk insertion
   If you want to insert too much data, consider bulk insert
   Counter-example:
   for(User u :list){
   INSERT into user(name,age) values(#name#,#age#)  
   }
   Positive example:
   //One batch of 500 inserts, carried out in batches
   insert into user(name,age) values
   <foreach collection="list" item="item" index="index" separator=",">
   (#{item.name},#{item.age})
   </foreach>
   Bulk insertion performance is good, time-saving
   To make an analogy: If you need to move 10,000 bricks to the top of the building, you have an elevator.
   The elevator can put an appropriate amount of bricks at a time (up to 500), you can choose to transport one brick at a time, or you can transport 500 bricks at a time. Which time do you think is expensive?
9. Use the distinct keyword with caution
   The distinct keyword is generally used to filter duplicate records to return unique records. When used in the case of querying one field or few fields, it brings optimization effect to the query.
   However, when there are many fields, it will greatly reduce the query efficiency.
   Counter-example:
   SELECT DISTINCT \* from user;
   Positive example:
   select DISTINCT name from user;
   Reason: The CPU time and occupancy time of the statement with distinct are higher than the statement without distinct.
   Because when querying many fields, if you use distinct, the database engine will compare the data and filter out the duplicate data. However, this comparison and filtering process will consume system resources and CPU time.
10. Remove redundant and duplicate indexes
    Counter-example:
    KEY `idx_userId` (`userId`)  
    KEY `idx_userId_age` (`userId`,`age`)
    Positive example:
    //Delete the userId index, because the combined index (A, B) is equivalent to creating the (A) and (A, B) indexes
    KEY `idx_userId_age` (`userId`,`age`)
    Reasons: Duplicate indexes need to be maintained, and the optimizer also needs to consider them one by one when optimizing queries, which will affect performance.
11. If the amount of data is large, optimize your modify/delete statement
    Avoid modifying or deleting too much data at the same time, because it will cause high CPU utilization, which will affect others’ access to the database.
    Counter-example:
    //Delete 100,000 or 1 million+ at a time?
    delete from user where id <100000;
    //Or use single cycle operation, low efficiency and long time
    for（User user：list）{
    delete from user； }
    Positive example:
    //Delete in batches, such as 500 each time
    delete user where id<500;
    delete product where id>=500 and id<1000；
    Reason: To delete too much data at one time, there may be a lock wait timeout exceed error, so it is recommended to operate in batches.
12. Consider using default values ​​instead of null in the where clause
    Counter-example:
    select _ from user where age is not null;
    Positive example:
    select _ from user where age>0; //Set 0 as default
    Reason:
    If you replace the null value with the default value, it often makes indexing possible, and at the same time, the expression will be relatively clear.
13. Try to replace union with union all
    If there are no duplicate records in the search results, it is recommended to replace union with union all.
    Counter-example:
    select _ from user where userid=1
    union  
    select _ from user where age = 10
    Positive example:
    select _ from user where userid=1
    union all  
    select _ from user where age = 10
    Reason:
    If you use union, regardless of whether the search results are repeated, it will try to merge and sort them before outputting the final results.
    If the search results are known to have no duplicate records, use union all instead of the union, which will improve efficiency.
14. Use numeric fields as much as possible. If the fields only contain numeric information, try not to design them as a character type.
    Counter-example:
    `king_id` varchar（20） NOT NULL;
    Positive example:
    `king_id` int(11) NOT NULL;
    Reason: Compared with numeric fields, character types will reduce the performance of query and connection, and will increase storage overhead.
15. Use varchar/nvarchar instead of char/nchar whenever possible
    Counter-example:
    `deptName` char(100) DEFAULT NULL
    Positive example:
    `deptName` varchar(100) DEFAULT NULL
    Reason:
    First, because the storage space of the variable-length field is small, hence, the storage space can be saved.
    Secondly, for queries, searching in a relatively small field is more efficient.
16. Use explain to analyze your SQL plan
    When writing SQL in daily development, try to develop a habit. Use explain to analyze the SQL you wrote, especially the index.
    explain select \* from user where userid = 10086 or age =18;
    Yeah, we made it to the end. Hope you enjoy it and have an idea about the optimizing and speeding up your SQL queries 😊

Other stories you’ll love

- Related to optimization of File Handling - How to do Efficient File Handling
- Related to optimization of Python code — Do You have these Python Speedup Skills?
- Related to memory management in Python - Dark Secrets of Python Memory Allocation
  Thank you for reading. Don’t hesitate to stay tuned for more!
