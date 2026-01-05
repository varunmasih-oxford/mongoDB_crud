# REST API with MongoDB (Node.js + Express)

A beginner-friendly step-by-step guide to building a REST API using **MongoDB**, **Node.js**, and **Express.js**.  
This structure follows **MERN stack best practices** and is suitable for teaching and real projects.

---

## 1. What is an API with MongoDB?

An **API (Application Programming Interface)** allows communication between a client and a database.

### Request Flow
```

Client → Express API → MongoDB → API → Client

````

---

## 2. Tech Stack Used

- **Node.js** – JavaScript runtime
- **Express.js** – Web framework for APIs
- **MongoDB** – NoSQL database
- **Mongoose** – ODM for MongoDB

---

## 3. Project Setup

### Initialize Project
```bash
mkdir mongo-api
cd mongo-api
npm init -y
````

### Install Dependencies

```bash
npm install express mongoose dotenv cors
npm install nodemon --save-dev
```

---

## 4. Folder Structure (MVC Pattern)

```
mongo-api/
│
├── config/
│   └── db.js
├── models/
│   └── User.js
├── controllers/
│   └── userController.js
├── routes/
│   └── userRoutes.js
├── .env
├── server.js
```

---

## 5. MongoDB Connection

### `.env`

```env
PORT=5000
MONGO_URI=mongodb://127.0.0.1:27017/userdb
```

### `config/db.js`

```js
const mongoose = require("mongoose");

const connectDB = async () => {
  try {
    await mongoose.connect(process.env.MONGO_URI);
    console.log("MongoDB Connected");
  } catch (error) {
    console.error(error);
    process.exit(1);
  }
};

module.exports = connectDB;
```

---

## 6. Create Mongoose Model

### `models/User.js`

```js
const mongoose = require("mongoose");

const userSchema = new mongoose.Schema(
  {
    name: {
      type: String,
      required: true
    },
    email: {
      type: String,
      required: true,
      unique: true
    },
    age: Number
  },
  { timestamps: true }
);

module.exports = mongoose.model("User", userSchema);
```

---

## 7. Controller (Business Logic)

### `controllers/userController.js`

```js
const User = require("../models/User");

// Create User
exports.createUser = async (req, res) => {
  try {
    const user = await User.create(req.body);
    res.status(201).json(user);
  } catch (error) {
    res.status(400).json({ message: error.message });
  }
};

// Get All Users
exports.getUsers = async (req, res) => {
  const users = await User.find();
  res.json(users);
};

// Get User by ID
exports.getUserById = async (req, res) => {
  const user = await User.findById(req.params.id);
  res.json(user);
};

// Update User
exports.updateUser = async (req, res) => {
  const user = await User.findByIdAndUpdate(
    req.params.id,
    req.body,
    { new: true }
  );
  res.json(user);
};

// Delete User
exports.deleteUser = async (req, res) => {
  await User.findByIdAndDelete(req.params.id);
  res.json({ message: "User deleted successfully" });
};
```

---

## 8. Routes

### `routes/userRoutes.js`

```js
const express = require("express");
const router = express.Router();
const {
  createUser,
  getUsers,
  getUserById,
  updateUser,
  deleteUser
} = require("../controllers/userController");

router.post("/", createUser);
router.get("/", getUsers);
router.get("/:id", getUserById);
router.put("/:id", updateUser);
router.delete("/:id", deleteUser);

module.exports = router;
```

---

## 9. Server Setup

### `server.js`

```js
const express = require("express");
const dotenv = require("dotenv");
const connectDB = require("./config/db");

dotenv.config();
connectDB();

const app = express();
app.use(express.json());

app.use("/api/users", require("./routes/userRoutes"));

const PORT = process.env.PORT || 5000;
app.listen(PORT, () =>
  console.log(`Server running on port ${PORT}`)
);
```

---

## 10. API Testing (Postman)

### Create User

```
POST /api/users
```

```json
{
  "name": "Rahul",
  "email": "rahul@gmail.com",
  "age": 22
}
```

### Get All Users

```
GET /api/users
```

### Update User

```
PUT /api/users/:id
```

### Delete User

```
DELETE /api/users/:id
```

---

```
