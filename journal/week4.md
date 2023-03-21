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
   > This will take about 10-15 mins to get started.
   > We can temporarily stop an RDS instance for 4 days when we aren't using it.
  
  
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
https://www.postgresql.org/docs/current/app-createdb.html

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
```
### Creating Postgres Connection String
To ease up the task of connecting our database we need to set the connection command as an environmental variable.

the command to connect to our already created cruddur database is :

```bash
postgresql://postgres:password@127.0.0.1:5432/cruddur
```

to set this as an environmental variable 👇
```bash
export CONNECTION_URL="postgresql://postgres:password@127.0.0.1:5432/cruddur"
```

now lets set this into the gitpod environment.

```bash
gp env CONNECTION_URL="postgresql://postgres:pssword@127.0.0.1:5433/cruddur
```
lets set the production database which is rds environment variable

```
export PROD_CONNECTION_URL="PROD_CONNECTION_URL=postgresql://<aws rds endpoint address>:5432/cruddur"
```
now lets set this into the gitpod environment.
```bash
gp env PROD_CONNECTION_URL="PROD_CONNECTION_URL=postgresql://<aws rds endpoint address>:5432/cruddur"
```



### Shell Script to perform Database Operations

For things we commonly need to do we can create a new directory called `bin`

We'll create an new folder called `bin`to hold all our bash scripts.

```bash
mkdir /workspace/aws-bootcamp-cruddur-2023/backend-flask/bin
```
We'll create a new bash script named `db-connect` in the `bin` folder.

```bash

#! /usr/bin/bash 
CYAN='\033[1;36m'
NO_COLOR='\033[0m'
LABEL="db-connect"
printf "${CYAN}== ${LABEL}${NO_COLOR}\n"

echo "db-connect"

if [ "$1" = "prod" ]; then
  echo "Running in production mode"
  URL=$PROD_CONNECTION_URL
else
  echo "Running in development mode"
  URL=$CONNECTION_URL
fi


psql $URL
```

To execute the script:

`The condition statement in this bash script will be used to toggle between production and dev database foe connection using the example below`
#### PROD connection
```bash
cd backend-flask

./bin/db-connect prod
```

#### DEV Connection

```bash

cd backend-flask

./bin/db-connect
```

#### we'll make the db-connect script executable.

we will do this by modifying the file permission.

``` bash
chmod u+x bin/db-connect
```
We'll create a new bash script named `db-create` in the `bin` folder.
```bash
#! /usr/bin/bash 
CYAN='\033[1;36m'
NO_COLOR='\033[0m'
LABEL="db-create"
printf "${CYAN}== ${LABEL}${NO_COLOR}\n"


echo "db-create"

 NO_DB_CONNECTION_URL=$(sed 's/\/cruddur//g' <<<"$CONNECTION_URL")


psql $NO_DB_CONNECTION_URL -c "create database cruddur;"
```

#### we'll make the db-create script executable.

we will do this by modifying the file permission.

``` bash
chmod u+x bin/db-create
```
TO execute this script:

```bash

cd backend-flask

./bin/db-create
```
We'll create a new bash script named `db-drop in the `bin` folder.
```bash
#! /usr/bin/bash 
CYAN='\033[1;36m'
NO_COLOR='\033[0m'
LABEL="db-drop"
printf "${CYAN}== ${LABEL}${NO_COLOR}\n"


echo "db-drop"

 NO_DB_CONNECTION_URL=$(sed 's/\/cruddur//g' <<<"$CONNECTION_URL")

 psql $NO_DB_CONNECTION_URL -c "drop database cruddur;"
````
#### we'll make the db-drop script executable.

we will do this by modifying the file permission.

``` bash
chmod u+x bin/db-drop
```
TO execute this script:

```bash

cd backend-flask

./bin/db-drop
```

We'll create a new bash script named `db-schema-load` in the `bin` folder.

```bash
#! /usr/bin/bash 
CYAN='\033[1;36m'
NO_COLOR='\033[0m'
LABEL="db-schema-load"
printf "${CYAN}== ${LABEL}${NO_COLOR}\n"


echo "db-schema-load"

schema_path=$(realpath .)/db/schema.sql
echo $schema_path

if [ "$1" = "prod" ]; then
  echo "Running in production mode"
  URL=$PROD_CONNECTION_URL
else
  echo "Running in development mode"
  URL=$CONNECTION_URL
fi

psql $URL cruddur < $schema_path
```
#### we'll make the db-schema-load script executable.

we will do this by modifying the file permission.

``` bash
chmod u+x bin/db-schema-load
```
TO execute this script:

```bash

cd backend-flask

./bin/db-schema-load
```


We'll create a new bash script named `db-seed` in the `bin` folder.

```bash
#! /usr/bin/bash 

CYAN='\033[1;36m'
NO_COLOR='\033[0m'
LABEL="db-seed"
printf "${CYAN}== ${LABEL}${NO_COLOR}\n"


echo "db-seed"

seed_path=$(realpath .)/db/seed.sql
echo $seed_path

if [ "$1" = "prod" ]; then
  echo "Running in production mode"
  URL=$PROD_CONNECTION_URL
else
  echo "Running in development mode"
  URL=$CONNECTION_URL
fi

psql $URL cruddur < $seed_path
```
#### we'll make the db-seed script executable.

we will do this by modifying the file permission.

``` bash
chmod u+x bin/db-seed
```
TO execute this script:

```bash

cd backend-flask

./bin/db-seed
```

We'll create a new bash script named `db-session` in the `bin` folder.

```bash
#! /usr/bin/bash

CYAN='\033[1;36m'
NO_COLOR='\033[0m'
LABEL="db-session"
printf "${CYAN}== ${LABEL}${NO_COLOR}\n"

if [ "$1" = "prod" ]; then
  echo "Running in production mode"
  URL=$PROD_CONNECTION_URL
else
  echo "Running in development mode"
  URL=$CONNECTION_URL
fi

NO_DB_URL=$(sed 's/\/cruddur//g' <<<"$CONNECTION_URL")
psql $NO_DB_URL -c "select pid as process_id, \
       usename as user,  \
       datname as db, \
       client_addr, \
       application_name as app,\
       state \
from pg_stat_activity;"
```
#### we'll make the db-session script executable.

we will do this by modifying the file permission.

``` bash
chmod u+x bin/db-session
```
TO execute this script:

```bash

cd backend-flask

./bin/db-session
```


We'll create a new bash script named `db-setup` in the `bin` folder.

```bash
#! /usr/bin/bash

-e # stops if its fails at any point 
CYAN='\033[1;36m'
NO_COLOR='\033[0m'
LABEL="db-setup"
printf "${CYAN}== ${LABEL}${NO_COLOR}\n"

bin_path="$(realpath .)/bin"


source "$bin_path/db-drop"
source "$bin_path/db-create"
source "$bin_path/db-schema-load"
source "$bin_path/db-seed"
source "$bin_path/rds-update-sg-rule"
```
#### we'll make the db-setup script executable.

we will do this by modifying the file permission.

``` bash
chmod u+x bin/db-setup
```
To execute this script:

```bash

cd backend-flask

./bin/db-setup
```
## Hooking Postgres to the Backend Flask Python Application
---------
we need to set the env var for our backend-flask application in our dockerfile
```yml
  backend-flask:
    environment:
      CONNECTION_URL: "${CONNECTION_URL}"
```


To plug the postgres to the flask application we need to install the postgres drivers to our backend-flask folder

we'll be adding the following to our `requirements.txt`
https://www.psycopg.org/psycopg3/

```bash
 psycopg[binary]
 psycopg[pool]
 ```
 
 we would install the postgres driver using our `pip` command
 
 ```bash 
 cd backend-flask
 
 pip install -r requirements.txt
 ```
 
 ## DB Object and Connection Pool
 
lets create a file named `db.py` in the `lib` file and import the following code.

`lin/db.py`

```python
from psycopg_pool import ConnectionPool
import os

def query_wrap_object(template):
  sql = f"""
  (SELECT COALESCE(row_to_json(object_row),'{{}}'::json) FROM (
  {template}
  ) object_row);
  """
  return sql

def query_wrap_array(template):
  sql = f"""
  (SELECT COALESCE(array_to_json(array_agg(row_to_json(array_row))),'[]'::json) FROM (
  {template}
  ) array_row);
  """
  return sql

connection_url = os.getenv("CONNECTION_URL")
pool = ConnectionPool(connection_url)
```

in our Home activities we'll replace our mock endpoint with real api call:

```python

from lib.db import pool, query_wrap_array

    sql = query_wrap_array("""
     SELECT
        activities.uuid,
        users.display_name,
        users.handle,
        activities.message,
        activities.replies_count,
        activities.reposts_count,
        activities.likes_count,
        activities.reply_to_activity_uuid,
        activities.expires_at,
        activities.created_at
      FROM public.activities
      LEFT JOIN public.users ON users.uuid = activities.user_uuid
      ORDER BY activities.created_at DESC
    """)
    print("SQL--------------")
    print("SQL--------------")
    

    with pool.connection() as conn:
      with conn.cursor() as cur:
        cur.execute(sql)
        # this will return a tuple
        # the first fieled being the data
        json = cur.fetchone()
    print("1---------------")
    print(json[0])
    return json[0]
    return results
    
 ```
 
 ## Connect to RDS via Gitpod
 In order to connect to the RDS instance we need to provide our Gitpod IP and whitelist for inbound traffic on port 5432 in our AWS Management console.
 
 to get our gitpod ip address 
 
 ```bash
 curl ifconfig.me
 ```
 <img width="723" alt="SCR-20230315-wwg" src="https://user-images.githubusercontent.com/112965272/225459654-aeb5b3b6-a934-4c38-8dea-18a0e279ea51.png">

lets set the gitpod ip command as an env variable.

```bash
GITPOD_IP=$(curl ifconfig.me)
```
> Gitpod workspaces are ephemeral and only live for as long as you work on a task.
> Gitpod workspaces IP addresses are also ephemeral i.e it changes each time an enviroment is spinned off.

so we have to create a script that  update the allowed ip with the new ip in our gitpod workspace in our postgres security group.


We'll create an inbound rule for Postgres (5432) and provide the GITPOD ID.

We'll get the security group rule id so we can easily modify it in the future from the terminal here in Gitpod.

```sh
export DB_SG_ID="sg-0b725ebab7e256345t"
gp env DB_SG_ID="sg-0b725ebab7e256345t"
export DB_SG_RULE_ID="sgr-070061bba456cfa88"
gp env DB_SG_RULE_ID="sgr-070061bba456cfa88"
```

Whenever we need to update our security groups we can do this for access.

```sh
aws ec2 modify-security-group-rules \
    --group-id $DB_SG_ID \
    --security-group-rules "SecurityGroupRuleId=$DB_SG_RULE_ID,SecurityGroupRule={IpProtocol=tcp,FromPort=5432,ToPort=5432,CidrIpv4=$GITPOD_IP/32}"
```

https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-security-group-rules.html#examples

lets turn this script `aws ec2 modify-security-group-rule` to bash script.

We'll create a new bash script named `rds-update-sg-rule` in the `bin` folder.
```bash
#! /usr/bin/bash

CYAN='\033[1;36m'
NO_COLOR='\033[0m'
LABEL="rds-update-sg-rule"
printf "${CYAN}== ${LABEL}${NO_COLOR}\n"

echo "rds-update-sg-rule"

aws ec2 modify-security-group-rules \
    --group-id $DB_SG_ID \
    --security-group-rules "SecurityGroupRuleId=$DB_SG_RULE_ID,SecurityGroupRule={Description=GITPOD,IpProtocol=tcp,FromPort=5432,ToPort=5432,CidrIpv4=$GITPOD_IP/32}"
````
#### we'll make the `rds-update-sg-rule` script executable.

we will do this by modifying the file permission.

``` bash
chmod u+x bin/rds-update-sg-rule
```
TO execute this script:

```bash

cd backend-flask

./bin/rds-update-sg-rule
```

<img width="989" alt="SCR-20230316-1lf" src="https://user-images.githubusercontent.com/112965272/225475261-73ea4731-2ccc-45aa-808f-86dba824a429.png">

## Update Gitpod yaml file with the command to get gitpod ip as an env variable.

```yaml
    command: |
      export GITPOD_IP=$(curl ifconfig.me)
      source "$THEIA_WORKSPACE_ROOT/backend-flask/db-update-sg-rule"
```

## Test remote access

We'll create a connection url:

```
 postgresql://cruddurroot:<DB password>@cruddur-db-instance.c42hhpqlj9jr.us-east-1.rds.amazonaws.com:5432/cruddur
```

We'll test that it works in Gitpod:

```sh
psql postgresql://cruddurroot:<DB password>@cruddur-db-instance.c42hhpqlj9jr.us-east-1.rds.amazonaws.com:5432/cruddur
```

We'll update your URL for production use case

```sh
export PROD_CONNECTION_URL="postgresql://cruddurroot:<DB password>@cruddur-db-instance.c42hhpqlj9jr.us-east-1.rds.amazonaws.com:5432/cruddur"
gp env PROD_CONNECTION_URL="postgresql://cruddurroot:<DB password>@cruddur-db-instance.c42hhpqlj9jr.us-east-1.rds.amazonaws.com:5432/cruddur"
```

to connect to the productionn database, we would pass the PROD_CONNECTION_URL TO psql 
```bash
psql $PROD_CONNECTION_URL
```




To connect to our rds database which is production we can use the `db-connect` bash script and add `prod` to it 

```bash
source bin/db-connect prod
```
<img width="995" alt="SCR-20230316-293" src="https://user-images.githubusercontent.com/112965272/225479569-4efb2190-789a-4fae-8de3-271df3a67877.png">


## Setup Cognito Post Confirmation Lambda

### Create the handle function

- Create lambda in same vpc as rds instance python 3.8
- Add a layer for psycopg2 with one of the below methods for development or production.

### lets create our lambda and add the post confirmation.

- login to amazon management console
<img width="1251" alt="SCR-20230321-unn" src="https://user-images.githubusercontent.com/112965272/226740691-b035abea-9b6d-48d5-967b-74bcb28d3702.png">
<img width="1442" alt="SCR-20230321-uqa" src="https://user-images.githubusercontent.com/112965272/226741440-88e0a9c6-86a8-40e7-80c5-3ddf5a7df105.png">

After creating the lambda we pass the following code to the newly created lambda

```python
import json
import psycopg2
import os

def lambda_handler(event, context):
    user = event['request']['userAttributes']
    print('userAttributes')
    print(user)

    user_display_name  = user['name']
    user_email         = user['email']
    user_handle        = user['preferred_username']
    user_cognito_id    = user['sub']
    try:
      print('entered-try')
      sql = f"""
         INSERT INTO public.users (
          display_name, 
          email,
          handle, 
          cognito_user_id
          ) 
        VALUES(%s,%s,%s,%s)
      """
      print('SQL Statement ----')
      print(sql)
      conn = psycopg2.connect(os.getenv('CONNECTION_URL'))
      cur = conn.cursor()
      params = [
        user_display_name,
        user_email,
        user_handle,
        user_cognito_id
      ]
      cur.execute(sql,tuple(params))
      conn.commit() 

    except (Exception, psycopg2.DatabaseError) as error:
      print(error)
    finally:
      if conn is not None:
          cur.close()
          conn.close()
          print('Database connection closed.')
    return event
```

<img width="1394" alt="SCR-20230321-v7v" src="https://user-images.githubusercontent.com/112965272/226745266-b474ec94-4b51-43f6-bcd1-e9cb2f25efb3.png">

- click on configuration and click on environmental variables.
- set the production connection url to enable the lambda function connect and the newly created user to the database

<img width="1427" alt="SCR-20230321-vd8" src="https://user-images.githubusercontent.com/112965272/226747031-89378740-dc52-4e4f-84bd-47f54e5a4906.png">

- we need to set the vpc for our lambda  as well as the security group to allow our lambda connection to our RDS database.

<img width="1420" alt="SCR-20230321-vk5" src="https://user-images.githubusercontent.com/112965272/226748338-6d87e16a-7a02-4baf-a7d7-7de96eeb3949.png">


- we need to create a trigger for our lambda function from cognito

<img width="1407" alt="SCR-20230321-vrn" src="https://user-images.githubusercontent.com/112965272/226750048-57f1c445-7ba4-48e9-9812-dd067823aa95.png">



To test our cruddur app we create a new account in cruddur
<img width="817" alt="SCR-20230321-vv0" src="https://user-images.githubusercontent.com/112965272/226751270-c5964ca8-44d9-4398-8aba-49af1b22c48d.png">

- After creating our new user we need to check our proudction database to check if the lambda function passed the user to the db.

```bash
cd backend-flask
```

```bash
source bin/db-connect prod
```
passing the prod is instructing the script to connect to the rds database.

![SCR-20230321-w90](https://user-images.githubusercontent.com/112965272/226754157-abed29ab-6121-41a7-acf4-d5b2d1d7ff32.png)

The following script helps to arrange our result.

```sql
\x auto
```

```sql
SELECT * FROM users;
```

![SCR-20230321-weo](https://user-images.githubusercontent.com/112965272/226755096-b0445244-714f-4f73-baed-f7e0ffef5f98.png)


## Creating user Activities 

we need to create a folder called `sql` and create another folder called `activities`.

### Importing the following  codes into the db.py file

```python
from psycopg_pool import ConnectionPool
import os
import re
import sys
from flask import current_app as app

class Db:
  def __init__(self):
    self.init_pool()

  def template(self,*args):
    pathing = list((app.root_path,'db','sql',) + args)
    pathing[-1] = pathing[-1] + ".sql"

    template_path = os.path.join(*pathing)

    green = '\033[92m'
    no_color = '\033[0m'
    print("\n")
    print(f'{green} Load SQL Template: {template_path} {no_color}')

    with open(template_path, 'r') as f:
      template_content = f.read()
    return template_content

  def init_pool(self):
    connection_url = os.getenv("CONNECTION_URL")
    self.pool = ConnectionPool(connection_url)
  # we want to commit data such as an insert
  # be sure to check for RETURNING in all uppercases
  def print_params(self,params):
    blue = '\033[94m'
    no_color = '\033[0m'
    print(f'{blue} SQL Params:{no_color}')
    for key, value in params.items():
      print(key, ":", value)

  def print_sql(self,title,sql,params={}):
    cyan = '\033[96m'
    no_color = '\033[0m'
    print(f'{cyan} SQL STATEMENT-[{title}]------{no_color}')
    print(sql,params)
  def query_commit(self,sql,params={}):
    self.print_sql('commit with returning',sql,params)

    pattern = r"\bRETURNING\b"
    is_returning_id = re.search(pattern, sql)

    try:
      with self.pool.connection() as conn:
        cur =  conn.cursor()
        cur.execute(sql,params)
        if is_returning_id:
          returning_id = cur.fetchone()[0]
        conn.commit() 
        if is_returning_id:
          return returning_id
    except Exception as err:
      self.print_sql_err(err)
  # when we want to return a json object
  def query_array_json(self,sql,params={}):
    self.print_sql('array',sql,params)

    wrapped_sql = self.query_wrap_array(sql)
    with self.pool.connection() as conn:
      with conn.cursor() as cur:
        cur.execute(wrapped_sql,params)
        json = cur.fetchone()
        return json[0]
  # When we want to return an array of json objects
  def query_object_json(self,sql,params={}):

    self.print_sql('json',sql,params)
    self.print_params(params)
    wrapped_sql = self.query_wrap_object(sql)

    with self.pool.connection() as conn:
      with conn.cursor() as cur:
        cur.execute(wrapped_sql,params)
        json = cur.fetchone()
        if json == None:
          "{}"
        else:
          return json[0]
  def query_value(self,sql,params={}):
    self.print_sql('value',sql,params)
    with self.pool.connection() as conn:
      with conn.cursor() as cur:
        cur.execute(sql,params)
        json = cur.fetchone()
        return json[0]
  def query_wrap_object(self,template):
    sql = f"""
    (SELECT COALESCE(row_to_json(object_row),'{{}}'::json) FROM (
    {template}
    ) object_row);
    """
    return sql
  def query_wrap_array(self,template):
    sql = f"""
    (SELECT COALESCE(array_to_json(array_agg(row_to_json(array_row))),'[]'::json) FROM (
    {template}
    ) array_row);
    """
    return sql
  def print_sql_err(self,err):
    # get details about the exception
    err_type, err_obj, traceback = sys.exc_info()

    # get the line number when exception occured
    line_num = traceback.tb_lineno


    # print the connect() error
    print ("\npsycopg ERROR:", err, "on line number:", line_num)
    print ("psycopg traceback:", traceback, "-- type:", err_type)

    # print the pgcode and pgerror exceptions
    print ("pgerror:", err.pgerror)
    print ("pgcode:", err.pgcode, "\n")

db = Db()
```

 
 





