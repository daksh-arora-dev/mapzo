
## Overview

**Mapzo** brings local experiences onto one interactive map. Instead of discovering events through scattered posts, groups, and links, users can explore what's nearby, narrow down the options, and connect with people attending the same event.

Mapzo supports both sides of local discovery: **attendees** looking for experiences and **hosts** creating them.

> **From “What’s happening near me?” to “I’m going.”**

##  Features

###  Discover nearby events
- **Interactive map:** Explore events using location-based markers and event details.
- **Location-aware discovery:** Use device GPS or search for a location manually.
- **Useful filters:** Browse by date, distance, and category, including music, technology, sports, culture, and food.
- **Event search:** Find events by keywords and explore relevant results.

###  Join and engage
- **Event pages:** View descriptions, venues, dates, and other event information.
- **Join events:** Register interest in an event and view joined events in your account.
- **Ratings and reviews:** Share feedback and explore community reviews.
- **Event conversations:** Chat with other attendees through periodically refreshed event messages.
- **Sharing and reminders:** Share event links and use the reminder functionality in the event experience.

###  Host experiences
- **Event publishing:** Create event listings with a title, category, schedule, description, and image.
- **Map-based location selection:** Pin an event venue directly on the map.
- **Host access:** Host-specific publishing tools and a host-verification request interface.
- **Profile management:** Manage account information, profile images, and hosted events.

###  Designed for everyday use
- Responsive, mobile-friendly interface.
- Firebase-powered sign-in, including Google authentication.
- Progressive Web App assets, including a web manifest and service worker.

##  Tech Stack

| Layer | Technologies |
| --- | --- |
| **Frontend** | HTML5, CSS3, Vanilla JavaScript |
| **Maps & Location** | Google Maps JavaScript API, browser Geolocation API, OpenStreetMap Nominatim |
| **Authentication** | Firebase Authentication, Firebase Admin SDK, JWT |
| **Backend** | Node.js, Express.js |
| **Database** | PostgreSQL (`pg`) |
| **Media Storage** | Supabase Storage |
| **Deployment** | Vercel (frontend), Render (backend configuration) |

##  How It Works

```text
                 ┌───────────────────────────┐
                 │      Mapzo Frontend       │
                 │    HTML · CSS · JavaScript│
                 └─────────────┬─────────────┘
                               │
                  ┌────────────┼─────────────┐
                  │            │             │
                  ▼            ▼             ▼
            Google Maps   Firebase Auth   Express API
                                             │
                                     ┌───────┴────────┐
                                     │                │
                                     ▼                ▼
                                 PostgreSQL     Supabase Storage
                               Events · Users    Event images
                              Reviews · Tickets  Profile images
``` 

- The frontend renders the map and event discovery interface.
- Firebase handles user authentication; the backend validates access to protected endpoints.
- Express exposes endpoints for event discovery, profiles, reviews, participation, and conversations.
- PostgreSQL stores structured application data; Supabase Storage handles uploaded media.

##  Project Structure

```text
mapzo/
├── frontend/
│   ├── index.html             # Event discovery interface
│   ├── event.js               # Event details and interactions
│   ├── profile.html           # User profile page
│   ├── script.js              # Map and application interactions
│   ├── utils.js               # Shared configuration and utilities
│   ├── style.css              # Main styles
│   └── ...                    # Assets and PWA files
├── backend/
│   ├── server.js              # Express app and API endpoints
│   ├── db.js                  # PostgreSQL connection pool
│   ├── db.sql                 # Database schema (base setup)
│   ├── auth/                  # Authentication middleware
│   ├── route/                 # Authentication routes
│   └── package.json
└── render.yaml                # Render deployment configuration
```

##  Run Locally

### Prerequisites

- Node.js and npm
- Python 3 **or** any local static-file server for the frontend
- A PostgreSQL database
- A Firebase project with Authentication configured
- A Google Maps JavaScript API key
- A Supabase project if you want image uploads

### 1. Clone the repository

```bash
git clone https://github.com/daksh-arora-dev/mapzo.git
cd mapzo
```

### 2. Configure and run the backend

```bash
cd backend
npm install
```

Create a local `backend/.env` file with your own credentials:

```dotenv
NODE_ENV=development
PORT=3000
DATABASE_URL=postgresql://USER:PASSWORD@HOST:5432/DATABASE
JWT_SECRET=replace-with-a-long-random-secret
SUPABASE_URL=https://YOUR_PROJECT.supabase.co
SUPABASE_SERVICE_ROLE_KEY=YOUR_SUPABASE_SERVICE_ROLE_KEY
```

**Database:** Run `backend/db.sql` against your PostgreSQL database to create the base schema. Additional columns/tables used by newer features may require migrations before every feature works.

**Firebase Admin:** Place your own Firebase service-account JSON at `backend/serviceAccountKey.json` for local development. Do not commit this file. In production, the current backend code expects the file at `/etc/secrets/serviceAccountKey.json`.

Start the backend:

```bash
node server.js
```

The local API runs at `http://localhost:3000` by default.

### 3. Configure and serve the frontend

In `frontend/utils.js`, point the API URL to the local backend:

```javascript
window.API_URL = "http://localhost:3000";
```

Configure your **Firebase web-app settings** in `frontend/utils.js` and provide a properly restricted **Google Maps API key** in the Maps script included by `frontend/index.html`. Ensure local development origins are allowed in your Firebase/Google Cloud settings.

In a separate terminal:

```bash
cd frontend
python -m http.server 5500
```

Visit **`http://localhost:5500`**.

> **Setup note:** This is a multi-service app, not a zero-config clone-and-run project. Mapzo requires working credentials, a database, and matching frontend/backend configuration. The frontend currently references a deployed backend by default, so change that setting for local API testing.

## 🔌 API Highlights

| Method | Endpoint | Purpose |
| --- | --- | --- |
| `GET` | `/` | Backend status |
| `POST` | `/auth/google` | Sync authenticated Google user |
| `GET` | `/api/events` | Retrieve events |
| `GET` | `/api/events/nearby` | Find location-relevant events *(auth required)* |
| `GET` | `/api/events/:id` | Retrieve a single event |
| `POST` | `/api/events` | Publish an event *(auth required)* |
| `POST` | `/api/events/:eventId/join` | Join an event *(auth required)* |
| `GET` | `/api/events/:eventId/reviews` | Read event reviews |
| `POST` | `/api/events/:eventId/reviews` | Submit a review *(auth required)* |
| `GET` | `/api/events/:eventId/messages` | Read event conversation |
| `POST` | `/api/events/:eventId/messages` | Send a message *(auth required)* |
| `GET` | `/api/user/me` | Fetch the current profile *(auth required)* |
| `GET` | `/api/user/tickets` | View joined events *(auth required)* |

Protected endpoints expect a bearer token. See `backend/server.js` and `backend/route/auth.js` for request formats and authentication details.

##  Project Status

**Active project / evolving implementation.** The repository contains the application frontend, backend, and database setup, but some newer database fields and chat-related tables may need additional migration work for a completely fresh installation. Automated tests are not yet configured in the checked-in package scripts.

Potential next steps include simplifying configuration, consolidating database migrations, adding automated tests, and improving deployment documentation.

##  Contributing

Feedback, bug reports, and improvements are welcome. Open an [issue](https://github.com/daksh-arora-dev/mapzo/issues) to discuss a change, or submit a pull request with a clear description of the problem and your solution.

##  Creator

**Daksh Arora** · Founder, Mapzo
