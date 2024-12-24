# Getting Started with Node.js, Express.js, and MongoDB

This guide will walk you through setting up a backend application using Node.js, Express.js, and MongoDB, starting from scratch, even if you haven't set up your environment yet.

---

## Step 1: Simple Server with Node.js and Express.js

Start by creating a very basic server.

### Code Example

1. Create a file named `index.js`:

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

### Running the Server

1. Install Node.js from [Node.js official website](https://nodejs.org/).
2. Initialize your project:

    ```bash
    npm init -y
    ```
3. Install Express.js:

    ```bash
    npm install express
    ```
4. Run the server:

    ```bash
    node index.js
    ```
5. Visit `http://localhost:5000` in your browser to see the message.

---

## Step 2: Auto-Restart Server with Nodemon

To avoid restarting the server manually every time you make changes, use Nodemon.

1. Install Nodemon globally:

    ```bash
    npm install -g nodemon
    ```

2. Start the server with Nodemon:

    ```bash
    nodemon index.js
    ```

3. Now, any changes to your code will automatically restart the server.

---

## Step 3: Complete Server with Node.js, Express.js, and MongoDB

Now, let's build a complete server using MongoDB and other essential packages.

### Setting Up the Project

1. Install required packages:

    ```bash
    npm install express mongoose dotenv cors body-parser
    ```

2. Create the following structure:

    ```
    my-node-app/
    ├── src/
    │   ├── models/
    │   │   └── User.js
    │   ├── routes/
    │   │   └── userRoutes.js
    │   ├── config/
    │   │   └── db.js
    │   └── index.js
    ├── .env
    ├── package.json
    └── README.md
    ```

### MongoDB Connection

1. Create `db.js` in `src/config/`:

    ```javascript
    const mongoose = require('mongoose');

    const connectDB = async () => {
        try {
            await mongoose.connect(process.env.MONGO_URI, {
                useNewUrlParser: true,
                useUnifiedTopology: true,
            });
            console.log('MongoDB Connected');
        } catch (error) {
            console.error('MongoDB connection error:', error.message);
            process.exit(1);
        }
    };

    module.exports = connectDB;
    ```

2. Add your MongoDB connection string to `.env`:

    ```env
    MONGO_URI=mongodb://localhost:27017/mydatabase
    PORT=5000
    ```

### User Model

1. Create `User.js` in `src/models/`:

    ```javascript
    const mongoose = require('mongoose');

    const userSchema = new mongoose.Schema({
        name: { type: String, required: true },
        email: { type: String, required: true, unique: true },
        password: { type: String, required: true },
    });

    const User = mongoose.model('User', userSchema);

    module.exports = User;
    ```

### User Routes

1. Create `userRoutes.js` in `src/routes/`:

    ```javascript
    const express = require('express');
    const User = require('../models/User');
    const router = express.Router();

    // Create a new user
    router.post('/register', async (req, res) => {
        try {
            const { name, email, password } = req.body;
            const user = new User({ name, email, password });
            await user.save();
            res.status(201).json(user);
        } catch (error) {
            res.status(400).json({ message: error.message });
        }
    });

    // Get all users
    router.get('/users', async (req, res) => {
        try {
            const users = await User.find();
            res.json(users);
        } catch (error) {
            res.status(500).json({ message: error.message });
        }
    });

    module.exports = router;
    ```

### Main Application File

1. Create `index.js` in `src/`:

    ```javascript
    const express = require('express');
    const dotenv = require('dotenv');
    const cors = require('cors');
    const bodyParser = require('body-parser');
    const connectDB = require('./config/db');
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
    nodemon src/index.js
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

3. **Advanced Error Handling**:
    - Implement centralized error handling for cleaner code and better debugging.

4. **Deploy Your App**:
    - Use platforms like [Heroku](https://www.heroku.com/), [Render](https://render.com/), or [AWS](https://aws.amazon.com/) to deploy your app.

5. **Add More Features**:
    - Create CRUD operations for other entities in your app.
    - Implement relationships between collections in MongoDB.

6. **Use Advanced Tools**:
    - Integrate a logging library like `winston`.
    - Explore rate limiting with `express-rate-limit`.

For more details and examples, check out the official [Node.js documentation](https://nodejs.org/) and [MongoDB documentation](https://www.mongodb.com/docs/).
