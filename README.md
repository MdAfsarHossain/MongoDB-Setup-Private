# Mongo DB All Setup

### Resources:

- [MongoDB-Atlas](https://account.mongodb.com/account/login?nds=true)
- [Express](https://expressjs.com/)
- [Express-Middleware](https://expressjs.com/en/resources/middleware.html)
- [Express-Cors](https://expressjs.com/id/resources/middleware/cors.html)
- [MongoDb-CRUD](https://www.mongodb.com/docs/drivers/node/current/fundamentals/crud/)

## Server Site SetUp

```js
mkdir myapp
cd myapp
```

```js
npm init
index.js
```

```js
npm install express cors mongodb
```

```js
node index.js
nodemon index.js
```

`package.json`

```js
"scripts": {
    "start": "node index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },

```

```js
const express = require("express");
const cors = require("cors");

const app = express();
const port = process.env.PORT || 5000;
```

```js
// Middleware
app.use(cors());
app.use(express.json());
```

```js
app.get("/", (req, res) => {
  res.send("Hello World!");
});
```

```js
app.listen(port, () => {
  console.log(`Example app listening on port ${port}`);
});
```

## Client Site Setup
