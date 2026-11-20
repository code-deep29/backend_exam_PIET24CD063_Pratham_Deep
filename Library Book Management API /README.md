# Library Book Management API

A RESTful API for managing a library's book collection. The backend is built with **Node.js**, **Express.js**, **MongoDB**, and **Mongoose**.






<img width="1085" height="433" alt="WhatsApp Image 2026-11-20 at 22 05 43 (1)" src="https://github.com/user-attachments/assets/b3a55bf1-8111-4b60-abb8-3e421da38be4" />






## Features

- Add a new book to the library.
- Retrieve all books.
- Retrieve a single book by its MongoDB ID.
- Update an existing book.
- Delete a book.
- Store book records in MongoDB.
- Validate book fields through a Mongoose schema.
- Automatically maintain `createdAt` and `updatedAt` timestamps.
- Log each incoming request's timestamp, HTTP method, and URL.

## Technology Stack

- Node.js
- Express.js
- MongoDB
- Mongoose
- dotenv

## Project Structure

```text
Library-Book-Management-API/
├── .env.example
├── .gitignore
├── package.json
├── package-lock.json
├── README.md
├── server.js
├── models/
│   └── Book.js
└── routes/
    └── books.js
```

## Prerequisites

- Node.js **20.19.0 or later** (Mongoose 9 requires this; Node.js 24 also works).
- npm
- A running MongoDB database, either local MongoDB or MongoDB Atlas.

## Installation and Setup

1. Open a terminal in the project directory.
2. Install the dependencies:

   ```bash
   npm install
   ```

3. Create a file named `.env` in the project root. You can copy `.env.example` and update the MongoDB connection string:

   ```env
   MONGO_URL=mongodb://127.0.0.1:27017/librarydb
   PORT=3000
   ```

   For MongoDB Atlas, replace the local URL with your Atlas connection string. Keep the real `.env` file private and do not commit it to GitHub.

4. Start the API:

   ```bash
   npm start
   ```

   During development, you can use:

   ```bash
   npm run dev
   ```

The server starts on the port configured in `.env`, or port `3000` if `PORT` is not set. It connects to MongoDB before starting to listen for requests.

Base URL (default):

```text
http://localhost:3000
```

## Book Data Model

Each book document has the following fields:

| Field | Type | Rules |
|---|---|---|
| `title` | String | Required |
| `author` | String | Required |
| `price` | Number | Optional; minimum value is `1` |
| `category` | String | Optional |
| `isAvailable` | Boolean | Defaults to `true` |
| `createdAt` | Date | Automatically generated |
| `updatedAt` | Date | Automatically updated |

## API Endpoints

All request bodies should use JSON where applicable.

| Method | Endpoint | Description |
|---|---|---|
| GET | `/` | Check whether the Library API is running |
| POST | `/books` | Add a new book |
| GET | `/books` | Get all books |
| GET | `/books/:id` | Get one book by MongoDB ID |
| PUT | `/books/:id` | Update a book by MongoDB ID |
| DELETE | `/books/:id` | Delete a book by MongoDB ID |

### 1. Check API Status

```http
GET /
```

Returns:

```text
📚 Library API is running!
```

### 2. Add a Book

```http
POST /books
Content-Type: application/json
```

Example request body:

```json
{
  "title": "The Alchemist",
  "author": "Paulo Coelho",
  "price": 299,
  "category": "Fiction",
  "isAvailable": true
}
```

A successful request returns `201 Created` with the saved book document.

### 3. Get All Books

```http
GET /books
```

Returns `200 OK` with an array of book documents.

### 4. Get a Book by ID

```http
GET /books/:id
```

Replace `:id` with the book's MongoDB ObjectId.

- `200 OK` when the book is found.
- `404 Not Found` when no book exists with that ID.
- `400 Bad Request` for an invalid ID or database/query error.

### 5. Update a Book

```http
PUT /books/:id
Content-Type: application/json
```

Example request body:

```json
{
  "price": 349,
  "isAvailable": false
}
```

A successful request returns `200 OK` with the updated book document. Mongoose schema validators are enabled for updates.

### 6. Delete a Book

```http
DELETE /books/:id
```

A successful deletion returns:

```json
{
  "message": "Book deleted successfully"
}
```

The API returns `404 Not Found` if the book does not exist.

## Testing with Postman

You can test the API using Postman:

1. Start the server with `npm start`.
2. Send `GET http://localhost:3000/` to check the server.
3. Send `POST http://localhost:3000/books` with a JSON book object.
4. Send `GET http://localhost:3000/books` to view all books.
5. Copy a book's `_id` from the response and use it with `GET`, `PUT`, or `DELETE /books/:id`.

## Current Scope and Limitations

- This project provides book CRUD operations only.
- It does not currently implement user registration, login, authentication, or role-based access.
- It does not currently implement borrowing, returning, reservations, search, filtering, pagination, or book availability workflows beyond the `isAvailable` field.
- The API relies on Mongoose schema validation; there is no separate request-validation library.
- The current routes return `400` for caught database and request errors, so invalid input and some server/database errors are not distinguished in every case.
- Automated tests are not configured in the current project.

## Security Notes

- Do not upload `.env` to GitHub. The `.gitignore` file excludes it.
- Keep your MongoDB connection string private.
- Restrict database access and credentials appropriately before deploying the API publicly.

## License

This project specifies the **ISC** license in `package.json`.

