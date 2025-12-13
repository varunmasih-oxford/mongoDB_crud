# MongoDB CRUD Operations using Express & Mongoose (Step-by-Step Guide)

These notes are prepared **directly from the code shown in your screenshots**. This is a beginner‑friendly explanation of how **CRUD (Create, Read, Update, Delete)** works using:

- Node.js
- Express.js
- MongoDB
- Mongoose

---

## 1. Project Setup

### Install Required Packages

```bash
npm init -y
npm install express mongoose
```

---

## 2. Basic Express Server (`app.js`)

```js
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send('hey');
});

app.listen(3000);
```

### Explanation
- `express()` creates the server
- `app.get('/')` is a test route
- Server runs on **port 3000**

---

## 3. MongoDB Connection & Schema (`usermodel.js`)

```js
const mongoose = require('mongoose');

mongoose.connect('mongodb://127.0.0.1:27017/mongopractice');

const userSchema = mongoose.Schema({
  name: String,
  username: String,
  email: String
});

module.exports = mongoose.model('user', userSchema);
```

### Explanation
- Connects MongoDB using **Mongoose**
- Database name: `mongopractice`
- Schema defines structure of user data
- Model is exported for use in `app.js`

---

## 4. Import User Model in `app.js`

```js
const userModel = require('./usermodel');
```

This allows Express routes to interact with MongoDB.

---

## 5. CREATE Operation (Insert Data)

### Route: `/create`

```js
app.get('/create', async (req, res) => {
  let createdUser = await userModel.create({
    name: 'harsh',
    email: 'harsh@gmail.com',
    username: 'harsh'
  });

  res.send(createdUser);
});
```

### What Happens?
- Creates a new user document
- Data is saved in MongoDB
- Returns the inserted document

---

## 6. READ Operation (Fetch Data)

### Read All Users

```js
app.get('/read', async (req, res) => {
  let users = await userModel.find();
  res.send(users);
});
```

### Read Specific User

```js
app.get('/read', async (req, res) => {
  let users = await userModel.find({ username: 'harsh' });
  res.send(users);
});
```

### Explanation
- `find()` → fetches all documents
- `find({condition})` → fetches filtered data

---

## 7. UPDATE Operation

### Route: `/update`

```js
app.get('/update', async (req, res) => {
  let updatedUser = await userModel.findOneAndUpdate(
    { username: 'harsh' },
    { name: 'harsh vadi' },
    { new: true }
  );

  res.send(updatedUser);
});
```

### Explanation
- Finds user by `username`
- Updates the `name` field
- `{ new: true }` returns updated document

---

## 8. DELETE Operation

### Route: `/delete`

```js
app.get('/delete', async (req, res) => {
  let users = await userModel.findOneAndDelete({ username: 'harsh' });
  res.send(users);
});
```

### Explanation
- Finds user by condition
- Deletes matching document
- Returns deleted data

---

## 9. Final CRUD Flow

| Operation | Route | Mongoose Method |
|--------|------|----------------|
| Create | `/create` | `create()` |
| Read | `/read` | `find()` |
| Update | `/update` | `findOneAndUpdate()` |
| Delete | `/delete` | `findOneAndDelete()` |

---

## 10. Testing the API

Open browser or Postman:

```
http://localhost:3000/create
http://localhost:3000/read
http://localhost:3000/update
http://localhost:3000/delete
```

---

