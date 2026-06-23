# Connexa

Connexa is a production-ready MERN real-time chat application with JWT authentication, Socket.IO messaging, direct conversations, group chats, file attachments, online presence, typing indicators, dark mode, and responsive UI.

## Features

- Register, login, logout, protected routes, password hashing, JWT cookies and bearer token support
- One-to-one chat and group chat conversations
- Real-time messaging with Socket.IO rooms and acknowledgements
- Online/offline presence, typing indicators, seen status, unread counts
- Message edit, delete, timestamps, search, optimistic sending
- Image/file/audio/video attachments through local upload storage
- Responsive React + Vite UI with Tailwind CSS and dark mode
- Helmet, CORS, rate limiting, Mongo sanitization, validation, global error handling
- PWA and video/voice call preparation structure

## Screenshots

Add screenshots after deployment:

- `docs/screenshots/login.png`
- `docs/screenshots/chat-desktop.png`
- `docs/screenshots/chat-mobile.png`

## Tech Stack

Frontend: React, Vite, Tailwind CSS, React Router DOM, Axios, Context API, Socket.IO Client, React Hot Toast, Lucide React.

Backend: Node.js, Express, MongoDB, Mongoose, Socket.IO, JWT, bcryptjs, cookie-parser, dotenv, cors, helmet, express-rate-limit, multer.

## Folder Structure

```text
backend/
  app.js           Express app, middleware, REST routes
  config/          MongoDB connection
  controllers/     REST request handlers
  middleware/      auth, errors, uploads
  models/          User, Message, Conversation, GroupChat
  routes/          /api/auth, /api/users, /api/messages, /api/conversations
  sockets/         Socket.IO auth, rooms, messaging, presence
  uploads/         local attachment storage
  utils/           async wrapper, app error, JWT helpers
  server.js        Express and Socket.IO bootstrap

frontend/
  public/          PWA manifest and service worker shell
  src/components/  reusable UI and chat components
  src/context/     auth, theme, socket state
  src/hooks/       reusable React hooks
  src/layouts/     auth and app layouts
  src/pages/       Login, Signup, Home, Chat, Profile, NotFound
  src/services/    Axios API client and socket client
  src/utils/       constants and formatters
```

## Environment Setup

Create `backend/.env`:

```env
NODE_ENV=development
PORT=5000
MONGO_URI=mongodb://127.0.0.1:27017/connexa
JWT_SECRET=replace-with-a-long-random-secret
JWT_EXPIRES_IN=7d
CLIENT_URL=http://localhost:5173
```

Create `frontend/.env`:

```env
VITE_API_URL=http://localhost:5000
```

## Installation

Install backend dependencies:

```bash
cd backend
npm install
```

Install frontend dependencies:

```bash
cd frontend
npm install
```

## Run Locally

Start MongoDB locally or point `MONGO_URI` to MongoDB Atlas.

Backend:

```bash
cd backend
npm run dev
```

Frontend:

```bash
cd frontend
npm run dev
```

Open `http://localhost:5173`.

## API Routes

- `POST /api/auth/register`
- `POST /api/auth/login`
- `POST /api/auth/logout`
- `GET /api/auth/me`
- `GET /api/users`
- `PATCH /api/users/me`
- `GET /api/conversations`
- `POST /api/conversations/direct`
- `POST /api/conversations/group`
- `PATCH /api/conversations/:id/read`
- `GET /api/messages/:conversationId`
- `POST /api/messages/:conversationId`
- `PATCH /api/messages/:id`
- `DELETE /api/messages/:id`

## Deployment

The repository is prepared for separate deployments:

- Frontend: Vercel, root directory `frontend`
- Backend: Render Web Service, root directory `backend`
- Database: MongoDB Atlas

### Backend on Render

Option A: use the included `render.yaml` blueprint from the repository root.

Option B: create the service manually:

1. Create a new Render Web Service from this repository.
2. Set root directory to `backend`.
3. Runtime: Node.
4. Build command: `npm install`.
5. Start command: `npm start`.
6. Health check path: `/health`.
7. Add environment variables:

```env
NODE_ENV=production
PORT=5000
MONGO_URI=mongodb+srv://<user>:<password>@<cluster>/<database>
JWT_SECRET=<long-random-production-secret>
JWT_EXPIRES_IN=7d
CLIENT_URL=https://your-vercel-app.vercel.app
```

`CLIENT_URL` supports comma-separated origins if needed:

```env
CLIENT_URL=https://your-vercel-app.vercel.app,https://your-custom-domain.com
```

Render will provide the public backend URL, for example:

```text
https://connexa-backend.onrender.com
```

### Frontend on Vercel

1. Import the repository into Vercel.
2. Set root directory to `frontend`.
3. Framework preset: Vite.
4. Build command: `npm run build`.
5. Output directory: `dist`.
6. Add environment variable:

```env
VITE_API_URL=https://connexa-backend.onrender.com
```

The included `frontend/vercel.json` configures SPA rewrites to `index.html` and long-term caching for generated assets.

### Deployment Order

1. Create MongoDB Atlas database and copy the connection string.
2. Deploy the backend on Render with `MONGO_URI`, `JWT_SECRET`, and temporary `CLIENT_URL` if the frontend URL is not ready yet.
3. Deploy the frontend on Vercel with `VITE_API_URL` set to the Render backend URL.
4. Update Render `CLIENT_URL` to the final Vercel URL or custom domain.
5. Redeploy/restart the Render backend after changing `CLIENT_URL`.
6. Open the Vercel URL and verify `/health` on the backend returns:

```json
{ "status": "ok", "service": "connexa-api" }
```

### Build Scripts

Backend:

```bash
cd backend
npm run build
npm start
```

Frontend:

```bash
cd frontend
npm run build
npm run preview
```

## Production Notes

- Use MongoDB Atlas in production.
- Replace `JWT_SECRET` with a long random value.
- Keep `NODE_ENV=production` on Render so secure cookies and production middleware behavior are enabled.
- If using cookies across Render and Vercel, keep HTTPS enabled and set `CLIENT_URL` exactly to the browser origin.
- Configure persistent upload storage such as Cloudinary or S3 before heavy production file usage.
- For push notifications, wire VAPID keys and a service worker push handler.
- The Socket.IO `call:offer` event is prepared for WebRTC voice/video features.
