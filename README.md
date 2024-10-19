# Mongo DB All Setup

### Resources:

- [MongoDB-Atlas](https://account.mongodb.com/account/login?nds=true)
- [Express](https://expressjs.com/)
- [Express-Middleware](https://expressjs.com/en/resources/middleware.html)
- [Express-Cors](https://expressjs.com/id/resources/middleware/cors.html)
- [MongoDb-CRUD](https://www.mongodb.com/docs/drivers/node/current/fundamentals/crud/)
- [DotEnv](https://www.npmjs.com/package/dotenv)
- [JSON Web Token](https://jwt.io/libraries?language=Node.js)
- [JSON Web Token GIT](https://github.com/auth0/node-jsonwebtoken)
- [Cookie Parser](https://expressjs.com/en/resources/middleware/cookie-parser.html)

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
npm install express cors mongodb dotenv
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
require("dotenv").config();

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

### [MongoDB]()

```js
const { MongoClient, ServerApiVersion, ObjectId } = require("mongodb");
```

```js
// Mongodb configuration
const uri = "mongodb://localhost:27017";

// const uri = "mongodb+srv://<db_username>:<db_password>@cluster0.b6ov8m0.mongodb.net/?retryWrites=true&w=majority&appName=Cluster0";
// const uri = "mongodb+srv://${process.env.DB_USER}:{process.env.DB_PASS}@cluster0.b6ov8m0.mongodb.net/?retryWrites=true&w=majority&appName=Cluster0";

// Create a MongoClient with a MongoClientOptions object to set the Stable API version
const client = new MongoClient(uri, {
  serverApi: {
    version: ServerApiVersion.v1,
    strict: true,
    deprecationErrors: true,
  },
});
```

### Create DB

```js
// const database = client.db('usersDB');
// const userCollection = database.collection('users');

const userCollection = client.db("usersDB").collection("users");
```

### Get All data from the database

```js
// Get all users
app.get("/users", async (req, res) => {
  const cursor = userCollection.find();
  const result = await cursor.toArray();
  res.send(result);
});
```

### Get Specific data from the database

```js
// Get Specific user by id
app.get("/users/:id", async (req, res) => {
  const id = req.params.id;
  const query = { _id: new ObjectId(id) };
  const user = await userCollection.findOne(query);
  res.send(user);
});
```

### Create a new data

```js
// Create a new user
app.post("/users", async (req, res) => {
  const user = req.body;
  console.log("New user: ", user);
  const result = await userCollection.insertOne(user);
  res.send(result);
});
```

### Update a specific data from the database

```js
// Update a user in the collection
app.put("/users/:id", async (req, res) => {
  const id = req.params.id;
  const user = req.body;
  console.log("Update user: ", user);

  const filter = { _id: new ObjectId(id) };
  const options = { upsert: true };
  const updatedUser = {
    $set: {
      name: user.name,
      email: user.email,
    },
  };

  const result = await userCollection.updateOne(filter, updatedUser, options);
  res.send(result);
});
```

```js
// Update a job in db
app.put("/job/:id", verifyToken, async (req, res) => {
  const id = req.params.id;
  const jobData = req.body;
  const query = { _id: new ObjectId(id) };
  const options = { upsert: true };
  const updateDoc = {
    $set: {
      ...jobData,
    },
  };
  const result = await jobsCollection.updateOne(query, updateDoc, options);
  res.send(result);
});
```

### Delete a specific data from the database

```js
// Delete a user from the collection
app.delete("/users/:id", async (req, res) => {
  const id = req.params.id;
  console.log("Please delete a user from the collection: ", id);
  const query = { _id: new ObjectId(id) };

  const result = await userCollection.deleteOne(query);
  res.send(result);
});
```

### MongoDB Setup Code

```js
async function run() {
  try {
    // Connect the client to the server	(optional starting in v4.7)
    await client.connect();

    // Write here create data base code

    // Write here create collection code

    // Write here create data code

    // Write here get data code

    // Write here update code

    // Write here delete code

    // Send a ping to confirm a successful connection
    await client.db("admin").command({ ping: 1 });
    console.log(
      "Pinged your deployment. You successfully connected to MongoDB!"
    );
  } finally {
    // Ensures that the client will close when you finish/error
    // await client.close();
  }
}
run().catch(console.dir);
```

## Client Site Setup

### Get Route

```js
  {
    path:'/users',
    element: <Users></Users>,
    loader: () => fetch('http://localhost:5000/users')
  },
```

### Update Route

```js
  {
    path: '/update/:id',
    element: <Update></Update>,
    loader: ({params}) => fetch(`http://localhost:5000/users/${params.id}`)
  }
```

### Create a new data

```js
const handleAddCoffee = (event) => {
  event.preventDefault();

  const form = event.target;

  const name = form.name.value;
  const quantity = form.quantity.value;
  const supplier = form.supplier.value;
  const taste = form.taste.value;
  const category = form.category.value;
  const details = form.details.value;
  const photo = form.photo.value;

  const newCoffee = {
    name,
    quantity,
    supplier,
    taste,
    category,
    details,
    photo,
    user.email // take the user's email address from the AuthContext
  };

  // Write here server site code to send data to the server
};

<form onSubmit={handleAddCoffee}></form>;
```

### Send data to the server for create a new data

```js
// send data to the server
fetch("http://localhost:5000/coffee", {
  method: "POST",
  headers: {
    "content-type": "application/json",
  },
  body: JSON.stringify(newCoffee),
})
  .then((res) => res.json())
  .then((data) => {
    console.log(data);
    if (data.insertedId) {
      Swal.fire({
        title: "Success!",
        text: "Coffee Added Successfully",
        icon: "success",
        confirmButtonText: "Cool",
      });
    }
  });
```

### Update data field

```js
const coffee = useLoaderData();
const { _id, name, quantity, supplier, taste, category, details, photo } =
  coffee;

// Set the default value to the input data field by using this
defaultValue = { name };
defaultValue = { quantity };
defaultValue = { supplier };
defaultValue = { taste };
```

### Send data to the server for update the data

```js
// send data to the server
fetch(`http://localhost:5000/coffee/${_id}`, {
  method: "PUT",
  headers: {
    "content-type": "application/json",
  },
  body: JSON.stringify(updatedCoffee),
})
  .then((res) => res.json())
  .then((data) => {
    console.log(data);
    if (data.modifiedCount > 0) {
      Swal.fire({
        title: "Success!",
        text: "Coffee Updated Successfully",
        icon: "success",
        confirmButtonText: "Cool",
      });
    }
  });
```

### Delete data

```js
Swal.fire({
  title: "Are you sure?",
  text: "You won't be able to revert this!",
  icon: "warning",
  showCancelButton: true,
  confirmButtonColor: "#3085d6",
  cancelButtonColor: "#d33",
  confirmButtonText: "Yes, delete it!",
}).then((result) => {
  if (result.isConfirmed) {
    fetch(`http://localhost:5000/coffee/${_id}`, {
      method: "DELETE",
    })
      .then((res) => res.json())
      .then((data) => {
        console.log(data);
        if (data.deletedCount > 0) {
          Swal.fire("Deleted!", "Your Coffee has been deleted.", "success");
          const remaining = coffees.filter((cof) => cof._id !== _id);
          setCoffees(remaining);
        }
      });
  }
});
```

```js
// get state by using props from the parent
const CoffeeCard = ({ coffee, coffees, setCoffees }) => {};
```

## [JWT (JSON Web Token)](https://jwt.io/)

```js
## Generate Cookie
-> node
-> require('crypto').randomBytes(64).toString('hex')
```

```js
const jwt = require("jsonwebtoken");
const cookieParser = require("cookie-parser");
```

```js
// Middleware
app.use(
  cors({
    origin: ["http://localhost:5173", "https://car-doctor-b07ce.web.app"],
    credentials: true,
  })
);
app.use(cookieParser());
```

```js
const corsOptions = {
  origin: [
    "http://localhost:5173",
    "http://localhost:5174",
    "https://car-doctor-b07ce.web.app",
  ],
  credentials: true,
  optionSuccessStatus: 200,
};
app.use(cors(corsOptions));
```

```js
// Verify Token
const verifyToken = (req, res, next) => {
  const token = req.cookies?.token;
  if (!token) return res.status(401).send({ message: "unauthorized access" });
  if (token) {
    jwt.verify(token, process.env.ACCESS_TOKEN_SECRET, (err, decoded) => {
      if (err) {
        console.log(err);
        return res.status(401).send({ message: "unauthorized access" });
      }
      console.log(decoded);

      req.user = decoded;
      next();
    });
  }
};
```

```js
// JWT Generate
app.post("/jwt", async (req, res) => {
  const email = req.body;
  const token = jwt.sign(email, process.env.ACCESS_TOKEN_SECRET, {
    expiresIn: "365d",
  });

  res
    .cookie("token", token, {
      httpOnly: true,
      secure: process.env.NODE_ENV === "production",
      sameSite: process.env.NODE_ENV === "production" ? "none" : "strict",
    })
    .send({ success: true });
});
```

```js
// Clear token on logout
app.get("/logout", (req, res) => {
  res
    .clearCookie("token", {
      httpOnly: true,
      secure: process.env.NODE_ENV === "production",
      sameSite: process.env.NODE_ENV === "production" ? "none" : "strict",
      maxAge: 0,
    })
    .send({ success: true });
});
```

```js
// Get all jobs posted by a specific user
app.get("/jobs/:email", verifyToken, async (req, res) => {
  const tokenEmail = req.user.email;
  const email = req.params.email;
  if (tokenEmail !== email) {
    return res.status(403).send({ message: "forbidden access" });
  }
  const query = { "buyer.email": email };
  const result = await jobsCollection.find(query).toArray();
  res.send(result);
});
```
