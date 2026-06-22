# DRDO ESS Portal — Employee Self-Service Portal

> A full-stack web application built for **DRDO's Scientific Analysis Group**, providing employees with a unified portal for HR forms, IT asset tracking, grievance submission, real-time team chat, and an AI-powered HR assistant.

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Technology Stack](#2-technology-stack)
3. [Project Structure](#3-project-structure)
4. [Database Schema](#4-database-schema)
5. [Architecture Diagram](#5-architecture-diagram)
6. [All Flows — Explained in Detail](#6-all-flows--explained-in-detail)
   - [Authentication Flow](#61-authentication-flow)
   - [Route Protection Flow](#62-route-protection-flow)
   - [Forms Flow](#63-forms-flow)
   - [IT Asset Management Flow](#64-it-asset-management-flow)
   - [Grievance Submission Flow](#65-grievance-submission-flow)
   - [Real-Time Chat Flow](#66-real-time-chat-flow)
   - [AI HR Assistant Flow](#67-ai-hr-assistant-flow)
   - [Admin Real-Time Data Sync Flow](#68-admin-real-time-data-sync-flow)
   - [Admin Content Management Flow](#69-admin-content-management-flow)
7. [Page-by-Page Breakdown](#7-page-by-page-breakdown)
8. [API Reference](#8-api-reference)
9. [How to Run the Project](#9-how-to-run-the-project)
10. [Scaling the Application](#10-scaling-the-application)
11. [Interview Questions & Model Answers](#11-interview-questions--model-answers)

---

## 1. Project Overview

The ESS Portal is a **monorepo** containing two separate Node.js applications that work together:

| Part | Directory | Purpose |
|---|---|---|
| **Client** | `client/` | React SPA served to the browser |
| **Server** | `server/` | Express REST API + WebSocket server |

The application has two user roles:

- **Employee** — can log in, view the homepage, submit forms, view their assigned IT assets, submit grievances, use the AI assistant, and chat.
- **Admin** — has all employee privileges, plus access to a management dashboard for creating/deleting announcements, publications, events, managing users, assigning IT assets, and viewing all grievances and form submissions.

---

## 2. Technology Stack

### Frontend (`client/`)

| Technology | Version | Why it's used |
|---|---|---|
| **React** | 19 | UI component framework |
| **Vite** | 7 | Extremely fast dev server and bundler |
| **React Router v7** | 7 | Client-side routing (SPA navigation) |
| **TailwindCSS v4** | 4 | Utility-first CSS framework for styling |
| **Axios** | 1.11 | HTTP client for REST API calls |
| **Socket.io-client** | 4.8 | WebSocket client for real-time features |
| **@heroicons/react** | 2.2 | SVG icon library |
| **react-icons** | 5.5 | Additional icon sets |

### Backend (`server/`)

| Technology | Version | Why it's used |
|---|---|---|
| **Node.js** | LTS | JavaScript runtime |
| **Express** | 5 | HTTP web framework, handles all REST routes |
| **PostgreSQL** (`pg`) | 8.16 | Relational database |
| **Socket.io** | 4.8 | WebSocket server for real-time chat and data sync |
| **jsonwebtoken** | 9 | Creates and verifies JWT tokens for auth |
| **bcryptjs** | 3 | Securely hashes passwords |
| **dotenv** | 17 | Loads environment variables from `.env` |
| **cors** | 2.8 | Configures Cross-Origin Resource Sharing |
| **nodemon** | 3 | Auto-restarts server during development |

### External Service

| Service | Purpose |
|---|---|
| **HuggingFace Inference API** | Hosts `meta-llama/Meta-Llama-3-8B-Instruct` for all AI features |

---

## 3. Project Structure

```
ess-portal/
├── client/                         # React frontend
│   ├── public/                     # Static assets (images, etc.)
│   ├── src/
│   │   ├── assets/                 # Logos, images bundled by Vite
│   │   ├── components/
│   │   │   ├── forms/              # Actual fillable form components
│   │   │   │   ├── AnnualLeaveRequest.jsx
│   │   │   │   ├── ITSupportRequestForm.jsx
│   │   │   │   └── TravelReimbursementForm.jsx
│   │   │   ├── AdminRoute.jsx      # Blocks non-admins from admin pages
│   │   │   ├── Footer.jsx
│   │   │   ├── Navbar.jsx
│   │   │   ├── ProtectedLayout.jsx # Blocks unauthenticated users
│   │   │   ├── ProtectedRoute.jsx
│   │   │   ├── PublicLayout.jsx
│   │   │   ├── Sidebar.jsx
│   │   │   └── SubmissionDataViewer.jsx
│   │   ├── context/
│   │   │   ├── AuthContext.js      # React Context object definition
│   │   │   ├── AuthProvider.jsx    # State logic + login/logout functions
│   │   │   └── useAuth.js          # Custom hook to consume auth context
│   │   ├── hooks/
│   │   │   └── useSocket.js        # Socket.io hooks: useSocket, useAutoRefresh
│   │   ├── pages/                  # One component per route
│   │   │   ├── AIAssistantPage.jsx
│   │   │   ├── AdminDashboardPage.jsx
│   │   │   ├── AdminGrievancesPage.jsx
│   │   │   ├── AdminSubmissionsPage.jsx
│   │   │   ├── AssetManagementPage.jsx
│   │   │   ├── AssetsPage.jsx
│   │   │   ├── ChatPage.jsx
│   │   │   ├── FormDisplayPage.jsx
│   │   │   ├── FormsPage.jsx
│   │   │   ├── GrievancePage.jsx
│   │   │   ├── HomePage.jsx
│   │   │   ├── LoginPage.jsx
│   │   │   ├── ProfilePage.jsx
│   │   │   ├── PublicationsPage.jsx
│   │   │   ├── ResearchAreasPage.jsx
│   │   │   ├── ServicesPage.jsx
│   │   │   ├── UpcomingEventsPage.jsx
│   │   │   └── UserManagementPage.jsx
│   │   ├── services/
│   │   │   └── api.js              # Pre-configured Axios instance
│   │   ├── App.jsx                 # Route definitions
│   │   └── main.jsx                # React root entry point
│   ├── index.html
│   ├── vite.config.js
│   └── tailwind.config.js
│
├── server/                         # Express backend
│   ├── controllers/                # Business logic per feature
│   │   ├── aiController.js
│   │   ├── announcementController.js
│   │   ├── assetController.js
│   │   ├── authController.js
│   │   ├── eventController.js
│   │   ├── formsController.js
│   │   ├── grievanceController.js
│   │   ├── keyMomentsController.js
│   │   ├── projectController.js
│   │   ├── publicationController.js
│   │   └── userController.js
│   ├── middleware/
│   │   ├── authMiddleware.js       # Verifies JWT, attaches req.user
│   │   └── adminMiddleware.js      # Checks req.user.role === 'admin'
│   ├── routes/                     # Maps URLs to controller functions
│   │   ├── ai.js
│   │   ├── announcements.js
│   │   ├── assets.js
│   │   ├── auth.js
│   │   ├── events.js
│   │   ├── forms.js
│   │   ├── grievances.js
│   │   ├── keyMoments.js
│   │   ├── projects.js
│   │   ├── publications.js
│   │   └── users.js
│   ├── db.js                       # PostgreSQL connection pool
│   ├── schema.sql                  # Full database schema (run once to set up)
│   ├── hashGenerator.js            # Utility to pre-hash passwords
│   └── server.js                   # Entry point: Express app + Socket.io
│
├── package.json                    # Root-level scripts
└── README.md
```

---

## 4. Database Schema

The PostgreSQL database has **11 tables**. Here is the complete schema with explanations:

```sql
-- Users: core identity and role table
users (
    user_id       SERIAL PRIMARY KEY,
    email         VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,   -- bcrypt hash, never plain text
    first_name    VARCHAR(100) NOT NULL,
    last_name     VARCHAR(100) NOT NULL,
    role          VARCHAR(50)  DEFAULT 'employee',  -- 'employee' | 'admin'
    job_title     VARCHAR(100),
    department    VARCHAR(100)
)

-- Forms: metadata for each available form in the portal
forms (
    form_id        SERIAL PRIMARY KEY,
    title          VARCHAR(255) NOT NULL,
    description    TEXT,
    category       VARCHAR(100) NOT NULL,   -- e.g. 'Leave Management', 'IT Services'
    version        VARCHAR(20),
    last_updated   DATE,
    search_tags    TEXT,                    -- comma-separated keywords for search
    badge_text     VARCHAR(50),             -- e.g. 'New', 'Updated'
    badge_color    VARCHAR(50),             -- e.g. 'green', 'orange'
    component_name VARCHAR(255)             -- name of the React component to render
)

-- Form Submissions: each time a user submits a form
form_submissions (
    submission_id   SERIAL PRIMARY KEY,
    form_id         INTEGER REFERENCES forms(form_id),
    user_id         INTEGER REFERENCES users(user_id),
    status          VARCHAR(50) DEFAULT 'submitted',
    submission_data JSONB NOT NULL,         -- flexible JSON blob of form field values
    submitted_at    TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
)

-- IT Assets: the inventory of software licenses, hardware, etc.
it_assets (
    asset_id     SERIAL PRIMARY KEY,
    asset_name   VARCHAR(255) NOT NULL,
    asset_type   VARCHAR(100) NOT NULL,
    license_key  VARCHAR(255),
    purchase_date DATE,
    expiry_date   DATE,
    status        VARCHAR(50) DEFAULT 'Available'  -- 'Available' | 'Assigned'
)

-- Asset Allocations: tracks who has what asset and when
asset_allocations (
    allocation_id  SERIAL PRIMARY KEY,
    asset_id       INTEGER REFERENCES it_assets(asset_id),
    user_id        INTEGER REFERENCES users(user_id),
    assigned_date  TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    returned_date  TIMESTAMP WITH TIME ZONE NULL,   -- null = still assigned
    is_active      BOOLEAN DEFAULT true
)

-- Grievances: confidential employee complaints
grievances (
    grievance_id      SERIAL PRIMARY KEY,
    user_id           INTEGER REFERENCES users(user_id),
    grievance_subject VARCHAR(255) NOT NULL,
    grievance_details TEXT NOT NULL,
    status            VARCHAR(50) DEFAULT 'submitted',
    submitted_at      TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
)

-- Projects, Events, Publications, Announcements, Key Moments:
-- Content tables for the homepage and informational pages
projects       ( project_id, project_name, description, status )
events         ( event_id, day, month, title, description, event_date )
publications   ( publication_id, type, title, meta, description, pdf_link )
announcements  ( announcement_id, date, title, description )
key_moments    ( image_id, image_url, alt_text )
```

### Key design decisions

- **`submission_data JSONB`** — Each form has a different structure. Instead of creating a table per form, all form field values are stored as a JSON object. This makes the schema flexible as new forms are added.
- **`asset_allocations` as a history table** — Assets are never "deleted" from a user; instead, `is_active` is set to `false` and `returned_date` is recorded. This gives a full audit trail.
- **`component_name` on forms** — The database tells the frontend which React component to render for each form. This enables dynamic form routing: `FormDisplayPage` reads `component_name` and renders the matching component without hard-coding a mapping per form.

---

## 5. Architecture Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                        Browser (Client)                      │
│                                                             │
│  React SPA (Vite)                                           │
│  ┌───────────────────────────────────────────────────────┐  │
│  │  AuthProvider (React Context)                         │  │
│  │  - Stores user object + JWT in memory                 │  │
│  │  - Reads/writes token to localStorage                 │  │
│  └───────────────────────────────────────────────────────┘  │
│                                                             │
│  ┌────────────────┐    ┌─────────────────────────────────┐  │
│  │  React Router  │    │  Socket.io Client (useSocket.js) │  │
│  │  (client-side  │    │  - Persistent WS connection      │  │
│  │   navigation)  │    │  - chat-message, user-join, etc  │  │
│  └────────────────┘    └─────────────────────────────────┘  │
│           │                          │                      │
│     HTTP (Axios)               WebSocket (WS)               │
│           │                          │                      │
└───────────┼──────────────────────────┼─────────────────────┘
            │                          │
            ▼                          ▼
┌───────────────────────────────────────────────────────────┐
│                  Node.js / Express Server                   │
│                                                           │
│  ┌───────────────────────────────────────────────────┐    │
│  │  REST API (Express Router)                        │    │
│  │  /api/auth  /api/forms  /api/assets               │    │
│  │  /api/grievances  /api/users  /api/ai  ...        │    │
│  └───────────────────────────────────────────────────┘    │
│                                                           │
│  ┌──────────────────┐  ┌──────────────────────────────┐   │
│  │  authMiddleware  │  │  Socket.io Server             │   │
│  │  (JWT verify)    │  │  - connectedUsers Map         │   │
│  └──────────────────┘  │  - chatHistory array (100)    │   │
│  ┌──────────────────┐  │  - Emits: data-updated,       │   │
│  │  adminMiddleware │  │    new-message, users-online  │   │
│  │  (role check)    │  └──────────────────────────────┘   │
│  └──────────────────┘                                     │
│                                                           │
│  ┌───────────────────────────────────────────────────┐    │
│  │  Controllers (Business Logic)                     │    │
│  │  authController  assetController  aiController    │    │
│  │  formsController grievanceController  ...         │    │
│  └───────────────────────────────────────────────────┘    │
│                         │                                 │
└─────────────────────────┼─────────────────────────────────┘
                          │
              ┌───────────┴───────────┐
              │                       │
              ▼                       ▼
   ┌────────────────────┐   ┌──────────────────────┐
   │  PostgreSQL (pg)   │   │  HuggingFace API      │
   │  Connection Pool   │   │  (Llama 3 8B Instruct)│
   │  - 11 tables       │   │  - /v1/chat/completions│
   └────────────────────┘   └──────────────────────┘
```

---

## 6. All Flows — Explained in Detail

### 6.1 Authentication Flow

This flow covers both **login** and **page refresh** (re-hydration).

#### Login

```
User types email + password → clicks "Sign In"
        │
        ▼
LoginPage.jsx calls login(email, password) from AuthContext
        │
        ▼
AuthProvider.jsx → POST /api/auth/login  { email, password }
        │
        ▼ (server side)
authController.loginUser()
  1. SELECT * FROM users WHERE email = $1
  2. If no user → 400 "Invalid credentials"
  3. bcrypt.compare(password, user.password_hash)
  4. If no match → 400 "Invalid credentials"
  5. delete user.password_hash  (never send hash to client)
  6. jwt.sign({ user: { id, role } }, JWT_SECRET, { expiresIn: '5h' })
  7. res.json({ token, user })
        │
        ▼ (client side)
AuthProvider receives { token, user }
  1. localStorage.setItem('token', token)
  2. api.defaults.headers.common['x-auth-token'] = token  (attaches to all future Axios requests)
  3. setUser(user)  → triggers re-render across the whole app
  4. navigate('/')  → redirects to homepage
```

#### Page Refresh (Re-hydration)

When the user refreshes the browser, React state is lost. `AuthProvider` re-hydrates on mount:

```
Browser loads → AuthProvider mounts → useEffect fires
        │
        ▼
localStorage.getItem('token')
  → If no token: setLoading(false). User stays on login page.
  → If token exists:
        │
        ▼
api.defaults.headers.common['x-auth-token'] = storedToken
GET /api/auth  (with token in header)
        │
        ▼ (server side)
authMiddleware → jwt.verify(token, JWT_SECRET)
  → If invalid/expired: 401 → client removes token from localStorage
  → If valid: attaches decoded user to req.user → calls next()
        │
        ▼
authController.getLoggedInUser()
  SELECT user_id, first_name, last_name, email, role, job_title, department
  FROM users WHERE user_id = $1
        │
        ▼ (client side)
setUser(res.data)  → user is now logged in without re-entering credentials
setLoading(false)  → AuthProvider renders children
```

---

### 6.2 Route Protection Flow

There are two layers of protection on the frontend, both acting as "guards" before rendering a page.

#### `ProtectedLayout` — guards all authenticated routes

```
User navigates to any route except /login
        │
        ▼
ProtectedLayout checks: isAuthenticated = !!user (from AuthContext)
  → false: <Navigate to="/login" replace />  (redirect, no history entry)
  → true:  renders <Navbar /> + <Outlet /> + <Footer />
           (Outlet = the actual page component for the current route)
```

#### `AdminRoute` — guards admin-only routes

```
User navigates to /admin/dashboard (or any /admin/* route)
        │
        ▼
AdminRoute checks: isAdmin = user?.role === 'admin'
  → false: <Navigate to="/" replace />  (redirect to homepage)
  → true:  renders the wrapped child component (e.g., AdminDashboardPage)
```

On the **server**, every protected API endpoint runs middleware in this order:

```
HTTP Request arrives at Express
        │
        ▼
authMiddleware (runs first on protected routes)
  - Reads token from req.header('x-auth-token')
  - jwt.verify(token, JWT_SECRET) → decodes { user: { id, role } }
  - Attaches decoded payload to req.user
  - Calls next() to proceed
        │
        ▼
adminMiddleware (runs second, only on admin routes)
  - Checks: req.user.role === 'admin'
  - If not admin → 403 Forbidden
  - If admin → calls next()
        │
        ▼
Controller function handles the request
```

---

### 6.3 Forms Flow

The forms system has two parts: **browsing/searching** and **submitting**.

#### Browsing

```
User navigates to /forms
        │
        ▼
FormsPage.jsx → GET /api/forms
  → Returns all rows from the `forms` table, ordered by title
        │
        ▼
Client renders a grid of cards (one per form)
  - Category filter buttons: All / Leave Management / IT Services / Finance / HR / Procurement
  - Search input filters by form.search_tags (comma-separated keywords in DB)
  - useMemo() re-computes the filtered list whenever searchTerm or activeCategory changes
        │
        ▼
User clicks a form card → navigate to /forms/:formId
```

#### Rendering & Submitting

```
User visits /forms/:formId
        │
        ▼
FormDisplayPage.jsx → GET /api/forms/:id
  → Returns the form's metadata including component_name
        │
        ▼
FormDisplayPage uses component_name to dynamically pick which React
component to render, e.g. 'AnnualLeaveRequest' → <AnnualLeaveRequest />
        │
        ▼
User fills in the form fields and clicks Submit
        │
        ▼
Form component calls → POST /api/forms/submit
  Body: { formId, submissionData: { ...all field values as JSON } }
        │
        ▼ (server side)
formsController.submitForm()
  1. INSERT INTO form_submissions (form_id, user_id, submission_data)
     - user_id comes from req.user.id (decoded from JWT, not from the body)
     - submission_data is stored as a JSONB blob
  2. io.emit('data-updated', { type: 'submissions' })
     → All connected admin clients instantly see the new submission
  3. res.status(201).json({ msg: 'Form submitted successfully!' })
```

---

### 6.4 IT Asset Management Flow

Assets have a full lifecycle: added → assigned → unassigned → deleted.

#### Employee view (read-only)

```
Employee navigates to /assets
        │
        ▼
AssetsPage.jsx → GET /api/assets/my
  - authMiddleware runs (JWT required)
  - Query joins asset_allocations + it_assets WHERE user_id = req.user.id AND is_active = true
  - Returns only assets currently assigned to the logged-in user
```

#### Admin management

```
Admin navigates to /admin/assets
        │
        ▼
AssetManagementPage.jsx → GET /api/assets  (requires admin)
  - LEFT JOIN it_assets with asset_allocations (is_active=true) and users
  - Returns all assets with their current assignee's email (or null if unassigned)
        │
  ┌─────┴──────────────────────────────────────┐
  │                                             │
  ▼                                             ▼
Add new asset                           Assign existing asset
POST /api/assets                        POST /api/assets/assign
  1. INSERT INTO it_assets               1. Lookup user by email
  2. If email provided:                  2. INSERT INTO asset_allocations
     - lookup user by email              3. UPDATE it_assets SET status='Assigned'
     - INSERT INTO asset_allocations     4. io.emit('data-updated')
     - UPDATE status = 'Assigned'
  3. io.emit('data-updated')
        │
        ▼
Unassign asset
PUT /api/assets/unassign
  1. Find active allocation for assetId
  2. UPDATE allocation: is_active=false, returned_date=NOW()
  3. UPDATE it_assets: status='Available'
  4. io.emit('data-updated')
        │
        ▼
DELETE /api/assets/:id
  1. DELETE FROM asset_allocations WHERE asset_id = $1
  2. DELETE FROM it_assets WHERE asset_id = $1
  3. io.emit('data-updated')
```

---

### 6.5 Grievance Submission Flow

```
Employee navigates to /grievance
        │
        ▼
GrievancePage.jsx — user types subject + details → clicks "Submit Confidentially"
        │
        ▼
POST /api/grievances/submit  { subject, details }
  - authMiddleware: JWT verified, userId extracted
        │
        ▼
grievanceController.submitGrievance()
  INSERT INTO grievances (user_id, grievance_subject, grievance_details)
  io.emit('data-updated', { type: 'grievances' })
  res.status(201)
```

**Note on privacy:** The grievance stores the `user_id` internally, but the admin-facing query (`getAllGrievances`) intentionally does NOT join the users table — it only returns `grievance_id, grievance_subject, grievance_details, status, submitted_at`. The employee's identity is hidden from admins at the database query level.

```
Admin navigates to /admin/grievances
        │
        ▼
AdminGrievancesPage → GET /api/grievances
  - Returns grievances WITHOUT user identity
  - Admin can also call POST /api/ai/analyze-grievance for AI analysis:
        │
        ▼
  aiController.analyzeGrievance()
    Sends grievance text to Llama 3 with a JSON schema prompt
    Returns structured analysis:
    {
      sentiment: 'negative',
      severity: 7,
      category: 'Workplace Safety',
      keywords: ['unsafe', 'machinery', 'injury risk'],
      suggestedPriority: 'high',
      summary: '...',
      suggestedAction: '...'
    }
```

---

### 6.6 Real-Time Chat Flow

The chat is built entirely on **WebSockets** using Socket.io. There are no database writes — messages are held in a server-side in-memory array with a cap of 100 messages.

```
User navigates to /chat
        │
        ▼
ChatPage.jsx mounts
  socket.emit('user-join', { user_id, first_name, last_name, role })
        │
        ▼ (server)
io.on('connection') handler:
  1. connectedUsers.set(socket.id, userData)      ← stores user keyed by socket ID
  2. socket.emit('chat-history', chatHistory)     ← sends last 100 messages to the newcomer
  3. io.emit('users-online', [...connectedUsers]) ← broadcasts updated online list to ALL clients
        │
        ▼ (client receives)
ChatPage: setMessages(history), setOnlineUsers(users)
        │
User types in input field
  - onChange fires handleTyping()
  - socket.emit('typing', true)            ← server broadcasts to all OTHER clients
  - setTimeout after 2s → socket.emit('typing', false)
        │
        ▼
User presses Send
  socket.emit('chat-message', { content })
        │
        ▼ (server)
  1. Look up user from connectedUsers Map by socket.id
  2. Build message object: { id: Date.now(), user_id, first_name, last_name, role, content, timestamp }
  3. chatHistory.push(message)
  4. if chatHistory.length > 100: chatHistory.shift()  ← circular buffer
  5. io.emit('new-message', message)   ← broadcasts to ALL connected clients
        │
        ▼ (all clients)
ChatPage: setMessages(prev => [...prev, message])
        │
User closes tab / navigates away
  socket fires 'disconnect' event
  connectedUsers.delete(socket.id)
  io.emit('users-online', [...connectedUsers])  ← online list updates for everyone
```

---

### 6.7 AI HR Assistant Flow

The AI assistant uses the **HuggingFace Inference API** with `meta-llama/Meta-Llama-3-8B-Instruct`. It supports both standard and streaming responses.

#### Context Building (happens on every request)

```
Before generating a response, the server fetches live data from the database:

Promise.all([
  SELECT title, description, category FROM forms,
  SELECT title, description, date FROM announcements ORDER BY ... LIMIT 10,
  SELECT title, description, event_date FROM events ORDER BY ... LIMIT 10,
  SELECT title, type, meta, description FROM publications ORDER BY ... LIMIT 5,
])

This data is injected into the system prompt so the AI knows about:
- What forms exist and their categories
- Recent company announcements
- Upcoming events
- Recent publications
```

#### Streaming Chat (primary mode)

```
User types message → clicks Send
        │
        ▼
AIAssistantPage.jsx
  1. Adds user message to local state
  2. Adds empty assistant message placeholder
  3. Calls fetch() (NOT axios) against /api/ai/chat/stream
     - Uses the Fetch ReadableStream API to process SSE incrementally
        │
        ▼ (server)
aiController.chatStream()
  1. Sets response headers:
     Content-Type: text/event-stream
     Cache-Control: no-cache
     Connection: keep-alive
  2. Builds context from database
  3. Constructs messages array:
     [ { role: 'system', content: systemPrompt },
       ...conversationHistory.slice(-6),   ← last 6 messages for context window
       { role: 'user', content: message } ]
  4. Calls HuggingFace API (single HTTP call, not true streaming)
  5. Simulates streaming: splits response by spaces, sends each word as SSE:
     data: {"content": "Hello "}
     data: {"content": "there! "}
     ...
     data: [DONE]
        │
        ▼ (client)
  ReadableStream reader reads chunks
  For each `data: {...}` line:
    - Parses JSON, extracts content
    - Appends to fullResponse
    - Updates the last message in state with growing text
    - This creates a "typewriter" effect
        │
  If stream fails → falls back to POST /api/ai/chat (non-streaming)
```

---

### 6.8 Admin Real-Time Data Sync Flow

When an admin performs an action (add/delete announcement, assign asset, etc.), connected clients automatically receive the update without polling. This is the `data-updated` WebSocket event pattern.

```
Admin deletes an announcement via /admin/dashboard
        │
        ▼
DELETE /api/announcements/:id
  1. DB delete executes
  2. const io = req.app.get('io')  ← Express stores Socket.io instance on app
     io.emit('data-updated', { type: 'announcements' })
        │
        ▼ (broadcasts to ALL connected WebSocket clients)
        │
        ├─────────────────────────────────────────────────┐
        ▼                                                 ▼
  AdminDashboardPage (another admin tab)          HomePage (employee tabs)
  useAutoRefresh('announcements', fetchAnnouncements)
        │
  socket.on('data-updated', handler)
  if (data.type === 'announcements') {
    fetchAnnouncements()  ← re-fetches from API
  }
        │
        ▼
  setAnnouncements(res.data)  ← UI updates instantly
```

The `useAutoRefresh` hook in `useSocket.js` creates this pattern reusably. Any page can subscribe to a specific data type. Currently connected: `announcements`, `key-moments`, `events`, `publications`, `assets`, `submissions`, `grievances`.

---

### 6.9 Admin Content Management Flow

Admins manage homepage content from the dashboard. The pattern is the same for all content types:

```
Admin Dashboard → /admin/dashboard
  ├── Manage Key Moments Images (gallery on homepage)
  │     POST /api/key-moments  { imageUrl, altText }
  │     DELETE /api/key-moments/:id
  │
  ├── Manage Announcements (scrolling ticker on homepage)
  │     POST /api/announcements  { date, title, description }
  │     DELETE /api/announcements/:id
  │
  ├── → /admin/users     (UserManagementPage)
  │     GET/POST/PUT/DELETE /api/users
  │
  ├── → /admin/assets    (AssetManagementPage)
  │     Full CRUD for IT assets + assignment management
  │
  ├── → /admin/grievances  (AdminGrievancesPage)
  │     GET /api/grievances  + AI analysis per grievance
  │
  └── → /admin/submissions  (AdminSubmissionsPage)
        GET /api/forms/submissions  (all form submissions with user + form details)
```

---

## 7. Page-by-Page Breakdown

| Route | Page Component | Who Can Access | What it Does |
|---|---|---|---|
| `/login` | `LoginPage` | Anyone | Email/password login form with show/hide password toggle |
| `/` | `HomePage` | Authenticated | Dashboard with auto-scrolling gallery, announcements ticker, services links, research areas, publications, events |
| `/about` | `AboutUsPage` | Authenticated | Static info about DRDO SAG |
| `/research` | `ResearchAreasPage` | Authenticated | Research areas pulled from `projects` table |
| `/publications` | `PublicationsPage` | Authenticated | Full list of publications with PDF links |
| `/events` | `UpcomingEventsPage` | Authenticated | All upcoming events from database |
| `/contact` | `ContactUsPage` | Authenticated | Static contact information |
| `/services` | `ServicesPage` | Authenticated | Overview of portal services |
| `/forms` | `FormsPage` | Authenticated | Searchable/filterable grid of all available HR forms |
| `/forms/:formId` | `FormDisplayPage` | Authenticated | Dynamically loads the correct form component and handles submission |
| `/assets` | `AssetsPage` | Authenticated | Shows assets currently assigned to the logged-in user |
| `/grievance` | `GrievancePage` | Authenticated | Confidential grievance submission form |
| `/profile` | `ProfilePage` | Authenticated | Edit own first name, last name, job title, department |
| `/ai-assistant` | `AIAssistantPage` | Authenticated | Streaming AI chat with quick action prompts |
| `/chat` | `ChatPage` | Authenticated | Real-time team chat with online users list and typing indicators |
| `/admin/dashboard` | `AdminDashboardPage` | **Admin only** | Manage announcements + key moment images |
| `/admin/users` | `UserManagementPage` | **Admin only** | Full user CRUD (add, edit, delete users) |
| `/admin/assets` | `AssetManagementPage` | **Admin only** | Full IT asset CRUD + assignment + history |
| `/admin/grievances` | `AdminGrievancesPage` | **Admin only** | View all grievances + AI analysis |
| `/admin/submissions` | `AdminSubmissionsPage` | **Admin only** | View all form submissions with user and form details |

---

## 8. API Reference

### Authentication (`/api/auth`)

| Method | Endpoint | Auth Required | Description |
|---|---|---|---|
| `POST` | `/api/auth/register` | No | Register a new user account |
| `POST` | `/api/auth/login` | No | Login, returns `{ token, user }` |
| `GET` | `/api/auth` | Yes (JWT) | Returns the currently logged-in user's profile |

### Users (`/api/users`)

| Method | Endpoint | Auth Required | Description |
|---|---|---|---|
| `GET` | `/api/users` | Admin | Get all users |
| `POST` | `/api/users` | Admin | Create a user (admin sets role) |
| `PUT` | `/api/users/:userIdToUpdate` | JWT (own or admin) | Update name/job title/department |
| `DELETE` | `/api/users/:id` | Admin | Delete a user |

### Forms (`/api/forms`)

| Method | Endpoint | Auth Required | Description |
|---|---|---|---|
| `GET` | `/api/forms` | Yes | Get all available forms |
| `GET` | `/api/forms/:id` | Yes | Get a single form's metadata |
| `POST` | `/api/forms/submit` | Yes | Submit a completed form |
| `GET` | `/api/forms/submissions` | Admin | Get all form submissions (with user + form info) |

### IT Assets (`/api/assets`)

| Method | Endpoint | Auth Required | Description |
|---|---|---|---|
| `GET` | `/api/assets/my` | Yes | Get assets assigned to current user |
| `GET` | `/api/assets` | Admin | Get all assets with current assignee |
| `POST` | `/api/assets` | Admin | Add a new asset (optionally assign immediately) |
| `DELETE` | `/api/assets/:id` | Admin | Delete asset and all its allocations |
| `POST` | `/api/assets/assign` | Admin | Assign an asset to a user by email |
| `PUT` | `/api/assets/unassign` | Admin | Mark an asset as returned/available |
| `GET` | `/api/assets/history` | Admin | Full allocation history for all assets |

### Grievances (`/api/grievances`)

| Method | Endpoint | Auth Required | Description |
|---|---|---|---|
| `POST` | `/api/grievances/submit` | Yes | Submit a grievance |
| `GET` | `/api/grievances` | Admin | Get all grievances (no user identity exposed) |

### AI (`/api/ai`)

| Method | Endpoint | Auth Required | Description |
|---|---|---|---|
| `POST` | `/api/ai/chat` | Yes | Standard (non-streaming) AI chat |
| `POST` | `/api/ai/chat/stream` | Yes | SSE streaming AI chat |
| `POST` | `/api/ai/analyze-grievance` | Admin | AI analysis of a grievance |
| `POST` | `/api/ai/suggest-fields` | Yes | AI form field suggestions |
| `GET` | `/api/ai/dashboard-insights` | Admin | AI-generated dashboard summary |

### Content (`/api/announcements`, `/api/events`, etc.)

All content endpoints follow the same pattern: `GET /` (public/auth), `POST /` (admin), `DELETE /:id` (admin). This covers: `announcements`, `events`, `publications`, `projects`, `key-moments`.

---

## 9. How to Run the Project

### Prerequisites

- Node.js 18+
- PostgreSQL 14+
- A HuggingFace account with an API key

### Environment Variables

Create `server/.env`:

```env
DATABASE_URL=postgresql://username:password@localhost:5432/ess_portal
JWT_SECRET=your_super_secret_jwt_key_here
HF_API_KEY=hf_xxxxxxxxxxxxxxxxxxxxxxxxxx
PORT=5000
CLIENT_ORIGIN=http://localhost:5173
```

Create `client/.env`:

```env
VITE_API_URL=http://localhost:5000
```

### Setup

```bash
# 1. Set up the database
psql -U postgres -c "CREATE DATABASE ess_portal;"
psql -U postgres -d ess_portal -f server/schema.sql

# 2. Install server dependencies
cd server
npm install

# 3. Install client dependencies
cd ../client
npm install
```

### Running in Development

```bash
# Terminal 1 — start the backend
cd server
npm run dev       # nodemon watches for changes, restarts automatically

# Terminal 2 — start the frontend
cd client
npm run dev       # Vite dev server at http://localhost:5173
```

### Building for Production

```bash
cd client
npm run build     # outputs to client/dist/

# Deploy dist/ to a static host (Vercel, Netlify, etc.)
# Deploy server/ to a Node.js host (Railway, Render, Fly.io, etc.)
```

---

## 10. Scaling the Application

The current architecture works well for a small team but has identifiable bottlenecks as user count grows. Here is a concrete scaling roadmap, from easiest to most impactful:

### 10.1 In-Memory Chat History → Redis

**Problem:** Chat messages are stored in a JavaScript array in the server process (`chatHistory` in `server.js`). If the server restarts, history is lost. With multiple server processes (horizontal scaling), each process has its own array and users on different servers will not see each other's messages.

**Solution:** Replace the in-memory array with **Redis Pub/Sub + Redis Lists**.

```
Server 1 receives message → PUBLISH chat_channel message → Redis
Redis → SUBSCRIBE callback on Server 1 AND Server 2 → both broadcast to their clients
Redis LPUSH chat_history message; LTRIM chat_history 0 99  ← persistent, capped list
```

### 10.2 Single Server → Horizontal Scaling with a Load Balancer

**Problem:** A single Node.js process is limited to one CPU core. Socket.io connections are tied to a specific process.

**Solution:**
1. Run multiple instances behind an **Nginx** or **AWS ALB** load balancer with **sticky sessions** (ensures a user's WebSocket stays on the same server).
2. Use **Socket.io Redis Adapter** (`@socket.io/redis-adapter`) so Socket.io events like `io.emit('data-updated')` broadcast across all server instances.

```bash
npm install @socket.io/redis-adapter
```

```js
// server.js
const { createAdapter } = require('@socket.io/redis-adapter');
const { createClient } = require('redis');
const pubClient = createClient({ url: process.env.REDIS_URL });
const subClient = pubClient.duplicate();
io.adapter(createAdapter(pubClient, subClient));
```

### 10.3 PostgreSQL → Connection Pooling via PgBouncer

**Problem:** Each Node.js process holds a pool of direct PostgreSQL connections. PostgreSQL has a hard limit (~100–300 connections). With multiple server instances, this exhausts the limit fast.

**Solution:** Add **PgBouncer** between the app and Postgres. All servers connect to PgBouncer, which maintains a small number of actual Postgres connections and multiplexes them efficiently. Alternatively, use **Neon** or **Supabase** which handle this transparently.

### 10.4 Database Read Replicas

**Problem:** Most queries are reads (fetching forms, events, publications). All traffic goes to one Postgres primary.

**Solution:** Add **read replicas**. Route `SELECT` queries to replicas and `INSERT/UPDATE/DELETE` queries to the primary. In `db.js`, maintain two pool instances:

```js
const writePool = new Pool({ connectionString: process.env.DATABASE_PRIMARY_URL });
const readPool  = new Pool({ connectionString: process.env.DATABASE_REPLICA_URL });
```

### 10.5 AI Requests → Queue with Rate Limiting

**Problem:** Every AI chat request makes a synchronous HTTP call to HuggingFace, which can be slow (5–20s). Under load, requests pile up, exhaust server threads, and users see timeouts.

**Solution:** Use a **job queue** (e.g., **BullMQ** with Redis) for AI requests. The server immediately returns a job ID. The client polls or subscribes via WebSocket for the result.

```
Client → POST /api/ai/chat → Server enqueues job → Returns { jobId }
Worker process → picks up job → calls HuggingFace → stores result in Redis
Client → socket.on('ai-response-${jobId}') → receives result
```

### 10.6 Static Assets → CDN

**Problem:** Images in the `key_moments` gallery are served from the application server.

**Solution:** Upload images to **AWS S3** or **Cloudflare R2** and store the full CDN URL in the `image_url` column. The server never serves image bytes.

### 10.7 Caching Frequently Read Data

**Problem:** The homepage fetches `announcements`, `events`, `publications`, and `key_moments` on every load.

**Solution:** Cache these responses in **Redis** with a short TTL (30–60 seconds). When an admin creates/deletes content, invalidate the relevant cache key. The `data-updated` Socket.io event already signals this — hook into it server-side to call `redis.del('cache:announcements')`.

### 10.8 Authentication → Refresh Tokens

**Problem:** JWT tokens currently expire in 5 hours. Users are forced to re-login, and there is no way to invalidate a token immediately (e.g., after a password change or account deletion).

**Solution:**
- Issue short-lived **access tokens** (15 minutes) + long-lived **refresh tokens** (7 days, stored in an `httpOnly` cookie).
- Store refresh tokens in a `refresh_tokens` database table.
- The client silently refreshes the access token using the refresh token when it receives a 401.
- On logout/account deletion, delete the refresh token row — the user is effectively locked out at the next access token expiry.

### Scaling Summary

| Concern | Current | Scaled Solution |
|---|---|---|
| Chat history | In-memory array | Redis List + Pub/Sub |
| Multiple server processes | Not supported | Redis Socket.io Adapter |
| DB connections | Direct pool | PgBouncer |
| DB read load | Single primary | Read replicas |
| AI latency | Synchronous HTTP | BullMQ job queue |
| Image serving | App server | S3/R2 CDN |
| Repeated DB reads | None | Redis cache + TTL |
| Session revocation | Not possible | Refresh token table in DB |

---

## 11. Interview Questions & Model Answers

These are the most likely questions an interviewer would ask about this project, covering architecture, security, real-time systems, React patterns, and database design.

---

### Q1: Walk me through the authentication system. How does it work end-to-end?

**Answer:**

Authentication is **stateless JWT-based**. When a user logs in with their email and password, the server first looks up the user in the `users` table. It then uses `bcryptjs.compare()` to check the submitted password against the stored hash — passwords are never stored in plain text.

If credentials are valid, the server signs a **JSON Web Token** using a secret key stored in an environment variable. The token's payload contains the user's ID and role (`{ user: { id, role } }`), and it expires in 5 hours.

The client stores this token in `localStorage` and attaches it to every subsequent API request via Axios's `defaults.headers.common['x-auth-token']`. On the server side, the `authMiddleware` reads this header, verifies the signature, and attaches the decoded user object to `req.user`. This means any controller can trust `req.user.id` without querying the database again.

On page refresh, the `AuthProvider` re-hydrates by reading the token from `localStorage` and calling `GET /api/auth` to get the full user object, re-establishing the React state.

---

### Q2: How does the real-time data synchronization work between the admin and employees?

**Answer:**

Both the HTTP server and the Socket.io WebSocket server run on the same Node.js process. The Socket.io instance (`io`) is stored on the Express app object via `app.set('io', io)`. Any controller can retrieve it with `req.app.get('io')`.

When an admin performs a write operation (adding an announcement, assigning an asset, etc.), the controller first commits the change to the database, then calls `io.emit('data-updated', { type: 'announcements' })`. This broadcasts a WebSocket event to **every connected client**.

On the frontend, the `useAutoRefresh` custom hook registers a listener for the `data-updated` event. If the `type` matches what that page cares about, it calls a re-fetch function. For example, the `HomePage` subscribes to `announcements`, `events`, `publications`, and `key-moments`. The moment an admin adds a new announcement, every employee's homepage updates itself by re-fetching from the REST API.

This is a **push-then-pull** pattern: the server pushes a small signal ("something changed"), and clients pull the full fresh data. It avoids sending large payloads over WebSocket while still being reactive.

---

### Q3: What is the `submission_data JSONB` column in `form_submissions` and why was it designed that way?

**Answer:**

JSONB is PostgreSQL's binary JSON column type. It allows storing arbitrary JSON objects efficiently, with support for indexing and operators.

The design choice here is deliberate: each form in the portal has a completely different set of fields. An Annual Leave Request has fields like `startDate`, `endDate`, `leaveType`. An IT Support Request has `issueType`, `priority`, `description`. Rather than creating a separate database table for each form (which would require schema migrations every time a new form is added), all field values are serialized into a single JSON object.

This trades some queryability for extreme flexibility. A new form can be added to the `forms` table and a new React component can be built without any database schema changes. The tradeoff is that you cannot easily filter submissions by specific field values with SQL `WHERE` clauses unless you use PostgreSQL's JSON operators (e.g., `submission_data->>'startDate'`).

---

### Q4: How does the AI streaming work? What is SSE?

**Answer:**

SSE stands for **Server-Sent Events**. It's a one-way HTTP streaming protocol where the server keeps a response open and sends chunks of data over time. The `Content-Type` is set to `text/event-stream`, and each event is formatted as `data: <payload>\n\n`.

In the AI assistant, when the user sends a message, the frontend uses the browser's native `fetch` API with `response.body.getReader()` to get a `ReadableStream`. It reads chunks as they arrive, parses each `data: {...}` line, extracts the `content` property, and appends it to the growing response string. This updates the React state character by character, creating a typewriter effect.

One important detail: the current implementation calls the HuggingFace API once and receives the full response, then *simulates* streaming by splitting the text on spaces and sending each word with a 30ms delay. True streaming would require the upstream API to support it natively (using `stream: true` in the request). The UX result is the same, but the server holds the full response in memory before sending it.

---

### Q5: How is admin access enforced? Could a malicious user bypass it?

**Answer:**

There are two independent layers of enforcement:

**Frontend:** The `AdminRoute` component checks `user.role === 'admin'` from React Context and redirects non-admins to `/`. However, this is purely a UX guard — a determined user could manipulate local state or navigate directly via URL.

**Backend:** Every admin API endpoint applies `adminMiddleware` after `authMiddleware`. This middleware reads `req.user.role`, which was decoded from the JWT by `authMiddleware`. Since the JWT is signed with a server-side secret, the role cannot be forged without knowing the secret. A malicious user cannot change their role in the token.

The `role` field is set at registration time (defaulting to `'employee'`) and can only be changed by an admin through the `POST /api/users` endpoint, which itself is protected by `adminMiddleware`. This creates a closed loop.

---

### Q6: Why does the grievance system not expose the submitter's identity to admins?

**Answer:**

The `getAllGrievances` query is written this way intentionally:

```sql
SELECT grievance_id, grievance_subject, grievance_details, status, submitted_at
FROM grievances ORDER BY submitted_at DESC
```

It deliberately omits a `JOIN` on the `users` table. Even though the `user_id` is stored in the row (required for foreign key integrity and potential internal audit use), the API response never includes it. An admin reading grievances through the portal UI cannot determine who submitted them.

This is an **application-level privacy control** rather than a database-level one. A proper production upgrade would use row-level security in PostgreSQL and restrict direct database access, so even a developer with DB credentials cannot easily correlate a grievance to a user without extra steps.

---

### Q7: What is the difference between `useSocket` and `useAutoRefresh`?

**Answer:**

Both are custom hooks exported from `useSocket.js`. A single `socket` instance is created once at module load time (a module-level singleton) and shared across all hook invocations.

`useSocket(event, callback)` is a generic event listener hook. It registers a Socket.io listener for any named event, calls the provided callback when that event fires, and cleans up the listener when the component unmounts. It uses a `useRef` for the callback to avoid the listener being re-registered every time the callback function reference changes.

`useAutoRefresh(dataType, fetchFunction)` is a specialized version built on top of the same pattern. It listens specifically for the `data-updated` event and only calls `fetchFunction` if `data.type === dataType`. It's used on pages that need to re-fetch data when an admin makes a change (e.g., the homepage re-fetches announcements when an admin adds one).

---

### Q8: How does the application handle the case where the HuggingFace model is loading?

**Answer:**

HuggingFace's free inference API returns a 503 error with an error message containing the word "loading" when the model has been idle and is warming up (a cold start). The `generateText()` function in `aiController.js` catches this and throws a user-friendly error: `"AI Model is warming up. Please try again in 20 seconds."`. This propagates to the route handler, which sends it in the error response.

On the client, if the streaming request fails (including this scenario), the `AIAssistantPage` has a fallback to the non-streaming `/api/ai/chat` endpoint. If that also fails, it shows a graceful error message to the user rather than crashing.

---

### Q9: The chat history is stored in a JavaScript array on the server. What are the implications?

**Answer:**

There are three significant implications:

1. **Persistence:** If the server process restarts (crash, deployment, etc.), all chat history is lost. Users reconnecting after a restart will see an empty chat.

2. **Multi-process incompatibility:** If the application is scaled horizontally (multiple server instances behind a load balancer), each process has its own independent `chatHistory` array. Users on Server A and users on Server B would have different histories and would not see each other's messages.

3. **Memory:** The array is capped at 100 messages via a `shift()` when it exceeds this limit (a circular buffer pattern), so memory usage is bounded.

For production, this should be replaced with **Redis** (using `LPUSH`/`LRANGE`/`LTRIM` for the history and Pub/Sub for broadcasting), or messages should be persisted to a `chat_messages` database table.

---

### Q10: How would you add a new HR form to the portal?

**Answer:**

The system is designed to make this straightforward thanks to the `component_name` field:

1. **Create the React component** at `client/src/components/forms/NewFormName.jsx`. It should accept props, render the form fields, and call the submit API on submission.

2. **Add a mapping** in `FormDisplayPage.jsx` that links the string `'NewFormName'` to the actual component import.

3. **Insert a row** into the `forms` table:
   ```sql
   INSERT INTO forms (title, description, category, version, last_updated, search_tags, badge_text, badge_color, component_name)
   VALUES ('New Form Title', 'Description', 'HR', 'v1.0', NOW(), 'keyword1,keyword2', 'New', 'green', 'NewFormName');
   ```

That's it. The `FormsPage` automatically picks up the new form from the API. The `FormDisplayPage` dynamically renders the correct component based on `component_name`. No other code changes are needed. This is the **Open/Closed Principle** in practice: the system is open for extension without modifying existing code.

---

### Q11: How is CORS configured and why does it matter?

**Answer:**

CORS (Cross-Origin Resource Sharing) is a browser security mechanism that blocks JavaScript on one domain from making HTTP requests to a different domain unless the server explicitly allows it.

In this application, the React client runs on `localhost:5173` (Vite) and the Express server runs on `localhost:5000`. These are different origins, so CORS must be configured.

The `cors` middleware is configured with a whitelist function that checks `origin` against the `allowedOrigins` array, which includes `localhost:5173`, `localhost:3000`, and optionally a production domain from the `CLIENT_ORIGIN` environment variable. The `credentials: true` option is necessary to allow the `x-auth-token` header (and cookies if used) to be sent cross-origin.

The same CORS policy is applied to both the Express REST routes and the Socket.io server independently, since they have separate CORS configurations.

---

*Built with ❤️ by Nemida for DRDO SAG.*
