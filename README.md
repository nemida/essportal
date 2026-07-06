# Employee Self-Service (ESS) Portal

A comprehensive web-based portal designed for internal organization management. This system streamlines employee services including leave management, IT asset tracking, grievance redressal, and internal communications.

https://github.com/user-attachments/assets/83f2357e-a46c-485c-a66f-85d386e99d39

---

## Project Overview

The ESS Portal is a **monorepo** containing two separate Node.js applications that work together:

| Part | Directory | Purpose |
|---|---|---|
| **Client** | `client/` | React SPA served to the browser |
| **Server** | `server/` | Express REST API + WebSocket server |

The application has two user roles:

- **Employee** — can log in, view the homepage, submit forms, view their assigned IT assets, submit grievances, use the AI assistant, and chat.
- **Admin** — has all employee privileges, plus access to a management dashboard for creating/deleting announcements, publications, events, managing users, assigning IT assets, and viewing all grievances and form submissions.

---

## Features

* **Role-Based Access Control**
  * **Admin Dashboard:** Full system administration with user management, grievance inbox, and form submission reviews.
  * **Employee Access:** Personal profile management, form submissions, and asset viewing.

* **Form Management**
  * Digital submission system for various organizational forms.
  * Form tracking with submission status.
  * Admin review and approval workflow.

* **Asset Management**
  * Track IT assets including hardware and software licenses.
  * Asset allocation and assignment history.
  * Employee asset viewing via `/my` endpoint for personal assets.

* **Information Hub**
  * **Announcements:** Organization-wide announcements management.
  * **Events:** Event calendar and event details.
  * **Publications:** Research publications repository.
  * **Key Moments:** Image gallery for organizational highlights.

* **Grievance Redressal**
  * Secure grievance submission system.
  * Admin grievance inbox for review and resolution.

* **Project Tracking**
  * Overview of active research projects.
  * Project status management.

* **AI Assistant**
  * Integrated AI chat functionality.
  * Grievance analysis (admin only).
  * Form field suggestions.
  * Dashboard insights generation.

* **Real-Time Chat**
  * Team communication feature.
    
---

## Tech Stack

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


## Project Structure

```
ess-portal/
├── client/                         # the React frontend
│   ├── public/                     # static stuff - images etc, served as-is
│   ├── src/
│   │   ├── assets/                 # logos and images that Vite bundles
│   │   ├── components/
│   │   │   ├── forms/              # the actual forms people fill out
│   │   │   │   ├── AnnualLeaveRequest.jsx
│   │   │   │   ├── ITSupportRequestForm.jsx
│   │   │   │   └── TravelReimbursementForm.jsx
│   │   │   ├── AdminRoute.jsx      # kicks you out if you're not an admin
│   │   │   ├── Footer.jsx
│   │   │   ├── Navbar.jsx
│   │   │   ├── ProtectedLayout.jsx # won't let you in unless you're logged in
│   │   │   ├── ProtectedRoute.jsx
│   │   │   ├── PublicLayout.jsx
│   │   │   ├── Sidebar.jsx
│   │   │   └── SubmissionDataViewer.jsx
│   │   ├── context/
│   │   │   ├── AuthContext.js      # just the raw Context object
│   │   │   ├── AuthProvider.jsx    # where login/logout actually live
│   │   │   └── useAuth.js          # hook so we don't import Context everywhere
│   │   ├── hooks/
│   │   │   └── useSocket.js        # socket.io hooks - useSocket, useAutoRefresh
│   │   ├── pages/                  # one file per route, pretty much
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
│   │   │   └── api.js              # our Axios instance, already configured
│   │   ├── App.jsx                 # all the routes live here
│   │   └── main.jsx                # where React actually mounts
│   ├── index.html
│   ├── vite.config.js
│   └── tailwind.config.js
│
├── server/                         # the Express backend
│   ├── controllers/                # where the real logic happens, per feature
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
│   │   ├── authMiddleware.js       # checks the JWT, sticks user on req
│   │   └── adminMiddleware.js      # makes sure req.user.role is 'admin'
│   ├── routes/                     # just wires URLs to controller functions
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
│   ├── db.js                       # PostgreSQL pool lives here
│   ├── schema.sql                  # run this once to set up the DB
│   ├── hashGenerator.js            # little utility for pre-hashing passwords
│   └── server.js                   # entry point - Express + Socket.io
│
├── package.json                    # scripts for the whole repo
└── README.md
```

---

## Database Schema

The PostgreSQL database has **11 tables**.

```sql
-- who's who, basically - identity + role
users (
    user_id       SERIAL PRIMARY KEY,
    email         VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,   -- bcrypt hash, obviously never plain text
    first_name    VARCHAR(100) NOT NULL,
    last_name     VARCHAR(100) NOT NULL,
    role          VARCHAR(50)  DEFAULT 'employee',  -- 'employee' or 'admin'
    job_title     VARCHAR(100),
    department    VARCHAR(100)
)

-- every form we offer in the portal, with its metadata
forms (
    form_id        SERIAL PRIMARY KEY,
    title          VARCHAR(255) NOT NULL,
    description    TEXT,
    category       VARCHAR(100) NOT NULL,   -- e.g. 'Leave Management', 'IT Services'
    version        VARCHAR(20),
    last_updated   DATE,
    search_tags    TEXT,                    -- comma-separated, used for search
    badge_text     VARCHAR(50),             -- e.g. 'New', 'Updated'
    badge_color    VARCHAR(50),             -- e.g. 'green', 'orange'
    component_name VARCHAR(255)             -- which React component renders it
)

-- one row per time someone submits a form
form_submissions (
    submission_id   SERIAL PRIMARY KEY,
    form_id         INTEGER REFERENCES forms(form_id),
    user_id         INTEGER REFERENCES users(user_id),
    status          VARCHAR(50) DEFAULT 'submitted',
    submission_data JSONB NOT NULL,         -- whatever the form fields were, as JSON
    submitted_at    TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
)

-- inventory of licenses, hardware, whatever IT tracks
it_assets (
    asset_id     SERIAL PRIMARY KEY,
    asset_name   VARCHAR(255) NOT NULL,
    asset_type   VARCHAR(100) NOT NULL,
    license_key  VARCHAR(255),
    purchase_date DATE,
    expiry_date   DATE,
    status        VARCHAR(50) DEFAULT 'Available'  -- 'Available' or 'Assigned'
)

-- who has what, and when they got it / gave it back
asset_allocations (
    allocation_id  SERIAL PRIMARY KEY,
    asset_id       INTEGER REFERENCES it_assets(asset_id),
    user_id        INTEGER REFERENCES users(user_id),
    assigned_date  TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    returned_date  TIMESTAMP WITH TIME ZONE NULL,   -- still null = still checked out
    is_active      BOOLEAN DEFAULT true
)

-- employee complaints - keep these confidential
grievances (
    grievance_id      SERIAL PRIMARY KEY,
    user_id           INTEGER REFERENCES users(user_id),
    grievance_subject VARCHAR(255) NOT NULL,
    grievance_details TEXT NOT NULL,
    status            VARCHAR(50) DEFAULT 'submitted',
    submitted_at      TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
)

-- everything below just feeds the homepage / info pages
projects       ( project_id, project_name, description, status )
events         ( event_id, day, month, title, description, event_date )
publications   ( publication_id, type, title, meta, description, pdf_link )
announcements  ( announcement_id, date, title, description )
key_moments    ( image_id, image_url, alt_text )
```
---
## Architecture Diagram

```mermaid
flowchart TB
    subgraph Browser["Browser (Client)"]
        direction TB
        subgraph SPA["React SPA (Vite)"]
            direction TB
            Auth["AuthProvider (React Context)<br/>- Stores user object + JWT in memory<br/>- Reads/writes token to localStorage"]
            Router["React Router<br/>(client-side navigation)"]
            Socket["Socket.io Client (useSocket.js)<br/>- Persistent WS connection<br/>- chat-message, user-join, etc"]
        end
    end

    Axios["HTTP (Axios)"]
    WS["WebSocket (WS)"]

    subgraph Server["Node.js / Express Server"]
        direction TB
        REST["REST API (Express Router)<br/>/api/auth  /api/forms  /api/assets<br/>/api/grievances  /api/users  /api/ai ..."]

        subgraph MW["Middleware"]
            direction LR
            AuthMW["authMiddleware<br/>(JWT verify)"]
            AdminMW["adminMiddleware<br/>(role check)"]
        end

        SocketServer["Socket.io Server<br/>- connectedUsers Map<br/>- chatHistory array (100)<br/>- Emits: data-updated,<br/>new-message, users-online"]

        Controllers["Controllers (Business Logic)<br/>authController  assetController  aiController<br/>formsController  grievanceController ..."]
    end

    DB[("PostgreSQL (pg)<br/>Connection Pool<br/>- 11 tables")]
    HF["HuggingFace API<br/>(Llama 3 8B Instruct)<br/>/v1/chat/completions"]

    Router --> Axios
    Socket --> WS
    Axios --> REST
    WS --> SocketServer
    REST --> AuthMW
    REST --> AdminMW
    AuthMW --> Controllers
    AdminMW --> Controllers
    SocketServer --> Controllers
    Controllers --> DB
    Controllers --> HF
```

---
## Flows
### 1. Authentication – Register & Login

```mermaid
sequenceDiagram
    actor User
    participant Client
    participant AuthMiddleware
    participant AuthController
    participant DB

    User->>Client: Fill registration form
    Client->>AuthController: POST /api/auth/register
    AuthController->>DB: SELECT user by email
    DB-->>AuthController: Result
    alt Email already exists
        AuthController-->>Client: 400 User already exists
    else New user
        AuthController->>AuthController: bcrypt.hash(password)
        AuthController->>DB: INSERT new user
        DB-->>AuthController: user_id, email, role
        AuthController->>AuthController: jwt.sign(payload, 5h)
        AuthController-->>Client: 201 { token }
        Client-->>User: Redirect to Home
    end

    User->>Client: Fill login form
    Client->>AuthController: POST /api/auth/login
    AuthController->>DB: SELECT user by email
    DB-->>AuthController: User row
    alt User not found
        AuthController-->>Client: 400 Invalid credentials
    else
        AuthController->>AuthController: bcrypt.compare(password, hash)
        alt Password mismatch
            AuthController-->>Client: 400 Invalid credentials
        else Match
            AuthController->>AuthController: jwt.sign(payload, 5h)
            AuthController-->>Client: 200 { token, user }
            Client->>Client: Store token in context
            Client-->>User: Redirect to Home
        end
    end
```

---

### 2. Route Protection (Auth & Admin Guards)

```mermaid
sequenceDiagram
    actor User
    participant Client
    participant ProtectedRoute
    participant AdminRoute
    participant AuthMiddleware
    participant AdminMiddleware
    participant Controller

    User->>Client: Navigate to protected page
    Client->>ProtectedRoute: Check AuthContext token
    alt No token
        ProtectedRoute-->>Client: Redirect to /login
    else Token present
        ProtectedRoute-->>Client: Render page
    end

    User->>Client: Navigate to /admin/* page
    Client->>AdminRoute: Check user.role
    alt Role != admin
        AdminRoute-->>Client: Redirect to /
    else Role == admin
        AdminRoute-->>Client: Render admin page
    end

    Client->>AuthMiddleware: API request with x-auth-token header
    AuthMiddleware->>AuthMiddleware: jwt.verify(token)
    alt Invalid / missing token
        AuthMiddleware-->>Client: 401 Unauthorized
    else Valid
        AuthMiddleware->>AdminMiddleware: next() (for admin routes)
        alt Role != admin
            AdminMiddleware-->>Client: 403 Forbidden
        else Admin
            AdminMiddleware->>Controller: next()
            Controller-->>Client: Response
        end
    end
```

---

### 3. Forms – Browse, Fill & Submit

```mermaid
sequenceDiagram
    actor Employee
    participant Client
    participant FormsController
    participant DB
    participant SocketIO

    Employee->>Client: Visit /forms
    Client->>FormsController: GET /api/forms
    FormsController->>DB: SELECT * FROM forms ORDER BY title
    DB-->>FormsController: Form list
    FormsController-->>Client: 200 [forms]
    Client-->>Employee: Display form cards

    Employee->>Client: Click a form → /forms/:formId
    Client->>FormsController: GET /api/forms/:id
    FormsController->>DB: SELECT form by form_id
    DB-->>FormsController: Form row
    FormsController-->>Client: 200 form
    Client-->>Employee: Render dynamic form fields

    Employee->>Client: Fill & submit form
    Client->>FormsController: POST /api/forms/submit { formId, submissionData }
    FormsController->>DB: INSERT INTO form_submissions
    DB-->>FormsController: OK
    FormsController->>SocketIO: emit('data-updated', { type: 'submissions' })
    FormsController-->>Client: 201 Form submitted successfully
    Client-->>Employee: Success message
```

---

### 4. Grievance – Submit & Admin Review

```mermaid
sequenceDiagram
    actor Employee
    actor Admin
    participant Client
    participant AIController
    participant GrievanceController
    participant DB
    participant SocketIO
    participant HuggingFace

    Employee->>Client: Fill grievance form (subject + details)
    Client->>AIController: POST /api/ai/analyze-grievance
    AIController->>HuggingFace: Chat completion (analysis prompt)
    HuggingFace-->>AIController: JSON { sentiment, severity, category, suggestedPriority, summary, suggestedAction }
    AIController-->>Client: 200 analysis
    Client-->>Employee: Show AI analysis preview

    Employee->>Client: Confirm & submit grievance
    Client->>GrievanceController: POST /api/grievances { subject, details }
    GrievanceController->>DB: INSERT INTO grievances (user_id, subject, details)
    DB-->>GrievanceController: OK
    GrievanceController->>SocketIO: emit('data-updated', { type: 'grievances' })
    GrievanceController-->>Client: 201 Submitted successfully
    Client-->>Employee: Confirmation

    Admin->>Client: Visit /admin/grievances
    Client->>GrievanceController: GET /api/grievances (admin)
    GrievanceController->>DB: SELECT all grievances ORDER BY submitted_at DESC
    DB-->>GrievanceController: Grievance list
    GrievanceController-->>Client: 200 [grievances]
    Client-->>Admin: Display grievance table
```

---

### 5. IT Asset Management

```mermaid
sequenceDiagram
    actor Employee
    actor Admin
    participant Client
    participant AssetController
    participant DB
    participant SocketIO

    Employee->>Client: Visit /assets
    Client->>AssetController: GET /api/assets/my-assets
    AssetController->>DB: SELECT assets WHERE user_id = req.user.id AND is_active = true
    DB-->>AssetController: Asset list
    AssetController-->>Client: 200 [assets]
    Client-->>Employee: Display assigned assets

    Admin->>Client: Visit /admin/assets
    Client->>AssetController: GET /api/assets (all)
    AssetController->>DB: SELECT all assets with allocation + user info
    DB-->>AssetController: Full asset list
    AssetController-->>Client: 200 [assets]
    Client-->>Admin: Display asset management table

    Admin->>Client: Add new asset (with optional email)
    Client->>AssetController: POST /api/assets { asset_name, asset_type, email, ... }
    AssetController->>DB: INSERT INTO it_assets
    DB-->>AssetController: Created asset
    alt Email provided
        AssetController->>DB: SELECT user by email
        DB-->>AssetController: user_id
        AssetController->>DB: INSERT INTO asset_allocations
        AssetController->>DB: UPDATE it_assets SET status = Assigned
    end
    AssetController->>SocketIO: emit('data-updated', { type: 'assets' })
    AssetController-->>Client: 201 asset

    Admin->>Client: Assign existing asset
    Client->>AssetController: POST /api/assets/assign { assetId, email }
    AssetController->>DB: SELECT user by email
    DB-->>AssetController: user_id
    AssetController->>DB: INSERT INTO asset_allocations (is_active = true)
    AssetController->>DB: UPDATE it_assets SET status = Assigned
    AssetController->>SocketIO: emit('data-updated', { type: 'assets' })
    AssetController-->>Client: 200 Assigned

    Admin->>Client: Unassign asset
    Client->>AssetController: POST /api/assets/unassign { assetId }
    AssetController->>DB: UPDATE asset_allocations SET is_active = false, returned_date = NOW()
    AssetController->>DB: UPDATE it_assets SET status = Available
    AssetController->>SocketIO: emit('data-updated', { type: 'assets' })
    AssetController-->>Client: 200 Unassigned

    Admin->>Client: Delete asset
    Client->>AssetController: DELETE /api/assets/:id
    AssetController->>DB: DELETE FROM asset_allocations WHERE asset_id
    AssetController->>DB: DELETE FROM it_assets WHERE asset_id
    AssetController->>SocketIO: emit('data-updated', { type: 'assets' })
    AssetController-->>Client: 200 Asset removed
```

---

### 6. User Management (Admin)

```mermaid
sequenceDiagram
    actor Admin
    participant Client
    participant UserController
    participant DB

    Admin->>Client: Visit /admin/users
    Client->>UserController: GET /api/users
    UserController->>DB: SELECT all users ORDER BY last_name
    DB-->>UserController: User list
    UserController-->>Client: 200 [users]
    Client-->>Admin: User management table

    Admin->>Client: Add new user
    Client->>UserController: POST /api/users { first_name, last_name, email, password, role, ... }
    UserController->>DB: SELECT user by email
    alt Email exists
        UserController-->>Client: 400 User already exists
    else
        UserController->>UserController: bcrypt.hash(password)
        UserController->>DB: INSERT INTO users
        DB-->>UserController: New user row
        UserController-->>Client: 201 user
    end

    Admin->>Client: Update user
    Client->>UserController: PUT /api/users/:id { first_name, last_name, job_title, department }
    UserController->>DB: UPDATE users WHERE user_id = id
    DB-->>UserController: Updated user
    UserController-->>Client: 200 updated user

    Admin->>Client: Delete user
    Client->>UserController: DELETE /api/users/:id
    alt Deleting own account
        UserController-->>Client: 400 Cannot delete own account
    else
        UserController->>DB: DELETE FROM users WHERE user_id = id
        DB-->>UserController: OK
        UserController-->>Client: 200 User removed
    end
```

---

### 7. AI Assistant Chat

```mermaid
sequenceDiagram
    actor Employee
    participant Client
    participant AIController
    participant DB
    participant HuggingFace

    Employee->>Client: Open /ai-assistant
    Employee->>Client: Type a message & send

    Client->>AIController: POST /api/ai/chat { message, conversationHistory }

    AIController->>DB: Parallel context queries
    DB-->>AIController: forms, announcements, events, publications

    AIController->>AIController: Build system prompt with DB context
    AIController->>HuggingFace: POST /v1/chat/completions (Llama-3-8B)
    alt Model loading (503)
        HuggingFace-->>AIController: Error: model warming up
        AIController-->>Client: 500 Try again in 20s
    else OK
        HuggingFace-->>AIController: assistant message
        AIController-->>Client: 200 { response }
        Client-->>Employee: Display AI reply
    end

    Note over Client,HuggingFace: Streaming variant (POST /api/ai/chat-stream)
    Client->>AIController: POST /api/ai/chat-stream
    AIController->>HuggingFace: Chat completion
    HuggingFace-->>AIController: Full response text
    loop Word by word (30ms delay)
        AIController-->>Client: SSE data: { content }
    end
    AIController-->>Client: SSE data: [DONE]
    Client-->>Employee: Streamed text appears progressively
```

---

### 8. Real-Time Group Chat (Socket.IO)

```mermaid
sequenceDiagram
    actor UserA
    actor UserB
    participant Client_A
    participant Client_B
    participant SocketIO_Server

    UserA->>Client_A: Open /chat page
    Client_A->>SocketIO_Server: socket.connect()
    Client_A->>SocketIO_Server: emit('user-join', { user_id, first_name, last_name, role })
    SocketIO_Server-->>Client_A: emit('chat-history', [last 100 msgs])
    SocketIO_Server->>Client_A: emit('users-online', [all users])
    SocketIO_Server->>Client_B: emit('users-online', [all users])

    UserB->>Client_B: Open /chat page
    Client_B->>SocketIO_Server: socket.connect()
    Client_B->>SocketIO_Server: emit('user-join', userData)
    SocketIO_Server-->>Client_B: emit('chat-history', [...])
    SocketIO_Server->>Client_A: emit('users-online', [updated])
    SocketIO_Server->>Client_B: emit('users-online', [updated])

    UserA->>Client_A: Start typing
    Client_A->>SocketIO_Server: emit('typing', true)
    SocketIO_Server->>Client_B: broadcast emit('user-typing', { user, isTyping: true })
    Client_B-->>UserB: Show typing indicator

    UserA->>Client_A: Send message
    Client_A->>SocketIO_Server: emit('chat-message', { content })
    SocketIO_Server->>SocketIO_Server: Build message object, push to chatHistory
    SocketIO_Server->>Client_A: emit('new-message', message)
    SocketIO_Server->>Client_B: emit('new-message', message)
    Client_A-->>UserA: Display message
    Client_B-->>UserB: Display message

    UserA->>Client_A: Close tab / disconnect
    SocketIO_Server->>SocketIO_Server: Remove from connectedUsers
    SocketIO_Server->>Client_B: emit('users-online', [updated])
```

---

### 9. Admin Dashboard Insights (AI)

```mermaid
sequenceDiagram
    actor Admin
    participant Client
    participant AIController
    participant DB
    participant HuggingFace

    Admin->>Client: Open /admin/dashboard
    Client->>AIController: GET /api/ai/dashboard-insights
    AIController->>DB: COUNT form_submissions (last 7 days, grouped by status)
    AIController->>DB: COUNT grievances (last 7 days, grouped by status)
    AIController->>DB: COUNT total announcements
    DB-->>AIController: Aggregated stats
    AIController->>HuggingFace: Generate 2-3 sentence professional summary
    HuggingFace-->>AIController: Insight text
    AIController-->>Client: 200 { data, insight }
    Client-->>Admin: Render stats + AI narrative summary
```

---

### 10. Admin Submissions Review

```mermaid
sequenceDiagram
    actor Admin
    participant Client
    participant FormsController
    participant DB

    Admin->>Client: Visit /admin/submissions
    Client->>FormsController: GET /api/forms/submissions
    FormsController->>DB: SELECT submissions JOIN users JOIN forms ORDER BY submitted_at DESC
    DB-->>FormsController: Submission rows with user & form details
    FormsController-->>Client: 200 [submissions]
    Client-->>Admin: Display submissions table with SubmissionDataViewer
```

---

### 11. Profile Management

```mermaid
sequenceDiagram
    actor Employee
    participant Client
    participant AuthController
    participant UserController
    participant DB

    Employee->>Client: Visit /profile
    Client->>AuthController: GET /api/auth/user (x-auth-token)
    AuthController->>DB: SELECT user fields WHERE user_id = req.user.id
    DB-->>AuthController: User row
    AuthController-->>Client: 200 user object
    Client-->>Employee: Display profile info

    Employee->>Client: Edit profile fields & save
    Client->>UserController: PUT /api/users/:id { first_name, last_name, job_title, department }
    UserController->>DB: UPDATE users WHERE user_id = id
    DB-->>UserController: Updated row
    UserController-->>Client: 200 updated user
    Client-->>Employee: Show updated profile
```

---
## Getting Started

### Prerequisites

* Node.js (v16 or higher)
* npm or yarn
* PostgreSQL database (local or hosted)

### Installation

1.  **Clone the repository**
    ```bash
    git clone https://github.com/your-username/ess-portal.git
    cd ess-portal
    ```

2.  **Install dependencies**
    ```bash
    # Install client dependencies
    cd client && npm install

    # Install server dependencies
    cd ../server && npm install
    ```

3.  **Configure Environment Variables**

    **Server (`server/.env`):**
    ```env
    NODE_ENV=development
    DATABASE_URL=postgresql://username:password@localhost:5432/ess_portal
    JWT_SECRET=your_jwt_secret_key
    CLIENT_ORIGIN=http://localhost:5173
    HF_API_KEY=your_huggingface_api_key
    ```

    **Client (`client/.env`):**
    ```env
    VITE_API_URL=http://localhost:5000
    ```

4.  **Start the development servers**
    ```bash
    # Start the backend server (from server directory)
    npm start

    # Start the frontend (from client directory, in a new terminal)
    npm run dev
    ```

### Deployment Configuration

**Vercel (Frontend):**
- Set the project root directory to `client`
- Add environment variable:
  - `VITE_API_URL` = Your Render backend URL (e.g., `https://your-app.onrender.com`)

**Render (Backend):**
- Set the root directory to `server`
- Add environment variables:
  - `NODE_ENV` = `production`
  - `DATABASE_URL` = Your PostgreSQL connection string(transaction pooler)
  - `JWT_SECRET` = Your secure JWT secret
  - `CLIENT_ORIGIN` = Your Vercel frontend URL (e.g., `https://your-app.vercel.app`)
  - `HF_API_KEY` = Your Hugging Face API key for AI features

> **Note:** Password hashing can be done using `hashGenerator.js` in the server directory. All seeded users have the password "password".

### Database Setup

To make the app function, you must populate your Supabase database with the required tables and sample data.

1.  Go to your Supabase Dashboard.
2.  Navigate to the **SQL Editor**.
3.  Copy and paste the schema and seed data below to create users, assets, forms, and events.

<details>
<summary><strong>Click here to view the SQL Seed Script</strong></summary>

```sql
-- 1. Users (every user has password "password". Use bycrypt.js to generate a hash for a different password)
INSERT INTO users (user_id, email, password_hash, first_name, last_name, role, job_title, department) VALUES
(1, 'admin@drdo.gov.in', '$2b$10$pqVbOXDc4C3z7QtRbVMmc.PiulNIUs4R2ZEenqMOZkCTFgAX8YNCK', 'Admin', 'User', 'admin', 'System Administrator', 'IT Department'),
(2, 'john.doe@drdo.gov.in', '$2b$10$pqVbOXDc4C3z7QtRbVMmc.PiulNIUs4R2ZEenqMOZkCTFgAX8YNCK', 'John', 'Doe', 'employee', 'Senior Scientist', 'Research & Development'),
(3, 'priya.sharma@drdo.gov.in', '$2b$10$pqVbOXDc4C3z7QtRbVMmc.PiulNIUs4R2ZEenqMOZkCTFgAX8YNCK', 'Priya', 'Sharma', 'employee', 'Research Engineer', 'Electronics Division'),
(4, 'amit.kumar@drdo.gov.in', '$2b$10$pqVbOXDc4C3z7QtRbVMmc.PiulNIUs4R2ZEenqMOZkCTFgAX8YNCK', 'Amit', 'Kumar', 'employee', 'Project Lead', 'Defence Systems'),
(5, 'sneha.patel@drdo.gov.in', '$2b$10$pqVbOXDc4C3z7QtRbVMmc.PiulNIUs4R2ZEenqMOZkCTFgAX8YNCK', 'Sneha', 'Patel', 'employee', 'Software Developer', 'IT Department');

-- 2. Forms Configuration
INSERT INTO forms (form_id, title, description, category, version, last_updated, search_tags, badge_text, badge_color, component_name) VALUES
(1, 'Annual Leave Request', 'Request for annual/earned leave', 'Leave Management', '1.0', '2025-12-01', 'leave vacation time-off annual', 'Popular', 'green', 'AnnualLeaveRequest'),
(2, 'IT Support Request', 'Submit IT support tickets and issues', 'IT Services', '2.1', '2025-11-15', 'IT support help desk technical', 'New', 'blue', 'ITSupportRequestForm'),
(3, 'Travel Reimbursement', 'Claim travel expenses and reimbursements', 'Finance', '1.5', '2025-10-20', 'travel expense reimbursement claim', NULL, NULL, 'TravelReimbursementForm'),
(4, 'Equipment Request', 'Request new office equipment or supplies', 'Procurement', '1.0', '2025-09-10', 'equipment supplies office', NULL, NULL, NULL),
(5, 'Training Request', 'Apply for training programs and workshops', 'HR', '1.2', '2025-11-01', 'training learning development workshop', 'Updated', 'orange', NULL);

-- 3. IT Assets Inventory
INSERT INTO it_assets (asset_id, asset_name, asset_type, license_key, purchase_date, expiry_date, status) VALUES
(1, 'Dell Latitude 5520', 'Laptop', NULL, '2024-01-15', NULL, 'Allocated'),
(2, 'Dell Latitude 5520', 'Laptop', NULL, '2024-01-15', NULL, 'Available'),
(3, 'HP LaserJet Pro M404dn', 'Printer', NULL, '2023-06-20', NULL, 'Allocated'),
(4, 'Microsoft Office 365', 'Software License', 'XXXXX-XXXXX-XXXXX-XXXXX-XXXXX', '2025-01-01', '2026-01-01', 'Allocated'),
(5, 'Adobe Creative Cloud', 'Software License', 'YYYYY-YYYYY-YYYYY-YYYYY-YYYYY', '2025-03-01', '2026-03-01', 'Available'),
(6, 'Cisco IP Phone 8845', 'Phone', NULL, '2023-09-10', NULL, 'Allocated'),
(7, 'Dell UltraSharp U2722D', 'Monitor', NULL, '2024-02-20', NULL, 'Available'),
(8, 'Logitech MX Keys', 'Keyboard', NULL, '2024-05-15', NULL, 'Available');

-- 4. Projects
INSERT INTO projects (project_name, description, status) VALUES
('Project Agni', 'Development of advanced ballistic missile systems', 'Active'),
('Project Akash', 'Surface-to-air missile defense system development', 'Active'),
('Autonomous Vehicle Research', 'Research on autonomous navigation systems for defence vehicles', 'In Progress'),
('Cyber Security Initiative', 'Development of advanced cyber security frameworks', 'Active'),
('Radar Systems Upgrade', 'Modernization of existing radar infrastructure', 'Completed');

-- 5. Events
INSERT INTO events (day, month, title, description, event_date) VALUES
('15', 'Feb', 'Annual Science Day', 'Celebration of National Science Day with exhibitions and lectures', '2026-02-15'),
('26', 'Jan', 'Republic Day Celebration', 'Annual Republic Day celebration at DRDO headquarters', '2026-01-26'),
('10', 'Mar', 'Tech Innovation Summit', 'Annual technology innovation summit showcasing new developments', '2026-03-10'),
('05', 'Apr', 'Cybersecurity Workshop', 'Workshop on latest cybersecurity threats and countermeasures', '2026-04-05'),
('20', 'Feb', 'Research Paper Presentation', 'Internal research paper presentation by scientists', '2026-02-20');

-- 6. Publications
INSERT INTO publications (type, title, meta, description, pdf_link) VALUES
('Research Paper', 'Advances in Radar Signal Processing', 'Dr. A. Kumar et al. | 2025', 'A comprehensive study on modern radar signal processing techniques and their applications in defence systems.', '/publications/radar-signal-processing.pdf'),
('Technical Report', 'Autonomous Systems Integration Framework', 'Defence Research Team | 2025', 'Framework for integrating autonomous systems in military applications.', '/publications/autonomous-framework.pdf'),
('Journal Article', 'Machine Learning in Defence Applications', 'Dr. P. Sharma | 2024', 'Survey of machine learning techniques applicable to defence sector.', '/publications/ml-defence.pdf'),
('White Paper', 'Cybersecurity Best Practices', 'IT Security Division | 2025', 'Guidelines and best practices for maintaining cybersecurity in defence organizations.', '/publications/cybersecurity-guide.pdf');

-- 7. Announcements
INSERT INTO announcements (date, title, description) VALUES
('January 30, 2026', 'System Maintenance Notice', 'The ESS Portal will undergo scheduled maintenance on February 1st from 10 PM to 2 AM.'),
('January 25, 2026', 'New Leave Policy Update', 'Please review the updated leave policy effective from February 1, 2026.'),
('January 20, 2026', 'IT Security Training', 'Mandatory IT security awareness training scheduled for all employees next week.'),
('January 15, 2026', 'Annual Performance Review', 'Annual performance review cycle begins on February 1st. Please update your goals.');

-- 8. Key Moments
INSERT INTO key_moments (image_url, alt_text) VALUES
('1.jpg', 'Scientists working in laboratory'),
('2.jpg', 'Defence technology exhibition'),
('3.png', 'Research team meeting');

-- 9. Asset Allocations
INSERT INTO asset_allocations (asset_id, user_id, assigned_date, returned_date, is_active) VALUES
(1, 2, '2024-01-20', NULL, true),
(3, 3, '2023-07-01', NULL, true),
(4, 2, '2025-01-05', NULL, true),
(4, 4, '2025-01-05', NULL, true),
(6, 4, '2023-09-15', NULL, true);

-- 10. Grievances
INSERT INTO grievances (user_id, grievance_subject, grievance_details, status, submitted_at) VALUES
(2, 'Office AC Not Working', 'The air conditioning unit in Room 204 has not been functioning properly for the past week.', 'submitted', '2026-01-28 10:30:00+05:30'),
(3, 'Parking Space Issue', 'Requested parking space has been allocated to another employee without prior notice.', 'in_progress', '2026-01-25 14:15:00+05:30'),
(4, 'Software License Delay', 'Waiting for Adobe Creative Cloud license for over 3 weeks now.', 'resolved', '2026-01-15 09:00:00+05:30');

-- 11. Form Submissions
INSERT INTO form_submissions (form_id, user_id, status, submission_data, submitted_at) VALUES
(1, 2, 'approved', '{"leave_type": "Annual Leave", "start_date": "2026-02-10", "end_date": "2026-02-14", "reason": "Family vacation", "days": 5}', '2026-01-20 11:00:00+05:30'),
(2, 3, 'submitted', '{"issue_type": "Hardware", "description": "Laptop keyboard not working", "priority": "Medium"}', '2026-01-28 15:30:00+05:30'),
(3, 4, 'pending', '{"trip_date": "2026-01-10", "destination": "Mumbai", "purpose": "Conference", "amount": 15000}', '2026-01-25 10:00:00+05:30');
```

