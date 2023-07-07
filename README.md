# Docker Storage Scenario - MySQL Database

This repository provides an example scenario completed as part of my training after learning Docker storage. The scenario focuses on creating and managing a Docker volume for persistent storage of MySQL data.

## Prerequisites

Make sure you have Docker installed on your system. You can download Docker from the official website: [https://www.docker.com/get-started](https://www.docker.com/get-started)

## Using Docker Volumes for Persistent MySQL Data

1. Create a Docker volume "mysql-data" in the host machine

```
docker volume create mysql-data
```

2. Check if the volume is created or not

```
docker volume ls
```

if the volume is created, you should see:
```
DRIVER    VOLUME NAME
local     mysql-data
```

3. Run a mysql container mapping the volume "mysql-data" to persist the database 

```
docker run -d --name mysql_db -v mysql-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=prabin123 mysql:latest
```

if this runs successfully, you should be able to see it when you run "docker ps"

```
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                 NAMES
a699216f94ae   mysql:latest   "docker-entrypoint.s…"   2 minutes ago   Up 2 minutes   3306/tcp, 33060/tcp   mysql_db
```

4. Access the running container

```
docker exec -it mysql_db mysql -u root -p
```

You will see:
```
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 9
Server version: 8.0.33 MySQL Community Server - GPL

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 

```

5. create a database "mydatabase" and table "mytable" and populate the table as follow;

```
mysql> create database mydatabase;
Query OK, 1 row affected (0.01 sec)

mysql> use mydatabase;
Database changed
mysql> CREATE TABLE mytable (id INT AUTO_INCREMENT PRIMARY KEY, name VARCHAR(255));
Query OK, 0 rows affected (0.04 sec)

mysql> INSERT INTO mytable (name) VALUES ('John'), ('Jane'), ('Michael');
Query OK, 3 rows affected (0.04 sec)
Records: 3  Duplicates: 0  Warnings: 0
```

6. stop the container "mysql_db" and delete it.

```
docker stop mysql_db
docker rm mysql_db
```

7. run a new mysql container mapping with the exiting volume "mysql-data"

```
docker run -d --name mysql_db_new -e MYSQL_ROOT_PASSWORD=prabin -v mysql-data:/var/lib/mysql mysql:latest
```

8. access the running container

```
prabin@pkc:~$ docker exec -it mysql_db_new mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 11
Server version: 8.0.33 MySQL Community Server - GPL

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
```

9. run the query

```
mysql> use mydatabase;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> select * from mytable;
+----+---------+
| id | name    |
+----+---------+
|  1 | John    |
|  2 | Jane    |
|  3 | Michael |
+----+---------+
3 rows in set (0.00 sec)
```

It shows that the data persisted even after stopping and removing the previous MySQL container.

