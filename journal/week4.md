# Week 4 — Postgres and RDS

This week we are going to be using AWS Relational database (RDS) to store our user activity data.

### What is RDS ?

Amazon Relational Database Service (Amazon RDS) is a collection of managed services that makes it simple to set up, operate, and scale databases in the cloud.

This week we are going to be using postgres database under the RDS collection of database to store our user activity data.

### FIrst Step

- Create an RDS Datababe using the `AWS CLI`

we first have to test that our aws cli is properly created by issue the command below to get our aws identity.

```bash
aws sts get-caller-identity
```

#### output

<img width="911" alt="SCR-20230314-tkh" src="https://user-images.githubusercontent.com/112965272/225126857-df6fe8f3-9833-4003-b6ab-562cd95c79db.png">

now that we have esterblished connection to our AWS Environment we can proceed to create our database via the cli

to create the rds database from the cli we issues the following command.

```bash
aws rds create-db-instance \
  --db-instance-identifier cruddur-db-instance \
  --db-instance-class db.t3.micro \
  --engine postgres \
  --engine-version  14.6 \
  --master-username cruddurroot \
  --master-user-password <Enter password here> \
  --allocated-storage 20 \
  --availability-zone us-east-1a \
  --backup-retention-period 0 \
  --port 5432 \
  --no-multi-az \
  --db-name cruddur \
  --storage-type gp2 \
  --publicly-accessible \
  --storage-encrypted \
  --enable-performance-insights \
  --performance-insights-retention-period 7 \
  --no-deletion-protection
  ```
  
  #### Output
  
  <img width="713" alt="SCR-20230314-tud" src="https://user-images.githubusercontent.com/112965272/225129967-ba152367-325b-4029-a02a-8b50e1aa828c.png">

when we login to the aws console we can see the rds database being started.

<img width="1137" alt="SCR-20230314-tx7" src="https://user-images.githubusercontent.com/112965272/225130129-10e2d818-2dd8-4b82-9422-582608c19746.png">

### now that we have successfully created our postgres database in AWS RDS lets spin up our docker-compose file

command:
```bash
docker compose -f "docker-compose.yml" up -d --build 

```
<img width="1078" alt="SCR-20230314-uq5" src="https://user-images.githubusercontent.com/112965272/225136472-cb7f0f71-7895-471c-bb5b-6bbafff05a37.png">

<img width="1084" alt="SCR-20230314-us0" src="https://user-images.githubusercontent.com/112965272/225136897-d24a0fed-2f12-4077-bb0d-c7df564070e5.png">

### Login into postgres database 

to login to postgress we issue the following command 

```bash
psql -U postgres -h localhost
```

`-h is all --host ` which is needed to connect to postgre from a docker

after issuing the command the terminal will prompt us to enter a password
with a correct password are successfully authenticated into postgres.

<img width="998" alt="SCR-20230314-v0d" src="https://user-images.githubusercontent.com/112965272/225138958-7cd256a2-0d34-4df3-a92e-2044590b46cb.png">

next step --- we create a databse called cruddur inside postgres with the command below:
```bash
CREATE databse cruddur;
```
<img width="989" alt="SCR-20230314-v3x" src="https://user-images.githubusercontent.com/112965272/225139658-d997c740-43e2-400d-b471-0eac3e36ce95.png">

to view the list of databases inside postgres
```bash
\l
```
<img width="876" alt="SCR-20230314-vb1" src="https://user-images.githubusercontent.com/112965272/225142633-835f7657-f04f-4033-9fb4-e7586adbdcba.png">

Common PSQL commands:

```sql
\x on -- expanded display when looking at data
\q -- Quit PSQL
\l -- List all databases
\c database_name -- Connect to a specific database
\dt -- List all tables in the current database
\d table_name -- Describe a specific table
\du -- List all users and their roles
\dn -- List all schemas in the current database
CREATE DATABASE database_name; -- Create a new database
DROP DATABASE database_name; -- Delete a database
CREATE TABLE table_name (column1 datatype1, column2 datatype2, ...); -- Create a new table
DROP TABLE table_name; -- Delete a table
SELECT column1, column2, ... FROM table_name WHERE condition; -- Select data from a table
INSERT INTO table_name (column1, column2, ...) VALUES (value1, value2, ...); -- Insert data into a table
UPDATE table_name SET column1 = value1, column2 = value2, ... WHERE condition; -- Update data in a table
DELETE FROM table_name WHERE condition; -- Delete data from a table

