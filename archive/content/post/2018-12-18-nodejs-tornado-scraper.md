---
title: "Nodejs tornado movies scraper"
date: 2018-12-18T10:55:26.149+01:00
subtitle: "A scraper for tornado movies to get the latest episode of a series"
author: Gabriele Teotino
tags: ["node", "nodejs", "scraper"]
categories: ["dev"]
draft: true
---

Tornado movies is a stremaing platform with a lot of TV series and movies. With this tool we can scrape the website to monitor when new episode of a series is available.

<!--more-->

Create a folder for the project and initialize a new node project

```shell
# update npm version
sudo npm update npm -g
mkdir tornado-scraper
cd tornado-scraper
npm init
```

Fill the questionaire with a description and change the licence to MIT.

The software will use a postgres database and expose a simple web api, so install the following libraries

```shell
npm install --save express pg
```

Start visual studio code, postman and pgadmin

## Express minimal configuration

Create a new file **index.js**

```javascript
const express = require('express');
const bodyParser = require('body-parser');
const app = express();
const port = 3000;

app.use(bodyParser.json());
app.use(
  bodyParser.urlencoded({
    extended: true
  })
);

app.get('/', (req, res) => {
  res.json({ info: 'Tornado scraper is working' });
});

app.listen(port, () => {
    console.log(`App running on port ${port}.`)
});
```

Launch the application

```shell
node index.js
```

In Postman create a new collection and a new request **Root** pointing to *http://localhost:3000/*.

Send the request and verify that the response is correct.

## Database

I used **pgAdmin III** but this can be done using the command line or any other tool.

Create a new login role **tornado_user** with password **password** (please change it to a sensible password) and no special privileges.

Create a new database **tornado** and set as a database owner **tornado_user**

Disconnect and create a new connection to localhost using the user **tornado_user** (use localhost so that the authentication can be md5)

Create a new table **users** in the **public** schema with a couple fields so that we can make a few tests. Use the query command to create the table (the interface to create a new table is a bit shitty)

```sql
CREATE TABLE users (
  ID SERIAL PRIMARY KEY,
  name VARCHAR(30),
  email VARCHAR(30)
);
```

Insert a couple test users in the table.

Create a new file **queries.js** with a basic configuration for a connection pool

```javascript
const Pool = require('pg').Pool
const pool = new Pool({
  user: 'tornado_user',
  host: 'localhost',
  database: 'tornado',
  password: 'password',
  port: 5432,
})
```

## Routes

Create a few new routes for the users table

- GET /users -> getUsers()
- GET /users/:id -> getUserById()
- POST users -> createUser()
- PUT /users/:id -> updateUser()
- DELETE /users/:id -> deleteUser()

In **queries.js** add the new endpoint functions

```javascript
const Pool = require('pg').Pool;
const pool = new Pool({
  user: 'tornado_user',
  host: 'localhost',
  database: 'tornado',
  password: 'password',
  port: 5432
});

const getUsers = (request, response) => {
  pool.query('SELECT * FROM users ORDER BY id ASC', (error, results) => {
    if (error) {
      throw error;
    }
    response.status(200).json(results.rows);
  });
};

const getUserById = (request, response) => {
  const id = parseInt(request.params.id);

  pool.query('SELECT * FROM users WHERE id = $1', [id], (error, results) => {
    if (error) {
      throw error;
    }
    response.status(200).json(results.rows);
  });
};

const createUser = (request, response) => {
  const { name, email } = request.body;

  pool.query(
    'INSERT INTO users (name, email) VALUES ($1, $2) RETURNING id',
    [name, email],
    (error, results) => {
      if (error) {
        throw error;
      }
      response.status(201).send(`User added with ID: ${results.rows[0].id}`);
    }
  );
};

const updateUser = (request, response) => {
  const id = parseInt(request.params.id);
  const { name, email } = request.body;

  pool.query(
    'UPDATE users SET name = $1, email = $2 WHERE id = $3',
    [name, email, id],
    (error, results) => {
      if (error) {
        throw error;
      }
      response.status(200).send(`User modified with ID: ${id}`);
    }
  );
};

const deleteUser = (request, response) => {
  const id = parseInt(request.params.id);

  pool.query('DELETE FROM users WHERE id = $1', [id], (error, results) => {
    if (error) {
      throw error;
    }
    response.status(200).send(`User deleted with ID: ${id}`);
  });
};

module.exports = {
  getUsers,
  getUserById,
  createUser,
  updateUser,
  deleteUser
};
```

Now import the queries file into **index.js** and configure the endpoints

```javascript
const express = require('express');
const bodyParser = require('body-parser');
const app = express();
const port = 3000;

const db = require('./queries')

app.use(bodyParser.json());
app.use(
  bodyParser.urlencoded({
    extended: true
  })
);

app.get('/', (req, res) => {
  res.json({ info: 'Tornado scraper is working' });
});

app.get('/users', db.getUsers)
app.get('/users/:id', db.getUserById)
app.post('/users', db.createUser)
app.put('/users/:id', db.updateUser)
app.delete('/users/:id', db.deleteUser)

app.listen(port, () => {
    console.log(`App running on port ${port}.`)
});
```

In the console stop the previously running node application and launch it again

```shell
node index.js
```

In Postman add a request for each new method and test them.
For the POST use a *x-www-form-urlencoded*

## Scraping

We will use the following libraries: [request-promise](https://github.com/request/request-promise) [CheerioJS](https://github.com/cheeriojs/cheerio), and [Puppeteer](https://github.com/GoogleChrome/puppeteer).

```shell
npm install --save request-promise cheerio puppeteer
# this is required by request promise but not installed for some reason
npm install request --save
```

Let's write a simple scraper for the names of the USA presidents from wikipedia.

First visit the page using Chrome, select the element you want to match (in this case George Washington) right click and *inspect*.

In the elements view click on the three dot on the left of the item and select *Copy->Copy selector*

```
#mw-content-text > div > table:nth-child(12) > tbody > tr:nth-child(3) > td:nth-child(4) > b > big > a
```

In this case we can use a simpler selector: `big > a`

Create a new file **potus-scraper.js** and use request-promise to get the page and **Cheerio** to apply the selector

```javascript
const rp = require('request-promise');
const $ = require('cheerio');
const url = 'https://en.wikipedia.org/wiki/List_of_Presidents_of_the_United_States';

rp(url)
  .then(function(html){
    //success!
    console.log($('big > a', html).length);
    console.log($('big > a', html));
  })
  .catch(function(err){
    //handle error
  });
}
```
