# Student Portal API

A RESTful Student Portal backend built with **Node.js** and
**Express.js**. It provides user registration, login, JWT-based
authentication, user profile access, and an admin-only endpoint to view
registered users.






<img width="1085" height="433" alt="WhatsApp Image 2026-11-20 at 21 15 41" src="https://github.com/user-attachments/assets/38f191ef-be9e-4125-ac25-1b2b85ac8c3e" />






<img width="1600" height="423" alt="image" src="https://github.com/user-attachments/assets/04109dee-2cc0-48b5-b57c-c7eae7089111" />







## Features

-   Register student or admin accounts.
-   Validate registration and login data using Joi.
-   Hash passwords securely using bcryptjs.
-   Authenticate users with JSON Web Tokens (JWT).
-   Protect private routes using authentication middleware.
-   Restrict the students list endpoint to users with the `admin` role.
-   Apply basic API security middleware using Helmet, CORS, and rate
    limiting.
-   Return consistent JSON responses and handle unknown routes and
    server errors.

## Technology Stack

-   Node.js
-   Express.js
-   JSON Web Token (`jsonwebtoken`)
-   bcryptjs
-   Joi
-   Helmet
-   CORS
-   express-rate-limit

## Project Structure

``` text
student-portal/
├── app.js
├── server.js
├── config.js
├── db.js
├── package.json
├── middleware/
│   └── authMiddleware.js
└── routes/
    ├── auth.js
    └── protected.js
```

-   `server.js` starts the HTTP server.
-   `app.js` configures Express, middleware, routes, health check, and
    error handling.
-   `config.js` contains the port and authentication configuration.
-   `db.js` contains the current in-memory user store.
-   `middleware/authMiddleware.js` verifies JWTs and checks user roles.
-   `routes/auth.js` contains registration and login endpoints.
-   `routes/protected.js` contains the profile and admin-only students
    endpoints.

## Prerequisites

-   Node.js 18 or later
-   npm

## Installation and Setup

1.  Download or clone this project.

2.  Open a terminal in the project folder.

3.  Install dependencies:

    ``` bash
    npm install
    ```

4.  Set a strong JWT secret before running the application.

    **Windows PowerShell:**

    ``` powershell
    $env:JWT_SECRET="replace_with_a_long_random_secret"
    ```

    **macOS/Linux:**

    ``` bash
    export JWT_SECRET="replace_with_a_long_random_secret"
    ```

5.  Start the server:

    ``` bash
    npm start
    ```

    For development with Node's watch mode:

    ``` bash
    npm run dev
    ```

The API runs at:

``` text
http://localhost:3000
```

You can set a different port with the `PORT` environment variable.

## API Endpoints

All request and response bodies use JSON.

  -----------------------------------------------------------------------
  Method            Endpoint          Access            Description
  ----------------- ----------------- ----------------- -----------------
  GET               `/`               Public            API health check
                                                        and endpoint
                                                        overview

  POST              `/register`       Public            Register a user

  POST              `/login`          Public            Log in and
                                                        receive a JWT

  GET               `/profile`        Authenticated     Get the logged-in
                                                        user's profile

  GET               `/students`       Admin             Get all
                                                        registered users,
                                                        excluding
                                                        passwords
  -----------------------------------------------------------------------

### 1. Health Check

**Request**

``` http
GET /
```

Returns a status message and the available endpoint names.

### 2. Register a User

**Request**

``` http
POST /register
Content-Type: application/json
```

**Example body**

``` json
{
  "name": "Aman Kumar",
  "email": "aman@example.com",
  "password": "password123",
  "role": "student"
}
```

The `role` field accepts `student` or `admin` and defaults to `student`
when omitted.

**Successful response:** `201 Created`

``` json
{
  "success": true,
  "message": "Registration successful.",
  "data": {
    "id": 1,
    "name": "Aman Kumar",
    "email": "aman@example.com",
    "role": "student"
  }
}
```

Possible errors include `400 Bad Request` for invalid input and
`409 Conflict` when the email is already registered.

### 3. Login

**Request**

``` http
POST /login
Content-Type: application/json
```

**Example body**

``` json
{
  "email": "aman@example.com",
  "password": "password123"
}
```

**Successful response:** `200 OK`

``` json
{
  "success": true,
  "message": "Login successful.",
  "token": "<JWT_TOKEN>",
  "expiresIn": "1h"
}
```

Use the returned token when calling protected endpoints.

### 4. Get User Profile

**Request**

``` http
GET /profile
Authorization: Bearer <JWT_TOKEN>
```

Returns the authenticated user's ID, name, email, and role. The password
is not included.

### 5. Get All Students/Users (Admin Only)

**Request**

``` http
GET /students
Authorization: Bearer <ADMIN_JWT_TOKEN>
```

Returns the registered users without password fields. A valid token with
the `admin` role is required.

## Authentication and Validation

-   Passwords are hashed with bcryptjs before being stored.
-   Login checks the submitted password against the stored hash.
-   Successful login creates a JWT containing the user's ID, email, and
    role.
-   JWTs expire after one hour.
-   Protected routes require an `Authorization: Bearer <token>` header.
-   Joi validates registration and login input.
-   The role middleware restricts `/students` to administrators.

## Security Middleware

The Express application uses:

-   **Helmet** to set security-related HTTP headers.
-   **CORS** to allow cross-origin requests.
-   **express-rate-limit** to limit requests to 100 per IP per 15-minute
    window.
-   A global error handler and a JSON 404 response for unknown routes.

## Important Notes and Limitations

-   **In-memory storage:** Users are stored in a JavaScript array in
    `db.js`. All registered users are lost when the server restarts.
    This project does not currently use MongoDB, MySQL, PostgreSQL, or
    another persistent database.
-   **Admin registration:** The current registration endpoint accepts
    `role: "admin"` from the request body. This is suitable only for a
    controlled demonstration. In a real deployment, users must not be
    allowed to grant themselves admin privileges; admin accounts should
    be created through a trusted administrative process.
-   **Development configuration:** Set `JWT_SECRET` to a strong secret
    using an environment variable. Do not use the fallback secret from
    `config.js` in production.
-   **CORS:** The current configuration allows cross-origin requests
    generally. Restrict allowed origins before production deployment.
-   **Testing:** The `package.json` currently contains a placeholder
    test script; automated tests have not been configured.

## Example Testing Workflow

You can test the API using Postman or another API client:

1.  Send `POST /register` with a student's details.
2.  Send `POST /login` with the same email and password.
3.  Copy the JWT from the login response.
4.  Send `GET /profile` with `Authorization: Bearer <JWT_TOKEN>`.
5.  For an admin-only test, register an admin account in this
    demonstration setup, log in with it, and use its token with
    `GET /students`.

## License

This project currently specifies the **ISC** license in `package.json`.
