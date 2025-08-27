# Contacts Management API - Backend

A RESTful API backend for managing personal contacts with user authentication and authorization. Built with Express.js, MongoDB, and JWT authentication.

## Features

- **User Authentication & Authorization**

  - User registration and login
  - JWT token-based authentication
  - Password hashing with bcrypt
  - Protected routes with token validation

- **Contact Management**

  - Create, read, update, and delete contacts
  - User-specific contact isolation
  - Contact validation (name, email, phone required)
  - MongoDB integration with Mongoose ODM

- **Security Features**
  - Input validation and sanitization
  - Error handling middleware
  - User authorization for contact operations
  - Secure password storage

## Tech Stack

<img src="https://skillicons.dev/icons?i=express" title="Express.js" />&#8239;
<img src="https://skillicons.dev/icons?i=mongodb" title="MongoDB" />&#8239;

- **Node.js** - JavaScript runtime environment  
- **Express.js** - Fast, unopinionated web framework  
- **MongoDB** - NoSQL document database  
- **Mongoose** - MongoDB object modeling for Node.js  
- **JWT (JSON Web Tokens)** - Authentication and authorization  
- **bcrypt** - Secure password hashing
- **Nodemon** - Development tool for auto-restarting  

## Prerequisites

Before running this application, make sure you have:

- Node.js (v14 or higher)
- MongoDB database (local or cloud instance like MongoDB Atlas)
- npm or yarn package manager

## Installation & Setup

1. **Clone the repository**

   ```bash
   git clone https://github.com/Kisara-k/Contacts-Express-MongoDB.git
   cd Contacts-Express-MongoDB/backend
   ```

2. **Install dependencies**

   ```bash
   npm install
   ```

3. **Environment Configuration**

   Create a `.env` file in the backend directory with the following variables:

   ```env
   PORT=5000
   CONNECTION_STRING=mongodb://localhost:27017/mycontacts
   ACCESS_TOKEN_SECRET=your_jwt_secret_key_here
   ```

   **Environment Variables Explained:**

   - `PORT`: Server port number (default: 5000)
   - `CONNECTION_STRING`: MongoDB connection URI
   - `ACCESS_TOKEN_SECRET`: Secret key for JWT token signing

4. **Start the server**

   For development (with auto-restart):

   ```bash
   npm run dev
   ```

   For production:

   ```bash
   npm start
   ```

The server will start on `http://localhost:5000` (or your specified PORT).

## API Documentation

### Base URL

```
http://localhost:5000/api
```

### Authentication Endpoints

#### Register User

- **POST** `/users/register`
- **Body:**
  ```json
  {
    "username": "john_doe",
    "email": "john@example.com",
    "password": "securePassword123"
  }
  ```
- **Response:** User object with JWT token

#### Login User

- **POST** `/users/login`
- **Body:**
  ```json
  {
    "email": "john@example.com",
    "password": "securePassword123"
  }
  ```
- **Response:** User object with JWT token

#### Get Current User

- **GET** `/users/current`
- **Headers:** `Authorization: Bearer <token>`
- **Response:** Current user information

### Contact Endpoints

All contact endpoints require authentication via Bearer token in the Authorization header.

#### Get All Contacts

- **GET** `/contacts`
- **Headers:** `Authorization: Bearer <token>`
- **Response:** Array of user's contacts

#### Create Contact

- **POST** `/contacts`
- **Headers:** `Authorization: Bearer <token>`
- **Body:**
  ```json
  {
    "name": "John Doe",
    "email": "john@example.com",
    "phone": "+1234567890"
  }
  ```
- **Response:** Created contact object

#### Get Single Contact

- **GET** `/contacts/:id`
- **Headers:** `Authorization: Bearer <token>`
- **Response:** Contact object

#### Update Contact

- **PUT** `/contacts/:id`
- **Headers:** `Authorization: Bearer <token>`
- **Body:**
  ```json
  {
    "name": "John Smith",
    "email": "johnsmith@example.com",
    "phone": "+1234567890"
  }
  ```
- **Response:** Updated contact object

#### Delete Contact

- **DELETE** `/contacts/:id`
- **Headers:** `Authorization: Bearer <token>`
- **Response:** Deleted contact object

## Project Structure

```
backend/
├── config/
│   └── dbConnection.js      # MongoDB connection configuration
├── controllers/
│   ├── contactController.js # Contact CRUD operations
│   └── userController.js    # User authentication logic
├── middleware/
│   ├── errorHandler.js      # Global error handling
│   └── validateTokenHandler.js # JWT token validation
├── models/
│   ├── contactModel.js      # Contact schema definition
│   └── userModel.js         # User schema definition
├── routes/
│   ├── contactRoutes.js     # Contact API routes
│   └── userRoutes.js        # User API routes
├── constants.js             # HTTP status code constants
├── server.js               # Main application entry point
├── package.json            # Dependencies and scripts
└── .env                    # Environment variables (create this)
```

## Authentication Flow

1. **Registration/Login:** User provides credentials
2. **Token Generation:** Server creates JWT token upon successful authentication
3. **Token Storage:** Client stores token (recommended: httpOnly cookies or localStorage)
4. **API Requests:** Include token in Authorization header: `Bearer <token>`
5. **Token Validation:** Server validates token on protected routes

## Security Features

- **Password Hashing:** Uses bcrypt for secure password storage
- **JWT Authentication:** Stateless authentication with configurable expiry
- **Input Validation:** Required field validation for all endpoints
- **User Authorization:** Users can only access their own contacts
- **Error Handling:** Comprehensive error responses without sensitive data exposure

## Testing the API

You can test the API using tools like:

- **Postman:** Import and test all endpoints
- **cURL:** Command-line testing
- **Thunder Client:** VS Code extension
- **Insomnia:** REST client

### Example cURL Commands

**Register a user:**

```bash
curl -X POST http://localhost:5000/api/users/register \
-H "Content-Type: application/json" \
-d '{"username":"testuser","email":"test@example.com","password":"password123"}'
```

**Create a contact (with token):**

```bash
curl -X POST http://localhost:5000/api/contacts \
-H "Content-Type: application/json" \
-H "Authorization: Bearer YOUR_JWT_TOKEN" \
-d '{"name":"John Doe","email":"john@example.com","phone":"1234567890"}'
```

## Common Issues & Troubleshooting

1. **MongoDB Connection Issues**

   - Ensure MongoDB is running
   - Check CONNECTION_STRING in .env file
   - Verify network connectivity for cloud databases

2. **JWT Token Errors**

   - Ensure ACCESS_TOKEN_SECRET is set in .env
   - Check token format in Authorization header
   - Verify token hasn't expired

3. **CORS Issues** (when connecting frontend)
   - Install and configure cors middleware if needed
   - Add appropriate CORS headers for frontend domain

## Deployment

For production deployment:

1. **Environment Variables:** Set all required env vars on your hosting platform
2. **Database:** Use MongoDB Atlas or other cloud MongoDB service
3. **Security:** Use strong JWT secrets and enable additional security headers
4. **HTTPS:** Always use HTTPS in production
5. **Process Manager:** Use PM2 or similar for process management

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## ‍Author

**Dipesh Malvia**

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## Support

For support and questions, please open an issue in the GitHub repository.

---

**Happy Coding! **
