# Advanced User Management System

## Overview

A production-grade user management and authentication platform built with the MEAN stack (MongoDB, Express.js, Angular.js, Node.js). This system provides enterprise-level account management, role-based access control, and a comprehensive administrative interface for managing user accounts at scale.

**Core Capabilities:**

- **Authentication & Authorization:** JWT-based token authentication with 24-hour expiry
- **User Account Management:** Registration, profile updates, and account lifecycle management
- **Role-Based Access Control:** Three-tier permission model (User, Moderator, Admin)
- **Administrative Dashboard:** Advanced user search, filtering, batch operations, and account modifications
- **Security:** bcryptjs password hashing, input validation, and secure session management
- **Responsive Design:** Mobile-first UI with Bootstrap framework
- **Extensible Architecture:** Passport.js strategies for OAuth integration (Facebook, Twitter, Google)

---

## System Requirements

This application requires the following components:

- **Node.js:** v14.0.0+ ([Download](https://nodejs.org/))
- **npm:** v6.0.0+ (included with Node.js)
- **MongoDB:** v4.0+ ([Community Edition](https://www.mongodb.com/try/download/community))
  - Running as a system service on port 27017 (default)
- **Git:** For cloning and version control (optional)

---

## Installation Guide

### 1. Clone Repository

```bash
git clone https://github.com/mahmoud-el-tohamy/Simple-MEAN-Stack-Page.git
```

### 2. Install Dependencies

```bash
npm install
```

This installs production dependencies:

- **Express.js:** RESTful API framework
- **Mongoose:** MongoDB object modeling
- **Angular.js:** Frontend MVC framework
- **bcryptjs:** Password hashing with salt rounds
- **jsonwebtoken:** JWT token generation and validation
- **Passport.js:** Authentication middleware with social login strategies

### 3. MongoDB Setup

**Verify MongoDB is running:**

**Linux (systemd):**

```bash
sudo systemctl status mongod
sudo systemctl start mongod  # If not running
```

**macOS (Homebrew):**

```bash
brew services start mongodb-community
```

**Windows:**

- MongoDB automatically runs as a Windows Service after installation
- Verify in Services.msc

### 4. Database Configuration

MongoDB connection settings are defined in `server.js`:

```javascript
mongoose.connect("mongodb://localhost:27017/user_management_db", {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});
```

To connect to a remote MongoDB instance, modify the connection string:

```javascript
mongoose.connect("mongodb://username:password@host:port/user_management_db");
```

### 5. Email Configuration (Optional)

Email notifications are mocked in development to prevent authentication failures. For production deployment:

1. Edit `app/routes/api.js`
2. Configure Nodemailer with your SMTP provider:

```javascript
var client = nodemailer.createTransport({
  host: "smtp.gmail.com",
  port: 587,
  secure: false,
  auth: {
    user: process.env.EMAIL_USER,
    pass: process.env.EMAIL_PASS,
  },
});
```

Use environment variables to store sensitive credentials.

---

## Running the Application

### Prerequisites Check

Before launching, ensure:

1. MongoDB service is running
2. All dependencies are installed (`npm install`)
3. Port 8080 is available

### Start the Application

```bash
npm start
```

Expected output:

```
> user_management_db@1.0.0 start
> node server.js

Running the server on port 8080
Successfully connected to MongoDB
```

### Access the System

Navigate to your browser:

```
http://localhost:8080
```

**Server Configuration:**

- Default Port: 8080 (configurable in `server.js`)
- Environment: Development (for production, set NODE_ENV=production)

---

## API Endpoints

### Authentication

| Endpoint             | Method | Payload                             | Response                   |
| -------------------- | ------ | ----------------------------------- | -------------------------- |
| `/api/users`         | POST   | `{name, email, username, password}` | New user object + token    |
| `/api/authenticate`  | POST   | `{username, password}`              | Authenticated user + token |
| `/api/checkusername` | POST   | `{username}`                        | `{available: boolean}`     |
| `/api/checkemail`    | POST   | `{email}`                           | `{available: boolean}`     |

### User Profile

| Endpoint       | Method | Authorization | Response          |
| -------------- | ------ | ------------- | ----------------- |
| `/api/profile` | GET    | JWT Token     | Current user data |

### User Management (Admin/Moderator)

| Endpoint         | Method | Authorization   | Action                                 |
| ---------------- | ------ | --------------- | -------------------------------------- |
| `/api/users`     | GET    | Admin/Moderator | Retrieve all users with pagination     |
| `/api/users/:id` | GET    | Admin/Moderator | Retrieve specific user by ID           |
| `/api/users/:id` | PUT    | Admin/Moderator | Update user (name, email, permissions) |
| `/api/users/:id` | DELETE | Admin only      | Permanently delete user account        |

### Navigation Routes

| Route         | Method | Purpose           |
| ------------- | ------ | ----------------- |
| `/`           | GET    | Home page         |
| `/register`   | GET    | Registration form |
| `/login`      | GET    | Login form        |
| `/profile`    | GET    | User profile      |
| `/management` | GET    | Admin dashboard   |
| `/logout`     | GET    | Terminate session |

---

## Architecture & Project Structure

```
user-management-system/
├── server.js                    # Express.js server, middleware configuration, DB connection
├── package.json                 # Dependencies and npm scripts
├── README.md                    # This file
├── app/
│   ├── models/
│   │   └── user.js             # MongoDB schema with validation and password hashing middleware
│   ├── routes/
│   │   └── api.js              # RESTful API endpoints for authentication and user management
│   └── passport/
│       └── passport.js         # Passport.js authentication strategies
└── public/
    └── app/
        ├── app.js              # Angular.js application bootstrap
        ├── routes.js           # Client-side routing configuration
        ├── controllers/        # Angular controllers for business logic
        │   ├── mainCtrl.js     # Navigation and session management
        │   ├── userCtrl.js     # User profile and authentication
        │   ├── managementCtrl.js # Admin dashboard functionality
        │   └── emailCtrl.js    # Email-related operations
        ├── services/           # Angular services for API communication
        │   ├── userServices.js # User API interactions
        │   └── authServices.js # Authentication state management
        ├── views/              # HTML templates
        │   ├── index.html      # Main layout with navigation
        │   └── pages/
        │       ├── home.html
        │       ├── about.html
        │       ├── users/      # Authentication and profile pages
        │       └── management/ # Admin interface pages
        └── assets/
            ├── css/            # Bootstrap and custom styling
            ├── js/             # Third-party libraries (Angular, jQuery)
            ├── fonts/          # Font assets
            └── images/         # Application imagery
```

### Technology Stack

| Layer    | Technology             | Version |
| -------- | ---------------------- | ------- |
| Database | MongoDB                | 4.0+    |
| Backend  | Node.js + Express.js   | 4.14.0  |
| Frontend | Angular.js + Bootstrap | 1.x     |
| Auth     | JWT + bcryptjs         | -       |
| ODM      | Mongoose               | 5.13+   |

---

## Access Control Model

The system implements a three-tier role hierarchy:

| Role          | Permissions                                       |
| ------------- | ------------------------------------------------- |
| **User**      | View own profile, manage account password         |
| **Moderator** | User permissions + view all users, edit user data |
| **Admin**     | Full system access including user deletion        |

Role assignment is managed through the MongoDB User schema `userPermission` field.

---

## Security Features

- **Password Security:** bcryptjs hashing with configurable salt rounds
- **Token Authentication:** JWT with 24-hour expiration window
- **Input Validation:** Mongoose schema validators on all user inputs
- **Session Management:** Secure session handling with express-session
- **CSRF Protection:** Can be enabled via middleware configuration
- **Email Verification:** Optional two-factor verification pipeline ready for implementation

---

## Troubleshooting & Support

### MongoDB Connection Failures

**Issue:** `MongoError: connect ECONNREFUSED 127.0.0.1:27017`

**Resolution:**

1. Verify MongoDB is running: `sudo systemctl status mongod`
2. Start MongoDB: `sudo systemctl start mongod`
3. Check connection string in `server.js`
4. Ensure port 27017 is not blocked by firewall

### Port Conflicts

**Issue:** `EADDRINUSE: address already in use :::8080`

**Resolution:**

Find and terminate the existing process:

```bash
lsof -i :8080          # List processes using port 8080
kill -9 <PID>          # Terminate the process
```

Or modify the port in `server.js`:

```javascript
const port = process.env.PORT || 3000;
```

### Module Resolution Errors

**Issue:** `Cannot find module 'express'`

**Resolution:**

Clean install dependencies:

```bash
rm -rf node_modules package-lock.json
npm install
```

### Email Service Issues (Development)

Email notifications are intentionally mocked in development mode to prevent SMTP authentication failures. This prevents blocking on email service errors during testing. To enable production email:

1. Configure SMTP credentials in environment variables
2. Replace mockSendMail with actual Nodemailer transport in `app/routes/api.js`
3. Test thoroughly in a staging environment before production deployment

---

## Performance Optimization

- **Database Indexing:** User schema includes indexes on `username` and `email` for faster queries
- **Pagination:** User list queries support limit/offset parameters to handle large datasets
- **Caching:** Session tokens reduce repeated authentication database calls
- **Lazy Loading:** Angular routes load views on-demand to minimize initial bundle size

---

## Development Roadmap

**Potential Enhancements:**

- [ ] Two-factor authentication (2FA) integration
- [ ] Redis caching layer for session management
- [ ] GraphQL API option alongside REST endpoints
- [ ] User activity audit logging
- [ ] Export user data functionality (GDPR compliance)
- [ ] Rate limiting on authentication endpoints
- [ ] Docker containerization for simplified deployment
- [ ] Automated test suite (Jest/Mocha)

---

## License

ISC License - See LICENSE file for details

---

## Author

**Mahmoud El-Tohamy**

A full-stack JavaScript developer specializing in MEAN stack applications and user management systems.

---

## Support & Contributions

For bug reports or feature requests, please open an issue in the repository. All pull requests are reviewed and considered for integration.

---

## Development Notes

- **Password Requirements:** Passwords must contain at least one uppercase letter, one lowercase letter, one number, one special character, and be 8-35 characters long.
- **Email Validation:** Valid email format is required during registration.
- **Username Requirements:** Alphanumeric characters only, 3-25 characters long.
- **JWT Token Expiry:** Login tokens expire after 24 hours.
- **Database:** User passwords are encrypted using bcrypt before storage.

---

## Testing the Application

### 1. Register a New User

1. Navigate to `http://localhost:8080/register`
2. Fill in the registration form with:
   - First Name: `John`
   - Last Name: `Doe`
   - Email: `john.doe@example.com`
   - Username: `johndoe123`
   - Password: `Test@12345` (must meet requirements)
3. Click "Register"
4. You will be redirected to login

### 2. Login

1. Navigate to `http://localhost:8080/login`
2. Enter your username and password
3. Click "Sign In"
4. You will be redirected to your profile page

### 3. Access Management Dashboard

1. Log in with an admin or moderator account
2. Click "Management" in the navigation bar
3. Search, filter, and manage users

---

## Performance & Deployment

### For Production Deployment:

1. **Set Environment Variables:**

   ```bash
   export NODE_ENV=production
   export MONGO_URI=your_production_mongodb_uri
   ```

2. **Optimize Angular:**

   - Minify CSS and JavaScript
   - Use Angular production build tools

3. **Secure Password Storage:**

   - Ensure all passwords are encrypted (bcrypt is already implemented)

4. **Configure Real Email Service:**

   - Replace mocked email in `app/routes/api.js` with real SMTP/SendGrid credentials

5. **Enable HTTPS:**
   - Implement SSL certificates
   - Update callback URLs in social authentication

---

## Support & Contact

For questions or issues related to this application:

- **Developer:** Mahmoud ElTohamy
- **GitHub:** [mahmoud-el-tohamy](https://github.com/mahmoud-el-tohamy)
- **LinkedIn:** [Mahmoud ElTohamy](https://www.linkedin.com/in/mahmoud-el-tohamy/)
- **Email:** mahmoud.eltohamyy@gmail.com

---

## License

This project is provided as-is for development and educational purposes.

---

## Screenshots

### Home Page

![Home Page](./public/assets/screenshots/Home%20page.png)

### Registration Page

![Registration Page](./public/assets/screenshots/Registration%20page.png)

### Login Page

![Login Page](./public/assets/screenshots/Login%20page.png)

### User Profile Page

![User Profile Page](./public/assets/screenshots/User%20profile%20page.png)

### User Management Dashboard

![User Management Dashboard](./public/assets/screenshots/User%20management%20dashboard.png)

---

## Changelog

### v1.0.0 (Current Release)

- User registration and authentication
- User profile management
- Admin/moderator dashboard
- Advanced user search and filtering
- JWT-based session management
- Social media profile links
- Responsive design

---

**Last Updated:** January 16, 2026
