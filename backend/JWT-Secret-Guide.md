# JWT Secret Token Guide

## In This Project

### 1. Register a User (Public Endpoint)

POST: `http://localhost:5000/api/users/register`

```json
{
  "username": "Kisara",
  "email": "Kisara@vk.com",
  "password": "password"
}
```
Response:
```json
{
	"_id": "68b026c2deabe7635d1e9d52",
	"email": "Kisara@vk.com"
}
```

### 2. Login and get Access Token (Public Endpoint)

POST: `http://localhost:5000/api/users/login`

```json
{
  "email": "Kisara@vk.com",
  "password": "password"
}
```
Response:
```json
{
	"accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjp7InVzZXJuYW1lIjoiRnJhbnoiLCJlbWFpbCI6IkZyYW56QHZrLmNvbSIsImlkIjoiNjhiMDI2YzJkZWFiZTc2MzVkMWU5ZDUyIn0sImlhdCI6MTc1NjM3NTI4OSwiZXhwIjoxNzU2Mzc2MTg5fQ.w0hQvBSoBqz9Kixek4NRhdt2NROxy33UIoaHgoPg4Wo"
}
```

### 3. Access Protected Routes

GET: `http://localhost:5000/api/users/current`

Headers:
Key: `Authorization`
Value: `Bearer <accessToken>`

Response:
```json
{
	"username": "Kisara",
	"email": "Kisara@vk.com",
	"id": "68b026c2deabe7635d1e9d52"
}
```

## What is a JWT Token?

**JWT (JSON Web Token)** is a compact, URL-safe token format used for securely transmitting information between parties. A JWT token consists of three parts separated by dots (`.`):

```
header.payload.signature
```

Example JWT:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

## Why Do You Need a JWT Secret?

The **JWT Secret** (also called signing key) is crucial for:

### **Security & Integrity**

- **Digital Signature**: The secret is used to create a digital signature for the token
- **Tampering Prevention**: If someone modifies the token payload, the signature becomes invalid
- **Authenticity Verification**: Only your server can create valid tokens with your secret

### **Authentication Flow**

1. User logs in with valid credentials
2. Server creates a JWT token signed with the secret
3. Token is sent to the client
4. Client includes token in subsequent requests
5. Server verifies the token using the same secret

### **Authorization**

- Tokens can contain user roles, permissions, and other claims
- Server can trust the token content because it's cryptographically signed
- No need to query the database on every request

## How JWT Tokens Work in This Project

In our Contacts API, JWT tokens are used for:

```javascript
// When user logs in (userController.js)
const token = jwt.sign(
  {
    user: {
      username: user.username,
      email: user.email,
      id: user.id,
    },
  },
  process.env.ACCESS_TOKEN_SECRET, // ← This is your secret!
  { expiresIn: "15m" }
);
```

```javascript
// When validating requests (validateTokenHandler.js)
const decoded = jwt.verify(token, process.env.ACCESS_TOKEN_SECRET);
```

## Why Your Secret Must Be Secure

### **What Happens with a Weak Secret?**

- Attackers can forge valid tokens
- Unauthorized access to user accounts
- Data breaches and security compromises

### **What Makes a Good Secret?**

- **Length**: At least 32 characters (256 bits)
- **Randomness**: Cryptographically secure random generation
- **Unpredictability**: Not based on dictionary words or patterns
- **Uniqueness**: Different for each environment

## Generating JWT Secret with Node.js Crypto Module

### Method 1: Command Line (Quick & Easy)

Open your terminal/command prompt and run:

```bash
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

**Output Example:**

```
a7f8d9e2c1b4e6f3a8d9c2e1f4b7e8d9c2a5f8e1d4b7c8e9f2a5d8c1e4f7b8e9
```

### Method 2: Creating a Secret Generator Script

Create a file called `generate-jwt-secret.js`:

```javascript
const crypto = require("crypto");

console.log("JWT Secret Generator");
console.log("========================\n");

// Generate different types of secrets
const hexSecret = crypto.randomBytes(32).toString("hex");
const base64Secret = crypto.randomBytes(32).toString("base64");
const base64UrlSecret = crypto.randomBytes(32).toString("base64url");

console.log("Hexadecimal (Recommended):");
console.log(hexSecret);
console.log("\nBase64:");
console.log(base64Secret);
console.log("\nBase64URL:");
console.log(base64UrlSecret);

console.log("\nCopy one of the above secrets to your .env file");
console.log("Example: ACCESS_TOKEN_SECRET=" + hexSecret);
```

Run the script:

```bash
node generate-jwt-secret.js
```

### Method 3: Interactive Node.js REPL

1. Open Node.js REPL:

   ```bash
   node
   ```

2. Generate secret:

   ```javascript
   const crypto = require("crypto");
   const secret = crypto.randomBytes(32).toString("hex");
   console.log("Your JWT Secret:", secret);
   ```

3. Exit REPL:
   ```javascript
   .exit
   ```

### Method 4: Different Secret Lengths

```javascript
const crypto = require("crypto");

// Different security levels
const secret256bit = crypto.randomBytes(32).toString("hex"); // 256 bits (Recommended)
const secret384bit = crypto.randomBytes(48).toString("hex"); // 384 bits (High Security)
const secret512bit = crypto.randomBytes(64).toString("hex"); // 512 bits (Maximum Security)

console.log("256-bit:", secret256bit);
console.log("384-bit:", secret384bit);
console.log("512-bit:", secret512bit);
```

## Setting Up Your JWT Secret

### Step 1: Generate the Secret

```bash
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

### Step 2: Add to .env File

```env
# .env file
PORT=5000
CONNECTION_STRING=mongodb://localhost:27017/mycontacts
ACCESS_TOKEN_SECRET=your_generated_secret_here
```

### Step 3: Verify It's Working

Check your application logs when starting the server. If the JWT secret is missing, you should see authentication errors.

## Security Best Practices

### **Do This:**

- Generate secrets using cryptographic libraries
- Use different secrets for different environments
- Store secrets in environment variables
- Use at least 256-bit (32-byte) secrets
- Rotate secrets periodically
- Never commit secrets to version control

### **Never Do This:**

- Use simple passwords as secrets
- Hardcode secrets in your source code
- Share secrets in plain text
- Use the same secret across multiple projects
- Store secrets in client-side code

## Environment-Specific Secrets

```bash
# Development
ACCESS_TOKEN_SECRET=dev_a7f8d9e2c1b4e6f3a8d9c2e1f4b7e8d9c2a5f8e1d4b7c8e9f2a5d8c1e4f7b8e9

# Staging
ACCESS_TOKEN_SECRET=staging_b8g9e3f4d2c5f7g4b9e3d2f5c8g9f3e6d3b6g9f2e5c8f9g3b6e9d2f5c8g9f3

# Production
ACCESS_TOKEN_SECRET=prod_c9h4g5f6e3d7g8h5c4g6e3f8d9h4g7f8e4c7h4g6f9e3d8h5g9c7f6e9d4g8h5f7
```

## Troubleshooting Common Issues

### Error: "jwt must be provided"

- Check if ACCESS_TOKEN_SECRET is set in .env
- Verify .env file is in the correct directory
- Ensure dotenv is configured in server.js

### Error: "invalid signature"

- Secret in .env doesn't match the one used to sign the token
- Token might be corrupted or tampered with
- Check for extra spaces in .env file

### Error: "jwt expired"

- Token has exceeded its expiration time
- User needs to log in again
- Consider implementing refresh tokens for better UX

## Conclusion

A properly generated JWT secret is the foundation of your application's security. Using Node.js crypto module ensures you get cryptographically secure random bytes that are suitable for production use.

**Remember**: Your JWT secret is like the master key to your application - treat it with the same security as you would treat passwords or API keys.

---

**Related Files:**

- `server.js` - Main application setup
- `controllers/userController.js` - JWT token creation
- `middleware/validateTokenHandler.js` - JWT token verification
- `.env` - Environment variables (create this file)
