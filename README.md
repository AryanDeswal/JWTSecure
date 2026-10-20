# JWTSecure

A Node.js and Express app that shows email and password authentication with JSON Web Tokens stored in HTTP-only cookies.

![Node.js](https://img.shields.io/badge/Node.js-339933?logo=nodedotjs&logoColor=white)
![Express](https://img.shields.io/badge/Express-000000?logo=express&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-47A248?logo=mongodb&logoColor=white)
![JWT](https://img.shields.io/badge/JWT-000000?logo=jsonwebtokens&logoColor=white)
![EJS](https://img.shields.io/badge/EJS-B4CA65?logo=ejs&logoColor=black)

## About

JWTSecure is a small server-rendered website (a "Smoothies" recipe demo) that puts a page behind JWT authentication. Users sign up or log in with an email and password, the server signs a JWT and sets it as an HTTP-only cookie, and middleware checks that cookie before showing the protected recipes page. Passwords are hashed with bcrypt and users are stored in MongoDB.

## Features

- Sign up and log in with email and password, with forms that call the API with `fetch` and show errors inline
- Passwords hashed with bcrypt (10 salt rounds) in a Mongoose `pre('save')` hook
- Validation: required fields, valid email (`validator`), minimum 6-character password, unique email
- JWT signed with the user ID, valid for 3 days, sent in an HTTP-only `jwt` cookie
- `requireAuth` middleware protects `/smoothies` and redirects to `/login` when the token is missing or invalid
- `checkUser` middleware loads the logged-in user on every GET request so the navbar shows "Welcome, email" and a log-out link
- Log out by clearing the cookie

## Tech Stack

- **Runtime:** Node.js
- **Framework:** Express 4
- **Views:** EJS
- **Database:** MongoDB with Mongoose 8
- **Auth:** jsonwebtoken, bcrypt, cookie-parser
- **Validation:** validator

## Project Structure

```
JWTSecure/
├── app.js                      # Express app, MongoDB connection, page routes, server on port 3000
├── controllers/
│   └── authController.js       # Sign-up, login, logout, JWT creation, error handling
├── middleware/
│   └── authMiddleware.js       # requireAuth and checkUser
├── models/
│   └── User.js                 # User schema, bcrypt hook, User.login()
├── routes/
│   └── authRoutes.js           # Auth routes
├── views/
│   ├── home.ejs
│   ├── login.ejs
│   ├── signup.ejs
│   ├── smoothies.ejs           # Protected page
│   └── partials/               # header.ejs (navbar), footer.ejs
├── public/                     # styles.css, smoothie.png
└── package.json
```

## Getting Started

### Prerequisites

- Node.js and npm
- MongoDB running locally on `127.0.0.1:27017`

### Installation

```bash
git clone https://github.com/AryanDeswal/JWTSecure.git
cd JWTSecure
npm install
```

### Configuration

The project does not read environment variables. Its settings are hard-coded:

| Setting          | Value                                         | Location                                                     |
| ---------------- | --------------------------------------------- | ------------------------------------------------------------ |
| MongoDB URI      | `mongodb://127.0.0.1:27017/jwtAuthProject`    | `app.js`                                                     |
| JWT secret       | `'secret'`                                    | `controllers/authController.js`, `middleware/authMiddleware.js` |
| Token lifetime   | 3 days                                        | `controllers/authController.js`                              |
| Port             | `3000`                                        | `app.js`                                                     |

Change the JWT secret before using this code anywhere outside local development.

### Run

```bash
npm start
```

The server starts after MongoDB connects. Open `http://localhost:3000`.

## Routes

| Method | Route        | Description                                                              |
| ------ | ------------ | ------------------------------------------------------------------------ |
| GET    | `/`          | Home page                                                                |
| GET    | `/smoothies` | Recipes page (requires a valid `jwt` cookie, otherwise redirects to `/login`) |
| GET    | `/signup`    | Sign-up page                                                             |
| POST   | `/signup`    | Create a user from `{ email, password }`, set the `jwt` cookie, return `201 { user }` |
| GET    | `/login`     | Login page                                                               |
| POST   | `/login`     | Check `{ email, password }`, set the `jwt` cookie, return `200 { user }` |
| GET    | `/logout`    | Clear the `jwt` cookie and redirect to `/`                               |

On failure, `POST /signup` and `POST /login` return `400 { "errors": { "email": "...", "password": "..." } }`.
