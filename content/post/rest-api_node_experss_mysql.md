+++
title = "Build a REST API with Node.js, Express, and MySQL"
date = 2023-04-20
[taxonomies]
categories = ["code"]
tags = ["nodejs", "rest-api", "express", "mysql"]
+++


## Prerequisites

To follow this tutorial, you will need:

- A local development environment for Node.js
- MySQL installed on your local machine or a remote server

## Step 1 — Setting Up Your Project

First, create a new directory for your project and navigate into it:

```bash
mkdir node-express-mysql
cd node-express-mysql
````

Next, initialize your project with npm:

```bash
npm init -y
```

This will create a `package.json` file in your project directory.

## Step 2 — Installing Dependencies

In this section, you will install the required dependencies for your project.

First, install the express module:

```bash
npm install express --save
```

Next, install the `mysql` module:

```bash
npm install mysql --save
```

## Step 3 — Creating Your Database

In this section, you will create a new database in MySQL.

First, log in to MySQL as the root user:

```bash
mysql -u root -p
```

Enter your MySQL root password when prompted.

Next, create a new database called `testdb`:

```sql
CREATE DATABASE testdb;
```

You should see output similar to the following:

```sql
Query OK, 1 row affected (0.01 sec)
```

Exit the MySQL shell by typing:

```sql
exit;
```

## Step 4 — Creating Your API

In this section, you will create an Express application that serves as your RESTful API.

First, create a new file called `server.js` and add the following code:

```javascript
const express = require('express');
const mysql = require('mysql');

const app = express();

const connection = mysql.createConnection({
  host: 'localhost',
  user: 'root',
  password: 'password',
  database: 'testdb',
});

connection.connect((err) => {
  if (err) {
    console.error('Error connecting to database:', err);
    return;
  }
  console.log('Connected to database');
});

app.get('/users', (req, res) => {
  connection.query('SELECT * FROM users', (err, rows) => {
    if (err) {
      console.error('Error querying database:', err);
      res.status(500).send('Error querying database');
      return;
    }
    res.json(rows);
  });
});

const port = process.env.PORT || 3000;

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
```

This code sets up an Express application with a single route that retrieves data from a MySQL database and returns it as JSON.

Start the server by running:

```bash
node server.js
```

You should now be able to access the API at http://localhost:3000/users.