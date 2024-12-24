# server-with-Node.js-Express.js-and-MongoDB
# Getting Started with Node.js, Express.js, and MongoDB

This guide will walk you through setting up a backend application using Node.js, Express.js, and MongoDB.

---

## Prerequisites

Before starting, ensure you have the following installed on your machine:

- [Node.js](https://nodejs.org/) (includes the `npm` CLI tool)
- [MongoDB](https://www.mongodb.com/try/download/community) (for the database server)
- A code editor like [VS Code](https://code.visualstudio.com/)

---

## Setting Up the Project

Follow these steps to set up a new Node.js project:

1. Open your terminal and create a new directory for your project:

    ```bash
    mkdir my-node-app
    cd my-node-app
    ```

2. Initialize a new Node.js project:

    ```bash
    npm init -y
    ```

3. Install the required dependencies:

    ```bash
    npm install express mongodb dotenv cors body-parser
    ```

4. Create the following directory structure:

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

---

## Setting Up MongoDB Connection

1. Create a `db.js` file in the `src/config/` directory:

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

    const getDB = () => {
      if (!db) {
        throw new Error('Database not connected');
      }
      return db;
    };

    module.exports = { connectDB, getDB };
    ```

2. Add your MongoDB connection string to the `.env` file:

    ```
    MONGO_URI=mongodb://localhost:27017/mydatabase
    PORT=5000
    ```

---

## Creating a Simple API

### User Routes

1. Create a `userRoutes.js` file in the `src/routes/` directory:

    ```javascript
    const express = require('express');
    const { getDB } = require('../config/db');
    const router = express.Router();

    // Create a new user
    router.post('/register', async (req, res) => {
      try {
        const db = getDB();
        const { name, email, password } = req.body;
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

### Main Application File

2. Create an `index.js` file in the `src/` directory:

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

## Running the Application

1. Start the MongoDB server locally or use a MongoDB cloud service like [MongoDB Atlas](https://www.mongodb.com/cloud/atlas).

2. Run your Node.js application:

    ```bash
    node src/index.js
    ```

3. Open Postman or a browser and test the endpoints:

    - `POST http://localhost:5000/api/register` (Create a new user)
    - `GET http://localhost:5000/api/users` (Retrieve all users)

---

## Next Steps

Now that your app is set up, here are some suggestions for what you can do next:

1. **Add Authentication**:
   - Implement user authentication using libraries like `bcrypt` for password hashing and `jsonwebtoken` for token-based authentication.

2. **Validation**:
   - Use `express-validator` or `Joi` for validating user input.

3. **Environment Variables**:
   - Secure sensitive information using `.env` files and tools like `dotenv`.

4. **Advanced Error Handling**:
   - Implement centralized error handling for cleaner code and better debugging.

5. **Deploy Your App**:
   - Use platforms like [Heroku](https://www.heroku.com/), [Render](https://render.com/), or [AWS](https://aws.amazon.com/) to deploy your app.

6. **Add More Features**:
   - Create CRUD operations for other entities in your app.
   - Implement relationships between collections in MongoDB.

7. **Use Advanced Tools**:
   - Integrate a logging library like `winston`.
   - Explore rate limiting with `express-rate-limit`.

For more details and examples, check out the official [Node.js documentation](https://nodejs.org/) and [MongoDB documentation](https://www.mongodb.com/docs/).
