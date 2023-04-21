+++
title = "Redis Caching with NodeJS"
date = 2023-04-20
[taxonomies]
categories = ["code"]
tags = ["nodejs", "redis"]
+++

Caching is a technique that can help speed up web applications by storing frequently accessed data in memory. 

> Redis is an in-memory data store that can be used as a cache to help speed up Node.js applications.

Today we will build an Express application that retrieves data from a RESTful API using the axios module. 

Next, you will modify the app to store the data fetched from the API in Redis using the ```node-redis``` module.

## Prerequisites

To follow this tutorial, you will need:

- A local development environment for Node.js
- An instance of Redis running on your local machine or a remote server

## Step 1 — Setting Up Your Project

First, create a new directory for your project and navigate into it:

```bash
mkdir node-redis-caching
cd node-redis-caching
````

Next, initialize your project with ```npm```:

```bash
npm init -y
```

This will create a ```package.json``` file in your project directory.

## Step 2 — Retrieving Data from a RESTful API

In this section, you will build an Express application that retrieves data from a RESTful API using the axios module.

First, install the required dependencies:

```bash
npm install express axios --save
```

Next, create a new file called ```server.js``` and add the following code:

```javascript
const express = require('express');
const axios = require('axios');

const app = express();

app.get('/', async (req, res) => {
  try {
    const response = await axios.get('https://jsonplaceholder.typicode.com/posts');
    const posts = response.data;
    res.json(posts);
  } catch (error) {
    console.error(error);
    res.status(500).send('Error retrieving posts from API');
  }
});

const port = process.env.PORT || 3000;

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
```

This code sets up an Express application with a single route that retrieves data from the JSONPlaceholder API and returns it as JSON.

Start the server by running:

```bash
node server.js
```

You should now be able to access the API at http://localhost:3000/.

## Step 3 — Caching RESTful API Requests Using Redis

In this section, you’ll cache data from the API so that only the initial visit to your app endpoint will request data from an API server, and all the following requests will fetch data from the Redis cache.

Open the ```server.js``` file:

```bash
nano server.js
```

In your ```server.js``` file, import the ```node-redis``` module and create a Redis client:

```javascript
const redis = require('redis');
const client = redis.createClient();
```

Next, modify your route handler to check if the requested data is already cached in Redis. If it is, return it directly. If not, retrieve it from the API and store it in Redis for future requests:

```javascript
app.get('/', async (req, res) => {
  try {
    const cachedPosts = await client.getAsync('posts');

    if (cachedPosts) {
      console.log('Retrieving posts from cache');
      res.json(JSON.parse(cachedPosts));
    } else {
      console.log('Retrieving posts from API');
      const response = await axios.get('https://jsonplaceholder.typicode.com/posts');
      const posts = response.data;
      client.set('posts', JSON.stringify(posts));
      res.json(posts);
    }
  } catch (error) {
    console.error(error);
    res.status(500).send('Error retrieving posts from API');
  }
});
```

This code uses the getAsync method of your Redis client to retrieve cached data. If there is no cached data available for this request, it retrieves it from the API and stores it in Redis using the set method.

Start your server again by running:

```bash
node server.js
```

You should now be able to access your cached API at http://localhost:3000/.