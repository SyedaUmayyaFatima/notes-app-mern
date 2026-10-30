# Notely: MERN Note-Taking App

A full-stack note-taking application built with the MERN stack (MongoDB, Express, React, Node.js). Users can register, sign in, and manage personal notes with pinning, trash/restore, search, filtering, sorting, import/export, and real-time updates, all behind authenticated routes.

<!--
SCREENSHOTS: add 2-3 images of the running app to docs/screenshots/ and
uncomment the lines below. Delete this block if you skip screenshots.

![Dashboard](docs/screenshots/dashboard.png)
![Rich-text editor](docs/screenshots/editor.png)
![Settings page](docs/screenshots/settings.png)
-->

## Features

### Authentication
- Register / login / logout with JWT-based auth stored in an HTTP-only cookie
- Protected routes: signed-out users are redirected to login
- Session persistence across page reloads

### Notes
- Create, edit, and delete notes with a rich-text editor
- Pin/unpin important notes
- Soft-delete to Trash, with restore or permanent delete
- Search notes by title/content
- Filter and sort (newest, oldest, title A-Z, title Z-A)
- Import notes from file, export notes (Text / Excel)
- Real-time updates via Socket.IO

### Profile & Settings
- Profile modal (avatar, bio, contact info, quick note stats) accessible from the header
- Dedicated Settings page with tabs for Profile, Notes overview, and Account
- Avatar upload, bio, phone, and location fields
- Logout with confirmation dialog and error handling

### UI/UX
- Responsive layout with a collapsible mobile sidebar drawer
- Dark-themed dashboard with gradient accents
- Inline confirmation dialogs for destructive actions

### Engineering Practices
- Centralized logging with Pino
- Global exception-handling middleware
- Git branching and merging workflow with GitHub pull requests
- AI-assisted code reviews with CodeRabbit
- Static analysis with SonarQube (Quality Gate passed)

## Tech Stack

- **Frontend:** React, React Router, Tailwind CSS, lucide-react, Vite
- **Backend:** Node.js, Express, MongoDB (Mongoose), JWT, bcryptjs, Socket.IO, Pino
- **Testing:** Jest (frontend), Mocha + Chai + Supertest + nyc (backend)
- **Code Quality:** SonarQube Community Edition, CodeRabbit

## Project Structure

```
notes-app-mern/
├── backend/                 # Express API, MongoDB models, auth, notes controllers
│   ├── config/              # Database connection and logger setup
│   ├── controllers/
│   ├── middleware/
│   ├── models/
│   ├── routes/
│   └── tests/
├── frontend/
│   └── Note-Taking-App/     # React app (Vite)
│       └── src/
│           ├── components/
│           ├── context/
│           ├── hooks/
│           ├── pages/
│           └── tests/
├── docs/
│   └── sonarqube-report/    # Static analysis screenshots
└── sonar-project.properties
```

## Getting Started

### Prerequisites
- Node.js (v18 recommended)
- MongoDB (local instance or a MongoDB Atlas connection string)

### Environment Variables

Copy each `.env.example` to `.env` and fill in the values. Use the keys listed in the example files; typical ones are:

| Location | Variable | Purpose |
| --- | --- | --- |
| `backend/.env` | `MONGO_URI` | MongoDB connection string |
| `backend/.env` | `JWT_SECRET` | Secret used to sign auth tokens |
| `backend/.env` | `PORT` | Port the API listens on |
| `frontend/Note-Taking-App/.env` | API URL variable | Points the frontend at your backend |

Never commit your real `.env` files. They are listed in `.gitignore`.

### Backend setup

```bash
cd backend
npm install
cp .env.example .env     # Windows PowerShell: copy .env.example .env
npm start
```

### Frontend setup

```bash
cd frontend/Note-Taking-App
npm install
cp .env.example .env     # Windows PowerShell: copy .env.example .env
npm run dev
```

The frontend runs on http://localhost:5173 by default; the backend port is set in your `.env`.

## Running Tests

Frontend:

```bash
cd frontend/Note-Taking-App
npm test -- --coverage
```

Backend:

```bash
cd backend
npx nyc --reporter=lcov --reporter=text mocha tests/**/*.test.js --timeout 10000 --exit
```

## SonarQube Analysis

This project has been analyzed with SonarQube Community Edition, run locally via Docker. Full screenshots are in [`docs/sonarqube-report`](docs/sonarqube-report).

### How the scan was run

1. SonarQube server started locally via Docker:
   ```bash
   docker run -d --name sonarqube -p 9000:9000 sonarqube:lts-community
   ```
2. Test coverage generated separately for each side of the app:
   - Frontend (Jest, built-in lcov reporter): `npm test -- --coverage`
   - Backend (Mocha + nyc, since the backend uses Mocha rather than Jest):
     ```bash
     npx nyc --reporter=lcov --reporter=text mocha tests/**/*.test.js --timeout 10000 --exit
     ```
3. Scan executed via the Dockerized SonarScanner CLI, using `sonar-project.properties` at the repo root:
   ```bash
   docker run --rm \
     -e SONAR_HOST_URL="http://host.docker.internal:9000" \
     -e SONAR_TOKEN="<project token>" \
     -v "${PWD}:/usr/src" \
     sonarsource/sonar-scanner-cli
   ```

### Results summary

| Metric | Result |
| --- | --- |
| Quality Gate | Passed |
| Bugs | 0 (Rating: A) |
| Vulnerabilities | 0 (Rating: A) |
| Code Smells | 24 (Maintainability Rating: A) |
| Coverage | 13.3% |
| Duplications | 1.4% |
| Technical Debt | ~1h 9min |

### Screenshots

| File | Description |
| --- | --- |
| `01-overview-quality-gate(a/b).png` | Project overview: Quality Gate status, bugs, vulnerabilities, code smells |
| `02-measures(a/b).png` | Detailed coverage and duplication metrics |
| `03-issues-list-1/2/3.png` | Full list of flagged code smells (24 total, no bugs or vulnerabilities) |

### Notes on the findings

All 24 flagged issues are Code Smells, which are maintainability suggestions rather than defects. Common patterns flagged:

- Redundant `await` on values that aren't Promises
- Using array index as a React `key` prop
- Nested ternary expressions that could be simplified
- A couple of functions with cognitive complexity slightly above the recommended threshold

None of these are blocking; the Maintainability Rating remains A despite them. Coverage (13.3%) reflects that automated tests currently focus on authentication flows (`AuthContext`, `ProtectedRoute`, backend `auth.js`) and core components rather than the full app surface. Expanding coverage across the notes controllers and services is the next planned improvement.

## Roadmap

- Increase test coverage for notes controllers, services, and data-access layers
- Deploy the app (backend on Render/Railway, frontend on Vercel/Netlify, database on MongoDB Atlas)
- Add a live demo link

## License

Built as a learning project while studying the MERN stack.