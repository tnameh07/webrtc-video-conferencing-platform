# 🌐 WebRTC Video Conferencing & Meeting Planner Platform

A full-stack, real-time video conferencing application featuring multi-peer WebRTC streaming, high-speed RTC DataChannel-based text chat, user authentication, and a meeting scheduling dashboard.

This repository is organized into a modular design containing:
- **`backend`**: Node.js & Express REST API powered by MongoDB for secure user registration, logins, and meeting management.
- **`webrtcpart3`**: React frontend client coupled with an integrated Socket.io Express signaling server.

---

## 🏗️ Architecture & Communication Flow

The application relies on a decoupled architecture where WebRTC handles the high-bandwidth media streams directly between peers, Socket.io coordinates connection handshakes (signaling), and MongoDB manages persistent user states.

```mermaid
graph TD
    subgraph Client App (React)
        A[Meeting Dashboard] -->|Join Room| B[WebRTC Room Component]
        B -->|P2P Media Stream| C[Peer Clients]
        B -->|RTCDataChannel Chat| C
    end

    subgraph Signaling Server (Socket.io)
        D[Express Socket Server] <-->|ICE & SDP Handshake| B
    end

    subgraph Database Backend (REST API)
        E[Express REST Server] -->|MongoDB Atlas| F[(Mongoose Database)]
        A -->|Auth & Schedule APIs| E
    end
```

---

## ✨ Features

- **P2P Video & Audio**: Multi-party real-time conferencing powered by standard WebRTC APIs.
- **Instant Chat**: Zero-latency chat via `RTCDataChannel`, with a Socket.io backup database connection for message synchronization.
- **Screen Sharing**: Live display-media sharing with on-the-fly track replacement in all peer connections.
- **User Authentication**: Secure passwords hashed via `bcrypt` and stateless session verification using JWT.
- **Meeting Planner**: Interactive scheduling dashboard supporting participant email validation and room UUID creation.
- **Draggable Local Preview**: Resizable, floating, and draggable local stream window allowing users to reposition their preview anywhere on screen.

---

## 🛠️ Tech Stack

### Frontend & Signaling Client (`webrtcpart3`)
- **Core**: React (v18), React Router (v6)
- **Styling**: Bootstrap & React-Bootstrap
- **Networking**: Socket.io Client (v4), Axios
- **WebRTC APIs**: `RTCPeerConnection`, `RTCDataChannel`, `getUserMedia`, `getDisplayMedia`

### API REST Services (`backend`)
- **Environment**: Node.js & Express
- **Database**: MongoDB & Mongoose
- **Security**: JWT (JsonWebTokens) & `bcryptjs`
- **Tooling**: Nodemon

---

## 📁 Repository Structure

```
webrtc-video-conferencing-platform/
├── backend/                  # Mongoose DB and REST Auth/Meeting services
│   ├── Controller/           # Request handlers for auth and scheduling
│   ├── db/                   # Database connection helper
│   ├── middilware/           # JWT auth token validator
│   ├── model/                # MongoDB Mongoose Schemas (User, Meeting)
│   ├── Routes/               # Express endpoints router maps
│   └── index.js              # REST service main entrypoint
├── webrtcpart3/              # React frontend client & Socket.io signaling
│   ├── public/               # Public assets
│   ├── src/                  # React Application source code
│   │   ├── components/       # Reusable components (Video feeds, Chat panels)
│   │   ├── providers/        # Context states (Auth, WebRTC skeleton)
│   │   ├── userinterface/    # Page components (Dashboard, Login, Room)
│   │   └── App.js            # Client router configs
│   ├── server.js             # Socket.io signaling server & static build host
│   └── package.json          # Frontend packages
└── README.md                 # Main workspace documentation
```

---

## 🚀 Getting Started & Installation

Follow these steps to run the application on your local machine:

### 1. Prerequisites
- **Node.js** (v16.x or higher)
- **npm** (v8.x or higher)
- **MongoDB Instance** (Local MongoDB Community Server or MongoDB Atlas Cloud Database)

---

### 2. Setting Up the Backend API

1. Navigate to the backend directory:
   ```bash
   cd backend
   ```
2. Install the backend dependencies:
   ```bash
   npm install
   ```
3. Create a `.env` file in the root of the `backend/` directory:
   ```env
   port=3001
   URL=your_mongodb_connection_uri
   JWT_SECRET=your_jwt_signing_key_here
   ```
4. Start the backend REST service in development mode:
   ```bash
   npm start
   # or run nodemon index.js
   ```
   The backend server will run on [http://localhost:3001](http://localhost:3001).

---

### 3. Setting Up the Frontend & Signaling Server

1. Navigate to the frontend directory:
   ```bash
   cd webrtcpart3
   ```
2. Install the frontend dependencies:
   ```bash
   npm install
   ```
3. Create a `.env` file in the root of the `webrtcpart3/` directory:
   ```env
   REACT_APP_API_URL=http://localhost:3001
   ```
4. Build the production package (compiles React code to static assets for deployment):
   ```bash
   npm run build
   ```
5. Start the Signaling server (which hosts the compiled static files and Socket.io channel):
   ```bash
   node server.js
   ```
   The application and signaling server will be online on [http://localhost:8080](http://localhost:8080).

---

## 👩‍💻 Recruiter & HR Demonstration Guide

To quickly test the application's capabilities, follow this scenario:

1. **User Sign Up & Login**:
   - Open [http://localhost:8080/ragistration](http://localhost:8080/ragistration) (or navigate through the Navbar) and create a user account.
   - Log in using your new credentials at [http://localhost:8080/login](http://localhost:8080/login) to obtain your JWT authorization token.
2. **Dashboard Overview**:
   - Once logged in, you will be redirected to the main dashboard.
   - Here, you can **Join a Meeting** directly by entering a Room ID (e.g., `test-room`) and a Display Name.
   - Alternatively, toggle the **Create Meeting** view to schedule a video conference. Enter a meeting title, set future start and end times, search and add registered participant emails, and click **Create Meeting** to log the scheduled link to the MongoDB database.
3. **Entering a Conference Room**:
   - Enter a Room ID and username, then click **Join Meeting**. This opens the WebRTC Room view at `/room/<room_id>?username=<name>`.
   - The browser will request camera permissions. Approve it to render your local media stream in the floating draggable window.
4. **Multi-Peer Testing**:
   - Open a separate browser tab (or an Incognito window) and navigate to the same URL (e.g. `http://localhost:8080/room/test-room?username=Collaborator`).
   - The signaling server will automatically pair the users. You will see both video streams render on-screen in real time!
5. **Interactive Controls**:
   - **Chat**: Use the sliding chat sidebar to send messages. Communication is sent directly through zero-latency WebRTC `RTCDataChannels`.
   - **Screen Sharing**: Click **Start Screen Share** in either client. The video feed will switch to capture your window/display and stream it to the remote peer.
   - **Mute**: Hover over your floating local video panel and click the mic or video icons to toggle camera/audio settings.
   - **Disconnect**: Click **End Call** to leave the conference. Socket connections will close and peer connection resources will be cleaned up.

---

## 🔗 Deep-Dive into Core Source Files

- **Authentication Logic**: [UserAuthController.js](./backend/Controller/UserAuthController.js) handles registration and JWT token signing.
- **REST Endpoints**: [user.router.js](./backend/Routes/user.router.js) and [Meetings.routes.js](./backend/Routes/Meetings.routes.js) register all database APIs.
- **WebRTC Handshake Coordinator**: [server.js](./webrtcpart3/server.js) maps the Socket.io signalling routes `/webrtcPeer` for room creation and SDP/ICE distribution.
- **WebRTC Peer Connection Management**: [Room.js](./webrtcpart3/src/userinterface/Room.js) manages `RTCPeerConnection` configuration, track assignment, chat channels, and screen-sharing swaps.
- **Conference Layout rendering**: [videos.js](./webrtcpart3/src/components/videos.js) dynamically structures the remote video feeds grid layout.
- **UI Components**: [MeetingComponent .js](./webrtcpart3/src/userinterface/MeetingComponent%20.js) sets up the creation and joining dashboard.
