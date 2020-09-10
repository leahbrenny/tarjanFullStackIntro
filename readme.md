Tarjan Full Stack Intro!
===

- npm init & git init usuals
- add .gitigore
- `npm install express pg`: "pg" is the package that will allow us to run SQL queries 
- set up our folders (Server, public, etc)
- move a copy of JQuery into "server/public/vendors"
- do initial commit

end 1st lecture segment
---

- build out our interface for adding a song and viewing songs
- get handshakes working for GET and POST routes
- 1st make GET route work (1st server side db query)


end 2nd lecture segment
---

- get user input working
- write POST query on server

- style as possible with time remaining


## CRUD


- Create
- Read
- Update
- Delete


HTTP    |  English                |       SQL
------  |  --------               |       ------
GET     |  Get a list of songs    |       SELECT
POST    |  Create a new song      |       INSERT
DELETE  |  Remove a song          |       DELETE
PUT     |  Modify an existing song  |       UPDATE




### Standard API Endpoints

| | |
|---------------------|-----------------------|
| GET /monkeys        | List monkeys          |
| GET /monkeys/:id    | Get a monkey by id    |
| POST /monkeys       | Create a monkey       |
| PUT /monkeys/:id    | Update a monkey by id |
| DELETE /monkeys/:id | Delete a monkey by id |


### Which quotes should I use?!

SQL is picky about quotes! Unlike Javascript where you can use double-quotes ("), single-quotes('), or back-ticks (``) interchangeably, in SQL these mean different things:

double quotes (") === Table and column names
  eg. `SELECT * FROM "songs" WHERE "id" = 7`

single quotes (') === Values we want to access within a DB record
  eg. `SELECT * FROM "songs" WHERE "artist" = 'The Doors'`


Embedding SQL code inside our javascript files makes this trickier. As a general rule, I use back-ticks (``) in JS. This allows me to use double and single quotes within my SQL query string:

```js
let queryString = `SELECT * FROM "songs" WHERE "published" < '1973-01-01';`

pool.query(queryString)
  .then(...)
  .catch(...);
```

Using back-ticks also lets me split up long queries into multiple lines:

```js
let queryString = `
  SELECT * FROM "songs"
  WHERE 
    -- Published between 1990 and 2005
    ("published" > '1990-01-01' AND "published" < '2005-01-01')

    -- or, its by The Doors
    OR "artist" = 'The Doors'
  
  -- Grab top 10 results by rank
  ORDER BY "rank" ASC
  LIMIT 10;
`;

pool.query(queryString)
  .then(...)
  .catch(...);
```

One important note with using backticks for SQL strings: **never use string interpolation** inside your SQL query strings!

```js
let songId = req.params.id;

// DON'T DO THIS!!!!
let queryString = `
  SELECT * FROM "songs" WHERE id = '${songId}'
`

pool.query(queryString)
  .then(...)
  .catch(...);
```

In this example, a user could destroy your entire database with a simple GET request in their browser, by going to

```
http://localhost:3000/songs/7';DROP TABLE "songs";--
```

Instead, we should use placeholder values in our query:

```js
let songId = req.params.id;

let queryString = `
  SELECT * FROM "songs" WHERE id = $1
`

pool.query(queryString, [songId])
  .then(...)
  .catch(...);
```

https://xkcd.com/327/
