# Getting Started with Node.js, Express.js, and MongoDB

This guide will walk you through setting up a backend application using Node.js, Express.js, and MongoDB, starting from scratch, even if you haven't set up your environment yet.

---

## Steps to Build a Backend Application

### Step 1: Create a Simple Server with Node.js and Express.js

1. **Initialize the Project**:
    ```bash
    npm init -y
    ```

2. **Install Express.js**:
    ```bash
    npm install express
    ```

3. **Create a File**:
    - Name the file `index.js` and add the following code:
      ```javascript
      const express = require('express');
      const app = express();
      const port = 5000;

      app.get('/', (req, res) => {
          res.send('Hello from my server');
      });

      app.get('/data', (req, res) => {
          res.send('More data coming soon.....');
      });

      app.listen(port, () => {
          console.log(`My first server running on port: ${port}`);
      });
      ```

4. **Run the Server**:
    ```bash
    node index.js
    ```

5. **Test**:
    - Visit `http://localhost:5000` in your browser.

---

### Step 2: Enable Auto-Restart with Nodemon

1. **Install Nodemon**:
    ```bash
    npm install -g nodemon
    ```

2. **Run the Server with Nodemon**:
    ```bash
    nodemon index.js
    ```

3. **Observe Auto-Restart**:
    - Modify `index.js` and save the file to see Nodemon restart the server automatically.

---

### Step 3: Build a Complete Server with MongoDB

#### 1. **Install Required Packages**:
    ```bash
    npm install express mongodb dotenv cors body-parser
    ```

#### 2. **Set Up the Directory Structure**:
    ```
    my-node-app/
    ├── src/
    │   ├── routes/
    │   │   └── userRoutes.js
    │   ├── config/
    │   │   └── db.js
    │   └── index.js
    ├── .env
    ├── package.json
    └── README.md
    ```

#### 3. **Configure MongoDB Connection**:
- Create `db.js` in `src/config/`:
  ```javascript
  const { MongoClient } = require('mongodb');

  let db;

  const connectDB = async () => {
      try {
          const client = new MongoClient(process.env.MONGO_URI, {
              useNewUrlParser: true,
              useUnifiedTopology: true,
          });

          await client.connect();
          db = client.db('mydatabase');
          console.log('MongoDB Connected');
      } catch (error) {
          console.error('MongoDB connection error:', error.message);
          process.exit(1);
      }
  };

  const getDB = () => db;

  module.exports = { connectDB, getDB };
  ```

- Add MongoDB connection string in `.env`:
  ```env
  MONGO_URI=mongodb://localhost:27017
  PORT=5000
  ```

#### 4. **Set Up User Routes**:
- Create `userRoutes.js` in `src/routes/`:
  ```javascript
  const express = require('express');
  const { getDB } = require('../config/db');
  const router = express.Router();

  // Create a new user
  router.post('/register', async (req, res) => {
      try {
          const { name, email, password } = req.body;
          const db = getDB();
          const result = await db.collection('users').insertOne({ name, email, password });
          res.status(201).json(result.ops[0]);
      } catch (error) {
          res.status(400).json({ message: error.message });
      }
  });

  // Get all users
  router.get('/users', async (req, res) => {
      try {
          const db = getDB();
          const users = await db.collection('users').find().toArray();
          res.json(users);
      } catch (error) {
          res.status(500).json({ message: error.message });
      }
  });

  module.exports = router;
  ```

#### 5. **Set Up the Main Application**:
- Create `index.js` in `src/`:
  ```javascript
  const express = require('express');
  const dotenv = require('dotenv');
  const cors = require('cors');
  const bodyParser = require('body-parser');
  const { connectDB } = require('./config/db');
  const userRoutes = require('./routes/userRoutes');

  dotenv.config();

  const app = express();

  // Middleware
  app.use(cors());
  app.use(bodyParser.json());

  // Database connection
  connectDB();

  // Routes
  app.use('/api', userRoutes);

  // Start server
  const PORT = process.env.PORT || 5000;
  app.listen(PORT, () => {
      console.log(`Server is running on http://localhost:${PORT}`);
  });
  ```

---

### Running the Application

1. **Start MongoDB**:
    - Run MongoDB locally or use [MongoDB Atlas](https://www.mongodb.com/cloud/atlas).

2. **Start the Server**:
    ```bash
    nodemon src/index.js
    ```

3. **Test the Endpoints**:
    - `POST http://localhost:5000/api/register` (Create a new user)
    - `GET http://localhost:5000/api/users` (Retrieve all users)

---

### Next Steps

1. Add authentication using `bcrypt` and `jsonwebtoken`.
2. Use validation libraries like `express-validator`.
3. Deploy the app using platforms like Heroku or Render.
4. Implement advanced error handling and logging tools.

For more examples, check the [Node.js documentation](https://nodejs.org/) and [MongoDB documentation](https://www.mongodb.com/docs/).
