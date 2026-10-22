# SkillForge

SkillForge is a full-stack online learning platform that connects students with instructors through searchable courses, structured lectures, enrollment and progress tracking, reviews, shopping-cart functionality, and role-based dashboards.

The application is split into two independent packages:

- `Backend/` - Express 5 API backed by MongoDB and Mongoose
- `Frountend/` - React 19 single-page application built with Vite

## Features

### Students

- Browse published courses
- Search and filter courses
- View course details, instructor information, lectures, and reviews
- Add courses to a cart
- Enroll in courses
- Track learning progress
- Access course lectures
- Review and rate completed courses
- View transaction history

### Instructors

- Access an instructor dashboard
- Create and manage courses
- Update course metadata and publishing state
- Add and manage course lectures
- Upload or associate course media
- Monitor course-related information

### Administrators

- Access an administrative dashboard
- Review and manage users
- Inspect user courses
- Review and manage courses
- Use administrator-only endpoints protected by role authorization

### Platform capabilities

- JWT authentication with an HTTP-only cookie and token response
- Role-based access control for `STUDENT`, `INSTRUCTOR`, and `ADMIN`
- MongoDB persistence through Mongoose models
- Course reviews and rating recalculation
- Local upload serving through `/uploads`
- Cloudinary integration support
- Stripe payment integration support
- CORS configuration for local and deployed frontend clients
- Centralized error handling for validation, duplicate records, and invalid IDs

## Technology stack

### Frontend

- React 19
- React Router 7
- Vite
- Axios
- Tailwind CSS 4
- Stripe.js and React Stripe.js
- ESLint

### Backend

- Node.js
- Express 5
- MongoDB and Mongoose
- JSON Web Tokens
- bcryptjs
- Stripe
- Cloudinary
- Multer
- Cookie Parser
- Morgan
- CORS

## Repository structure

```text
SkillForge/
├── Backend/
│   ├── APIs/
│   │   ├── AdminAPI.js
│   │   ├── CartAPI.js
│   │   ├── InstructorAPI.js
│   │   ├── StudentAPI.js
│   │   ├── commonapi.js
│   │   ├── paymentAPI.js
│   │   └── videoAPI.js
│   ├── middlewares/
│   ├── models/
│   │   ├── Cart.js
│   │   ├── CourseModel.js
│   │   ├── EnrollmentModel.js
│   │   ├── PaymentModel.js
│   │   ├── ReviewModel.js
│   │   ├── UserModel.js
│   │   └── VideoModel.js
│   ├── seeds/
│   ├── services/
│   ├── uploads/
│   ├── utils/
│   ├── package.json
│   └── server.js
├── Frountend/
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   ├── context/
│   │   ├── pages/
│   │   ├── services/
│   │   ├── App.jsx
│   │   └── main.jsx
│   ├── .env.example
│   ├── package.json
│   └── vite.config.js
└── package-lock.json
```

> The directory is named `Frountend` in the repository. The spelling is retained here so commands work with the current project structure.

## Prerequisites

- Node.js 18 or newer
- npm
- A MongoDB database, either local or hosted
- A Stripe account and test keys if payment flows are enabled
- A Cloudinary account if cloud media uploads are enabled

## Installation

Clone the repository and install dependencies independently:

```bash
git clone https://github.com/surya-mar13/SkillForge.git
cd SkillForge

cd Backend
npm install

cd ../Frountend
npm install
```

## Environment variables

### Backend

Create `Backend/.env` with the database and authentication configuration required by the server:

```dotenv
PORT=5000
NODE_ENV=development
DB_URL=mongodb://127.0.0.1:27017/skillforge
JWT_SECRET=replace-with-a-long-random-secret
CLIENT_URL=http://localhost:5174
```

The server also accepts `MONGO_URI` or `MONGODB_URI` as alternatives to `DB_URL`. Multiple frontend origins can be supplied through `CLIENT_URLS` as a comma-separated list.

For payment and media features, configure the corresponding provider credentials used by the implementation:

```dotenv
STRIPE_SECRET_KEY=sk_test_your_secret_key
STRIPE_WEBHOOK_SECRET=whsec_your_webhook_secret
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret
```

Use the exact variable names expected by the relevant backend API module when enabling those integrations. Never commit real credentials.

### Frontend

Copy the example file:

```bash
cd Frountend
copy .env.example .env
```

The example configuration contains:

```dotenv
VITE_API_URL=http://localhost:5000
VITE_STRIPE_PUBLISHABLE_KEY=pk_test_your_publishable_key_here
```

The frontend also supports `VITE_API_BASE_URL`. If both API variables are present, `VITE_API_BASE_URL` takes precedence.

For the deployed backend currently referenced by the example configuration:

```dotenv
VITE_API_URL=https://skillforge-7yrt.onrender.com
```

## Running locally

Start the backend and frontend in separate terminals.

### Start the backend

```bash
cd Backend
npm run dev
```

The backend listens on port `5000` by default. Verify it with:

```text
GET http://localhost:5000/common-api/health
```

### Start the frontend

```bash
cd Frountend
npm run dev
```

Vite prints the local frontend URL when it starts. The backend development CORS configuration allows the local Vite origin used by the project.

## Available scripts

### Backend scripts

Run these commands from `Backend/`:

| Command | Purpose |
| --- | --- |
| `npm run dev` | Start the API with Nodemon |
| `npm start` | Start the API with Node |
| `npm run seed` | Seed users |
| `npm run seed:courses` | Seed courses |
| `npm run seed:missing-courses` | Add missing instructor courses |
| `npm run seed:lectures` | Seed lectures |
| `npm run fix:lectures` | Fix lecture durations |

Additional maintenance scripts are defined in `cleanupScripts` and `localScripts` in `Backend/package.json`. Review them before running because cleanup operations can modify or remove database records.

### Frontend scripts

Run these commands from `Frountend/`:

| Command | Purpose |
| --- | --- |
| `npm run dev` | Start the Vite development server |
| `npm run build` | Create a production build |
| `npm run preview` | Preview the production build locally |
| `npm run lint` | Run ESLint |

## API overview

The backend groups endpoints by user role and responsibility:

### Common API

Base path: `/common-api`

| Method | Endpoint | Description |
| --- | --- | --- |
| `GET` | `/health` | Check API availability |
| `POST` | `/register` | Register a student or instructor |
| `POST` | `/authenticate` | Log in and receive an authentication token |
| `POST` | `/logout` | Clear the authentication cookie |
| `GET` | `/courses` | List published courses with filters |
| `GET` | `/courses/:courseId` | Get course details and reviews |

### Student API

Base path: `/student-api`

Student endpoints support dashboard data, enrollment, progress updates, reviews, course access, and transaction history. Cart endpoints are mounted under `/student-api/cart`.

Typical operations include:

- Student dashboard retrieval
- Course enrollment
- Progress updates
- Course reviews and ratings
- Enrolled course and lecture access
- Cart item creation, retrieval, update, and removal

### Instructor API

Base path: `/instructor-api`

Instructor endpoints support dashboard data, course creation and editing, lecture management, and instructor-owned course operations.

### Admin API

Base path: `/admin-api`

Administrator endpoints support dashboard data, user administration, course administration, and inspection of a user's courses.

### Video API

Base path: `/video-api`

This module supports video-related operations and media handling. Locally served files are exposed through:

```text
/uploads
```

### Payment API

Base path: `/payment-api`

Payment endpoints integrate with Stripe for course checkout and payment records. Configure Stripe keys before using payment flows.

For exact request bodies and response payloads, see the route implementations in `Backend/APIs/` and the request examples in `Backend/req.http`.

## Frontend routes

### Public routes

- `/`
- `/courses/:courseId`
- `/login`
- `/register`
- `/cart`

### Student routes

- `/student/dashboard`
- `/student/courses/:courseId/lectures`
- `/student/transactions`

### Instructor routes

- `/instructor/dashboard`
- `/instructor/courses/:courseId`

### Admin routes

- `/admin/dashboard`
- `/admin/courses`
- `/admin/users/:userId/courses`

Protected routes use the frontend `ProtectedRoute` component and enforce the corresponding role.

## Authentication flow

1. A user registers through `POST /common-api/register`.
2. The user authenticates through `POST /common-api/authenticate`.
3. The backend returns a JWT and sets a `token` cookie.
4. The frontend API client sends requests with credentials enabled.
5. Backend middleware verifies the token and role before protected operations.
6. The user is redirected to the dashboard associated with their role.

Supported roles:

| Role | Main access |
| --- | --- |
| `STUDENT` | Learning, enrollment, progress, reviews, cart, transactions |
| `INSTRUCTOR` | Course and lecture management |
| `ADMIN` | User, course, and platform administration |

## Deployment

### Frontend

The repository includes `Frountend/vercel.json`, which rewrites application routes to `index.html` for client-side routing. A typical deployment flow is:

```bash
cd Frountend
npm install
npm run build
```

Configure the deployment environment with `VITE_API_URL` and `VITE_STRIPE_PUBLISHABLE_KEY`.

### Backend

Deploy the `Backend/` package to a Node-compatible host such as Render or another managed service. Configure:

- A production MongoDB connection string
- A strong JWT secret
- Production CORS origins
- Stripe credentials, if payments are enabled
- Cloudinary credentials, if cloud media is enabled

The backend binds to `process.env.PORT` and falls back to port `5000`.

## Security considerations

- Never commit `.env` files, JWT secrets, Stripe keys, or Cloudinary credentials.
- Use HTTPS in production.
- Set secure cookie options for production deployments.
- Restrict CORS to known frontend origins rather than allowing arbitrary origins.
- Use Stripe webhook signature verification for payment confirmation.
- Do not run destructive cleanup or seed scripts against production data.
- Replace test Stripe keys and sample credentials before release.

## Current implementation notes

- The repository contains both local upload support and Cloudinary dependencies; configure the provider required by the deployment.
- Payment support is implemented around Stripe, but it requires valid server-side and client-side keys.
- Course detail responses intentionally avoid exposing lecture video URLs directly in the public course response; authenticated lecture access is handled through protected flows.
- The root `package-lock.json` is minimal; install dependencies from both `Backend/` and `Frountend/`.

## License

This project does not currently declare a license. Add a license file before distributing or accepting external contributions.

