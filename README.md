# MEAN Stack User Management System

## Project Description

A comprehensive web application built with the MEAN stack (MongoDB, Express.js, Angular.js, Node.js) that provides user account management, authentication, and administrative features. The application allows users to register, log in, manage their profiles, and provides administrators with tools to search, edit, and manage user accounts.

**Key Features:**

- User registration and authentication
- Secure login with JWT token-based authentication
- User profile management
- Admin/moderator dashboard for user management
- Advanced user search and filtering capabilities
- Responsive user interface
- Social media integration links

---

## Prerequisites

Before installing and running this application, ensure your system has the following installed:

- **Node.js** (v14.0.0 or higher) - [Download](https://nodejs.org/)
- **npm** (v6.0.0 or higher) - Comes with Node.js
- **MongoDB** (v4.0 or higher) - [Download](https://www.mongodb.com/try/download/community)
  - MongoDB should be running as a service on your system
  - For Linux: Install via package manager or download from MongoDB
  - For macOS/Windows: Download MongoDB Community Edition installer
- **Git** (optional, for cloning the repository)

---

## Installation & Setup

### Step 1: Clone or Download the Project

```bash
git clone <repository-url>
cd meanstacktutorial
```

### Step 2: Install Dependencies

Navigate to the project root directory and install all Node.js dependencies:

```bash
npm install
```

This will install all required packages including:

- Express.js (backend framework)
- Mongoose (MongoDB ODM)
- Angular.js (frontend framework)
- bcrypt-nodejs (password encryption)
- jsonwebtoken (JWT authentication)
- And other dependencies listed in `package.json`

### Step 3: Verify MongoDB Installation

Ensure MongoDB is installed and running on your system:

**On Linux (Ubuntu/Debian):**

```bash
sudo systemctl status mongod
# If not running, start it with:
sudo systemctl start mongod
```

**On macOS:**

```bash
brew services start mongodb-community
```

**On Windows:**

- MongoDB should start automatically as a service
- Verify it's running in Services (Services.msc)

### Step 4: Configure MongoDB Connection

The application is configured to use MongoDB locally on the default port. The connection string is set in `server.js`:

```javascript
mongoose.connect("mongodb://localhost:27017/meantutorial", function (err) {
  if (err) {
    console.log("Not connected to the database: " + err);
  } else {
    console.log("Successfully connected to MongoDB");
  }
});
```

**To use a different MongoDB connection:**

Edit `server.js` and modify the connection string:

```javascript
mongoose.connect("mongodb://YOUR_CONNECTION_STRING", function (err) {
  // ...
});
```

### Step 5: (Optional) Configure Email Notifications

**Note:** For development, email notifications are mocked and do not actually send emails. For production use:

1. Open `app/routes/api.js`
2. Replace the Nodemailer configuration with your email service credentials:

```javascript
var client = nodemailer.createTransport({
  service: "Gmail", // or your email provider
  auth: {
    user: "your-email@gmail.com",
    pass: "your-app-password",
  },
});
```

---

## Running the Application

### Start MongoDB

Ensure MongoDB is running before starting the application:

```bash
# Linux
sudo systemctl start mongod

# macOS
brew services start mongodb-community
```

### Start the Application

From the project root directory, start the Node.js server:

```bash
npm start
```

You should see the following output:

```
> meantutorial@1.0.0 start
> node server.js

Running the server on port 8080
Successfully connected to MongoDB
```

### Access the Application

Open your web browser and navigate to:

```
http://localhost:8080
```

**Default Port:** 8080 (Can be changed in `server.js` by modifying the `port` variable)

---

## Application Routes

### Authentication & Registration

| Route                | Method | Description                 |
| -------------------- | ------ | --------------------------- |
| `/register`          | GET    | User registration page      |
| `/login`             | GET    | User login page             |
| `/api/users`         | POST   | Register new user           |
| `/api/authenticate`  | POST   | Authenticate user login     |
| `/api/checkusername` | POST   | Check username availability |
| `/api/checkemail`    | POST   | Check email availability    |

### User Profile

| Route          | Method | Description       |
| -------------- | ------ | ----------------- |
| `/profile`     | GET    | View user profile |
| `/api/profile` | GET    | Get profile data  |

### User Management (Admin/Moderator Only)

| Route            | Method | Description                                   |
| ---------------- | ------ | --------------------------------------------- |
| `/management`    | GET    | User management dashboard                     |
| `/api/users`     | GET    | Get all users                                 |
| `/api/users/:id` | GET    | Get specific user                             |
| `/api/users/:id` | PUT    | Edit user (name, email, username, permission) |
| `/api/users/:id` | DELETE | Delete user                                   |

### Home & Navigation

| Route     | Method | Description |
| --------- | ------ | ----------- |
| `/`       | GET    | Home page   |
| `/about`  | GET    | About page  |
| `/logout` | GET    | User logout |

---

## Project Structure

```
meanstacktutorial/
├── server.js                 # Main server file (Express setup, MongoDB connection)
├── package.json              # Node.js dependencies
├── app/
│   ├── models/
│   │   └── user.js          # MongoDB User schema and validation
│   ├── routes/
│   │   └── api.js           # API route handlers
│   └── passport/
│       └── passport.js      # Authentication strategies (social login)
└── public/
    └── app/
        ├── app.js           # Angular app initialization
        ├── routes.js        # Angular route configuration
        ├── controllers/     # Angular controllers
        │   ├── mainCtrl.js
        │   ├── userCtrl.js
        │   ├── managementCtrl.js
        │   └── emailCtrl.js
        ├── services/        # Angular services
        │   ├── userServices.js
        │   └── authServices.js
        ├── views/           # HTML templates
        │   ├── index.html
        │   └── pages/
        │       ├── home.html
        │       ├── about.html
        │       ├── users/
        │       └── management/
        └── assets/
            ├── css/         # Stylesheets
            ├── js/          # Third-party libraries
            ├── fonts/       # Font files
            └── images/      # Image assets
```

---

## User Roles & Permissions

The application supports three user roles:

| Role          | Permissions                              |
| ------------- | ---------------------------------------- |
| **User**      | View own profile, change password        |
| **Moderator** | All user permissions + edit other users  |
| **Admin**     | All moderator permissions + delete users |

---

## Troubleshooting

### MongoDB Connection Error

**Error:** `Not connected to the database: MongoError...`

**Solution:**

1. Ensure MongoDB is running: `sudo systemctl status mongod`
2. Verify connection string in `server.js`
3. Check MongoDB is listening on port 27017 (default)

### Port Already in Use

**Error:** `EADDRINUSE: address already in use :::8080`

**Solution:**

1. Kill the existing process: `pkill -f "node server.js"`
2. Or change the port in `server.js`: `var port = process.env.PORT || 3000;`

### Module Not Found

**Error:** `Cannot find module 'express'` (or other modules)

**Solution:**

```bash
rm -rf node_modules package-lock.json
npm install
```

### Email Notifications Not Working (Development Mode)

**Note:** Email notifications are mocked in development mode and only logged to console. This is intentional to avoid email authentication errors during testing.

To enable real email notifications, configure valid email credentials in `app/routes/api.js`.

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
