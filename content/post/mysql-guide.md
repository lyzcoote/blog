+++
title = "A Basic MySQL Guide"
date = 2023-04-21
[taxonomies]
categories = ["code"]
tags = ["mysql"]
+++

## Using the MySQL Client

MySQL lets you connect to a server using a client like the command-line tool, **mysql**. Use the **-u** and **-p** flags to provide your username and password:

```bash
mysql -u [username] -p
mysql -u [username] -p [database]
```

When you're finished, exit the MySQL command-line client as follows:

```bash
exit
```

## Working With User Accounts

To create a new user account, open the new terminal to access MySQL as the root and create a new user as follows:

```bash
sudo mysql -u root -p
mysql> CREATE USER 'username' IDENTIFIED BY 'password';
```

You can also set up a user account with restricted access by specifying a host that they must access the database from:

```sql
CREATE USER 'user'@'localhost';  
CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';
```

To specify a remote connection, you can replace the ‘localhost’ with the machine's IP address as follows:

```sql
CREATE USER 'username'@'ip_address' IDENTIFIED BY 'password';
```

 Lastly, you can delete an account with the following statement:

```sql
DROP USER 'user'@'ip_address' IDENTIFIED BY 'password';
```

## User Account Privileges

Before moving on, you'll need to set the appropriate permissions on the new user account. This avoids the risk of unnecessary user access within the database.

You can work with user privileges in MySQL using statements such as 

- GRANT
- REVOKE
- ALTER

Depending on the actions you want a user to be able to carry out, you can assign all or some permissions. These permissions are:

- ALL PRIVILEGES
- SELECT
- UPDATE
- INSERT
- DELETE
- CREATE
- DROP 
- GRANT OPTION.

You can assign the administrative privilege of inserting data to all tables belonging to any database:

```sql
GRANT INSERT ON *.* TO 'username'@'ip_address';
```

However, you can also limit user access by specifying the database before the period. You can allow a user to select, insert, and delete data to and from all the tables inside a database as follows:

```sql
GRANT SELECT, INSERT, DELETE ON database.* TO 'user'@'ip_address' IDENTIFIED BY 'password';
```

Similarly, you can restrict user access to a specific table by specifying a table name after the period.

```sql
GRANT SELECT, INSERT, DELETE ON database.table_name TO 'user'@'ip_address' IDENTIFIED BY 'password';
```

You can grant all permissions to every table inside a specific database as follows:

```sql
GRANT ALL PRIVILEGES ON database.* TO 'user'@'ip_address' IDENTIFIED BY 'password';
```

To revoke permissions of a user from a single database:

```sql
REVOKE ALL PRIVILEGES ON database.* FROM 'user'@'ip_address'; 
```

You can revoke all user privileges from every database as follows:

```sql
REVOKE ALL PRIVILEGES, GRANT OPTION FROM 'user'@'ip_address'; 
```

Finally, you can set passwords like this:

```sql
SET PASSWORD FOR 'user'@'ip_address' = PASSWORD('new_password');
```

Note the use of the PASSWORD function which *hashes the plaintext password*.

## Working With Databases

You can create a new database with a name that does not already exist:

```sql
CREATE DATABASE database_name;
```

You can switch the current database to another that you want to work with:

```sql
USE database_name;
```

Lastly, you can delete an entire database along with its tables as follows:

```sql
DROP DATABASE database_name;
```

## Working With Tables

A table is the main structural element of a [MySQL database](https://dev.mysql.com/doc/refman/8.0/en/system-schema.html), grouping a set of related records as rows. Each row has columns with different data types that can be:

- CHAR
- VARCHAR
- TEXT

among many others.

The general syntax to create a table is as follows:

```sql
CREATE TABLE table_name (column_1 data_type1, column_2 data_type2);
```

You can also create a new table from an existing table by selecting specific columns as follows:

```sql
CREATE TABLE new_table_name AS SELECT column_1, column_2 FROM existing_table_name;
```

You can add data to a table using the following command:

```sql
INSERT INTO table_name (column_1, column_2) VALUES (value_1, value_2);
```

To delete a table, use the DROP TABLE statement as follows:

```sql
DROP TABLE table_name;
```

Or you keep the table but delete all its data using:

```sql
TRUNCATE TABLE table_name; 
```

## Accessing Databases

Use the following statement to show all the available databases inside the MySQL DMS:

```sql
SHOW DATABASES;
```

Similarly, you can list all tables in the current database:

```sql
SHOW TABLES;
```

To view all columns inside a table:

```sql
DESCRIBE table_name;
```

To display column information inside a table:

```sql
DESCRIBE table_name column_name;
```

## Querying Databases

MySQL allows you to use a **SELECT** statement to query data from the database. You can use various MySQL clauses to extend its base functionality.

The following statement returns a result set consisting of two columns from every row in a table:

```sql
SELECT column1, column2 FROM table_name; 
```

Or display all columns as follows:

```sql
SELECT * FROM table_name; 
```

You can also query databases/tables and retrieve information using conditions as follows:

```sql
SELECT column1, column2 FROM table_name WHERE condition; 
```

The SELECT statement also allows you to group the result set by one or more columns using the GROUP BY clause. You can then use aggregate functions to calculate summary data:

```sql
SELECT COUNT(CustomerID), Country FROM Customers GROUP BY Country;
```

## Updating Tables

You can modify data inside the table by using the UPDATE or ALTER statements. The UPDATE statement allows you to update existing single or multiple records/rows.

The following MySQL command changes the **UserName** and **City** of a single record where the **UserID** is **2**:

```sql
UPDATE Users SET UserName = 'Alfred James', City= 'Munich' WHERE UserID = 2;
```

While this example updates all **UserName**s for all records where the **City** is **Munich**:

```sql
UPDATE Users SET UserName='Juan' WHERE City='Munich'; 
```

You can add a column to a table like this:

```sql
ALTER TABLE table_name ADD COLUMN column_name;
```

To remove a column from the table, use the ALTER TABLE statement as follows:

```sql
ALTER TABLE table_name DROP COLUMN column_name; 
```

## Example Database

Now let's use what we learned before.

Let's say that we want to create a database called **MangoLeFroot** and a table called **Users** with this proprieties:

| sqlID                                       | createdAt        | username             | first_name           | last_name   | age             | isAdmin         |
|---------------------------------------------|------------------|----------------------|----------------------|-------------|-----------------|-----------------|
| auto_increment int(15) not null primary key | current_timestap | varchar(50) not null | varchar(50) not null | varchar(50) | int(3) not null | int(1) not null |

With 2 users:

- Lyz Coote, normal user, 21 years old
- Mango LeFroot, Admin User, 28 years old


Let's start! Let's create the DB and the table!

```sql
CREATE DATABASE MangoLeFroot;
USE MangoLeFroot;
CREATE TABLE users  
(  
	sqlID int(15) auto_increment comment 'Primary Key',  
	createAt datetime default current_timestamp not null comment 'Creation timestap',  
	username varchar(50) not null,  
	first_name varchar(50) not null,  
	last_name varchar(50) null,  
	age int(3) not null,  
	isAdmin int(1) default 0 not null,  
	constraint users_pk  
	primary key (sqlID)  
)engine = InnoDB;
```

Let's verify that our table is made up correctly with:

```sql
DESCRIBE users;
```

Results:

| Field | Type | Null | Key | Default | Extra |
| :--- | :--- | :--- | :--- | :--- | :--- |
| sqlID | int | NO | PRI | null | auto\_increment |
| createAt | datetime | NO |  | CURRENT\_TIMESTAMP | DEFAULT\_GENERATED |
| username | varchar\(100\) | NO |  | null |  |
| first\_name | varchar\(50\) | NO |  | null |  |
| last\_name | varchar\(50\) | YES |  | null |  |
| age | int | NO |  | null |  |
| isAdmin | int | NO |  | 0 |  |


Purrfect! Now let's add the users:

```sql
INSERT INTO users  
	(username, first_name, last_name, age)  
VALUES  
	('lyzcoote', 'Lyz', 'Coote', 21),  
	('froot', 'Mango', 'Le Froot', 28)  
;
```

Let's make the user **froot** an Admin account with:

```sql
UPDATE users set isAdmin = 1 where users.username = 'froot'
```

Let's see if everything is correct with:

```sql
SELECT * from users;
```

Results:

| sqlID | createAt | username | first\_name | last\_name | age | isAdmin |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | 2023-04-21 07:53:23 | lyzcoote | Lyz | Coote | 21 | 0 |
| 2 | 2023-04-21 07:53:23 | froot | Mango | Le Froot | 28 | 1 |

And here we are!