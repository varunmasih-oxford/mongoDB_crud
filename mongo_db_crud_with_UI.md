# MongoDB CRUD Operations using Express & Mongoose (Step-by-Step Guide)

These notes are prepared **directly from the code shown in your screenshots**. This is a beginner‑friendly explanation of how **CRUD (Create, Read, Update, Delete)** works using:

* Node.js
* Express.js
* MongoDB
* Mongoose

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

* `express()` creates the server
* `app.get('/')` is a test route
* Server runs on **port 3000**

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

* Connects MongoDB using **Mongoose**
* Database name: `mongopractice`
* Schema defines structure of user data
* Model is exported for use in `app.js`

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

* Creates a new user document
* Data is saved in MongoDB
* Returns the inserted document

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

* `find()` → fetches all documents
* `find({condition})` → fetches filtered data

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

* Finds user by `username`
* Updates the `name` field
* `{ new: true }` returns updated document

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

* Finds user by condition
* Deletes matching document
* Returns deleted data

---

## 9. Final CRUD Flow

| Operation | Route     | Mongoose Method      |
| --------- | --------- | -------------------- |
| Create    | `/create` | `create()`           |
| Read      | `/read`   | `find()`             |
| Update    | `/update` | `findOneAndUpdate()` |
| Delete    | `/delete` | `findOneAndDelete()` |

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

## 11. Key Learning Points

* Express handles routing
* Mongoose connects MongoDB
* Schema defines data structure
* CRUD is done using Mongoose methods
* `async/await` handles async DB operations

---

## 12. Next Improvements (Optional)

* Use POST instead of GET
* Use `express.json()`
* Take data from request body
* Add error handling
* Use environment variables

---

---

# Adding UI with EJS (Register, View, Edit, Delete Users)

Below is an **extension of the SAME project**, adding **HTML + CSS UI using EJS** without changing your MongoDB logic.

---

## 13. Install & Setup EJS

```bash
npm install ejs
```

In `app.js`

```js
app.set('view engine', 'ejs');
```

---

## 14. Project Structure

```
project/
│── app.js
│── usermodel.js
│── public/
│   └── style.css
│── views/
│   ├── index.ejs
│   ├── register.ejs
│   └── edit.ejs
```

---

## 15. Middleware (app.js)

```js
app.use(express.urlencoded({ extended: true }));
app.use(express.static('public'));
```

---

## 16. Register User Form (UI)

### Route

```js
app.get('/register', (req, res) => {
  res.render('register');
});
```

### `views/register.ejs`

```html
<!DOCTYPE html>
<html>
<head>
  <link rel="stylesheet" href="/style.css">
</head>
<body>
  <h2>Register User</h2>
  <form action="/register" method="POST">
    <input name="name" placeholder="Name" required>
    <input name="username" placeholder="Username" required>
    <input name="email" placeholder="Email" required>
    <button type="submit">Register</button>
  </form>
</body>
</html>
```

---

## 17. CREATE from Form

```js
app.post('/register', async (req, res) => {
  await userModel.create(req.body);
  res.redirect('/users');
});
```

---

## 18. View All Users (UI)

### Route

```js
app.get('/users', async (req, res) => {
  let users = await userModel.find();
  res.render('index', { users });
});
```

### `views/index.ejs`

```html
<!DOCTYPE html>
<html>
<head>
  <link rel="stylesheet" href="/style.css">
</head>
<body>
  <h2>Registered Users</h2>
  <a href="/register">Add User</a>

  <% users.forEach(user => { %>
    <div class="card">
      <p><b>Name:</b> <%= user.name %></p>
      <p><b>Username:</b> <%= user.username %></p>
      <p><b>Email:</b> <%= user.email %></p>

      <a href="/edit/<%= user._id %>">Edit</a>
      <a href="/delete/<%= user._id %>">Delete</a>
    </div>
  <% }) %>
</body>
</html>
```

---

## 19. Edit User

### Route

```js
app.get('/edit/:id', async (req, res) => {
  let user = await userModel.findById(req.params.id);
  res.render('edit', { user });
});
```

### `views/edit.ejs`

```html
<!DOCTYPE html>
<html>
<head>
  <link rel="stylesheet" href="/style.css">
</head>
<body>
  <h2>Edit User</h2>
  <form action="/edit/<%= user._id %>" method="POST">
    <input name="name" value="<%= user.name %>">
    <input name="username" value="<%= user.username %>">
    <input name="email" value="<%= user.email %>">
    <button>Update</button>
  </form>
</body>
</html>
```

### Update Logic

```js
app.post('/edit/:id', async (req, res) => {
  await userModel.findByIdAndUpdate(req.params.id, req.body);
  res.redirect('/users');
});
```

---

## 20. Delete User

```js
app.get('/delete/:id', async (req, res) => {
  await userModel.findByIdAndDelete(req.params.id);
  res.redirect('/users');
});
```

---

## 21. Basic CSS (`public/style.css`)

```css
body {
  font-family: Arial;
  background: #f4f6f8;
  padding: 20px;
}

input, button {
  display: block;
  margin: 10px 0;
  padding: 8px;
}

.card {
  background: white;
  padding: 15px;
  margin: 10px 0;
  border-radius: 6px;
}

a {
  margin-right: 10px;
}
```

---

