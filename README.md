readme file

Detailed Steps involved to execute the objective:
1. Install a PGSQL database on your local or a cloud virtual machine (should be a
 Docker-based image)

- 
this Project is done Using a Microsoft Azure VM .
* created a virtual machine on Azure vm with Ubuntu OS 
* ssh the virtual machine using the ssh command with username and password(this can be done via putty as well for with ppk key )
*install docker on the OS as its not installed in the base image:
sudo apt-get update
sudo apt-get install docker.io

* Pull the Postgres Docker Image using the below command
docker pull postgres


*Run a PostgreSQL Container:
Create a Docker container with PostgreSQL:
sudo docker run -itd -e POSTGRES_USER=sami -e POSTGRES_PASSWORD=XXXXXXXX -p 5432:5432 -v ~/data:/var/lib/postgresql/data --name postgresql postgres

***the password is changed with "xxxx" here as per recommendations. 

*Connect to Psql container using the command 
docker exec -it postgresql bash

if the container is stoped, identify the container and start the container with the command 
docker start 21323xxxxxxxxx...

connect to Postgress using the below command 
psql -U hevodata #sami is the instance name 

add tables to the psql 
data base name : hevodata 

~CREATE DATABASE hevodata

connect database:
\c hevodata;
CREATE TABLE customers (
    id SERIAL PRIMARY KEY,
    first_name TEXT NOT NULL,
    last_name TEXT NOT NULL
);

CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    user_id INTEGER NOT NULL,
    order_date DATE NOT NULL,
    status TEXT NOT NULL,
    CONSTRAINT fk_user
        FOREIGN KEY(user_id) 
        REFERENCES customers(id)
);

CREATE TABLE payments (
    id SERIAL PRIMARY KEY,
    order_id INTEGER NOT NULL,
    payment_method TEXT NOT NULL,
    amount INTEGER NOT NULL,
    CONSTRAINT fk_order
        FOREIGN KEY(order_id) 
        REFERENCES orders(id)
);

=====================================================================
loading data in tables:
-- Load data into customers table
COPY customers (id, first_name, last_name)
FROM '/home/sami/files/raw_customers.csv'
DELIMITER ','
CSV HEADER;

-- Load data into orders table
COPY orders (id, user_id, order_date, status)
FROM '/home/sami/files/raw_orders.csv'
DELIMITER ','
CSV HEADER;

-- Load data into payments table
COPY payments (id, order_id, amount, payment_date)
FROM '/home/sami/files/raw_payments.csv'
DELIMITER ','
CSV HEADER;


check for tables formed 
\dt


======================
sign up for snowflake and choose 
choose partner connect as hevo and setup hevo account

to build the pipline with logical replication:
Logical Replication
Logical Replication is applicable for the PostgreSQL Source types. In this mode, data is replicated using PostgreSQL Write Ahead Logs (WAL) set at a logical level (available on PostgreSQL version 9.4 and above). This mode is useful when you are looking to replicate the complete database as it is.

While incremental ingestion is done from the logs, query modes must be defined to fetch the historical data from the Source tables. The query modes available for Logical Replication mode are Full Load, Delta-Timestamp, and Unique Incrementing Append Only.

Note: Hevo creates a new Replication Slot for the Pipeline, which may lead to higher disk consumption in your PostgreSQL Database.

Steps to Build the Pipeline
Step 1: Enable Logical Replication on PostgreSQL
Edit PostgreSQL Configuration:
Open the postgresql.conf file:
sudo nano /etc/postgresql/12/main/postgresql.conf

Enable logical replication by setting the following parameters:
wal_level = logical
max_replication_slots = 4
max_wal_senders = 4

Edit pg_hba.conf File:
Open the pg_hba.conf file to configure client authentication:
sudo nano /etc/postgresql/12/main/pg_hba.conf

Add the following line to allow replication connections:
host    replication     all             0.0.0.0/0               md5

Restart PostgreSQL Service:
Restart the PostgreSQL service to apply the changes:
sudo systemctl restart postgresql

Step 2: Create a Replication Slot
Connect to PostgreSQL:
Use psql to connect to your PostgreSQL database:
psql -U sami -d hevodata

Create a Replication Slot:
Run the following command to create a logical replication slot:
SQL

================================================================================
DBT testing 
create a directory for as dbtproject
#mkdir dbtproject











Welcome to your new dbt project!

### Using the starter project

Try running the following commands:
- dbt run
- dbt test


### Resources:
- Learn more about dbt [in the docs](https://docs.getdbt.com/docs/introduction)
- Check out [Discourse](https://discourse.getdbt.com/) for commonly asked questions and answers
- Join the [chat](https://community.getdbt.com/) on Slack for live discussions and support
- Find [dbt events](https://events.getdbt.com) near you
- Check out [the blog](https://blog.getdbt.com/) for the latest news on dbt's development and best practices
# hevodata
# hevodata
# hevodata
# hevodata
