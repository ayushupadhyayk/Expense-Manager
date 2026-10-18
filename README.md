# 💼 Expense Management System

![MongoDB](https://img.shields.io/badge/MongoDB-Mongoose-47A248?logo=mongodb&logoColor=white)
![Express](https://img.shields.io/badge/Express-API-000000?logo=express&logoColor=white)
![React](https://img.shields.io/badge/React-18-61DAFB?logo=react&logoColor=black)
![Node.js](https://img.shields.io/badge/Node.js-Backend-339933?logo=nodedotjs&logoColor=white)
![Ant Design](https://img.shields.io/badge/Ant%20Design-4-0170FE?logo=antdesign&logoColor=white)

The first version of my final semester expense project: a **MERN stack** (MongoDB, Express,
React, Node.js) web app with user registration, login and a protected home page, built as
the foundation for managing personal expenses.

> **Where this led:** I built this first, in May 2023, and used it to learn the full-stack
> setup. The next day I started
> **[Expense-Tracker](https://github.com/ayushupadhyayk/Expense-Tracker)**, which reused the
> same architecture and focused on tracking income and expenses. See
> [How this led to Expense Tracker](#how-this-led-to-expense-tracker).

## Contents

- [Project status](#project-status)
- [Features](#features)
- [How it works](#how-it-works)
- [API](#api)
- [Getting started](#getting-started)
- [Project structure](#project-structure)
- [How this led to Expense Tracker](#how-this-led-to-expense-tracker)
- [Known issues](#known-issues)
- [Tech stack](#tech-stack)

## Project status

**Early stage.** User accounts work end to end: register, log in, stay logged in and log out.
The home page behind the login is still a placeholder, and the expense features were built
in the follow-up project, Expense-Tracker.

## Features

- 📝 **Register** with name, email and password (each email can only be used once)
- 🔐 **Log in** and stay logged in across page reloads
- 🛡️ **Protected home page**: visitors who aren't logged in are sent to the login page
- 👤 **Header** showing the logged-in user's name, with a logout button
- ⏳ Loading spinner and success or error messages on every form

## How it works

```
React client (port 3000)                          Express API (port 8080)               MongoDB
────────────────────────                          ───────────────────────               ───────
Register form ── POST /api/v1/users/register ──►  registerController ── save user ──►  users
Login form    ── POST /api/v1/users/login    ──►  loginController    ── find user ──►  users
     ◄──────────────── user details ─────────────────────┘
Saves the user in localStorage → ProtectedRoutes lets them into the home page
```

1. **Register.** The form sends name, email and password to the API, which saves a new user
   with Mongoose. The email field is unique, so duplicates are rejected.
2. **Log in.** The API looks up a user with that email and password and returns the user's details.
3. **Stay logged in.** The client stores the user (with the password removed) in the browser's
   `localStorage`.
4. **Protected routes.** A `ProtectedRoutes` wrapper checks `localStorage`: logged-in users
   see the home page, everyone else is redirected to `/login`.
5. **Log out.** The header button clears `localStorage` and returns to the login page.

In development, the React app forwards API calls to the backend through the
`"proxy": "http://localhost:8080/api/v1"` setting in `client/package.json`.

## API

All routes are under `/api/v1/users`.

| Method | Path | Body | Response |
|---|---|---|---|
| POST | `/register` | `{ name, email, password }` | `201` with the new user |
| POST | `/login` | `{ email, password }` | `200` with the user, or `404` "User Not Found" |

## Getting started

Requirements: Node.js and a MongoDB database (local, or a free MongoDB Atlas cluster).

**1. Backend** (from the repository root)

The root `package.json` doesn't list the backend's packages, so install them directly:

```bash
npm install express mongoose cors morgan dotenv colors
```

Create a `.env` file in the root:

```
MONGO_URL=mongodb+srv://<user>:<password>@<cluster>/expense-manager
```

Start the API (it listens on port 8080):

```bash
node server.js
```

**2. Frontend** (in a second terminal)

```bash
cd client
npm install
npm start
```

The app opens at http://localhost:3000.

## Project structure

```
Expense-Manager/
├── server.js                    # Express app: middleware, routes, port 8080
├── config/connectDb.js          # MongoDB connection using MONGO_URL
├── models/userModel.js          # User schema: name, email (unique), password
├── controllers/userController.js# Register and login logic
├── routes/userRoute.js          # POST /register and /login
├── client/                      # React app (Create React App, Ant Design, Bootstrap)
│   └── src/
│       ├── App.js               # Routes and ProtectedRoutes
│       ├── pages/               # HomePage, Login, Register
│       └── components/          # Layout (Header, Footer) and Spinner
└── Expense-Management-System/   # A duplicate copy of the same project
```

## How this led to Expense Tracker

I built Expense-Manager on **10 May 2023** and uploaded
[Expense-Tracker](https://github.com/ayushupadhyayk/Expense-Tracker) on **11 May 2023**.
Expense-Manager is where I worked out how a MERN app fits together. Expense-Tracker reused
that structure and put the effort into the actual money tracking.

### What carried over

| Learned in Expense-Manager | How Expense-Tracker uses it |
|---|---|
| Backend split into **models, controllers and routes**, with a separate database config | Same layout: `models/`, `controllers/`, `routes/` and `db/db.js` |
| **Mongoose** connecting to MongoDB through a `MONGO_URL` in a `.env` file | Same connection approach |
| **Versioned API routes** under `/api/v1` | Same prefix, e.g. `/api/v1/add-expense` |
| Express with `express.json()` and `cors()` middleware | Same middleware setup |
| React client calling the API with **axios** | Same pattern, managed centrally in a React context |
| Mongoose schemas with `required` fields and `timestamps` | Used for the income and expense models |

### What changed

| | Expense-Manager | Expense-Tracker |
|---|---|---|
| **Focus** | User accounts (register, log in) | Tracking income and expenses |
| **Data** | Users | Incomes and expenses, each with title, amount, category, date and description |
| **Main screen** | Placeholder home page | Dashboard with totals, balance, a chart and recent history |
| **Organisation** | Backend at the root, React in `client/`, plus a duplicate copy of everything | Separate `backend/` and `frontend/` folders, each with its own complete `package.json` |
| **Styling** | Ant Design forms and Bootstrap | A custom design built with styled-components |
| **Users** | Multiple accounts with login | A single personal tracker without login |

### What came next

Expense-Tracker has since grown into a full app with editing and filtering, weekly, monthly and
custom reports with Excel export, spending analysis and savings goals, plus a one-command
setup with a built-in database. Bringing back user accounts from this project is a natural
next step.

## Known issues

- **Passwords are stored as plain text** and compared directly at login. A real app should
  hash them (for example with `bcrypt`) and use tokens such as JWT instead of trusting `localStorage`.
- **The port setting is the wrong way round.** `8080 || process.env.PORT` always uses 8080;
  it should be `process.env.PORT || 8080`.
- **The root `package.json` is incomplete.** It lists no dependencies, and its start script
  runs `index.js`, which doesn't exist (the server is `server.js`).
- **The whole project is duplicated** inside `Expense-Management-System/`.
- **Generic error messages.** Failed logins and registrations show "something went wrong"
  without saying why.
- **The footer still shows a template credit** ("techinfoyt") rather than the project's own.

## Tech stack

| Area | Tools |
|---|---|
| Database | MongoDB, Mongoose |
| Backend | Node.js, Express, morgan (request logging), dotenv, cors |
| Frontend | React 18, React Router 6, Ant Design 4, Bootstrap 5, axios |
| Tooling | Create React App |
