# ChatNest 🪺

A real-time room-based chat platform built with the MERN stack, featuring Google OAuth authentication, dynamic room creation, and WebSocket-powered instant messaging.

## Features

- 🔐 **Google OAuth Authentication** - Secure sign-in with Google accounts
- 🏠 **Room Creation** - Create chat rooms with unique 6-character codes
- 🔗 **Easy Sharing** - Share room codes for instant access
- 💬 **Real-time Chat** - WebSocket-powered instant messaging
- 👥 **Room Management** - Creators can remove users, anyone can leave
- 🎨 **Modern UI** - Clean, responsive interface

## Tech Stack

- **Frontend**: React.js, Socket.io-client, Axios
- **Backend**: Node.js, Express.js, Socket.io
- **Database**: MongoDB
- **Authentication**: Google OAuth 2.0
- **Real-time**: WebSocket (Socket.io)

## Prerequisites

- Node.js (v14 or higher)
- MongoDB (local or Atlas)
- Google Cloud Console account for OAuth credentials

---

## Quick Setup (Automated)

```bash
# Create project directory
mkdir chatnest && cd chatnest

# Copy all project files from artifacts to their respective locations
# Then run the setup script:

chmod +x setup.sh
./setup.sh
```

The setup script will automatically install all dependencies and create configuration files.

## Manual Project Initialization

### 1. Clone and Setup

```bash
# Create project directory
mkdir chatnest
cd chatnest

# Initialize backend
mkdir backend
cd backend
npm init -y

# Install backend dependencies
npm install express mongoose socket.io cors dotenv jsonwebtoken bcryptjs passport passport-google-oauth20 express-session

# Install backend dev dependencies
npm install --save-dev nodemon

cd ..

# Initialize frontend
npx create-react-app frontend
cd frontend

# Install frontend dependencies
npm install axios socket.io-client react-router-dom

cd ..
```

### 2. MongoDB Setup

**Option A: Local MongoDB**
```bash
# Install MongoDB Community Edition
# Start MongoDB service
mongod --dbpath /path/to/data/directory
```

**Option B: MongoDB Atlas (Recommended)**
1. Go to [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)
2. Create a free cluster
3. Get your connection string
4. Whitelist your IP address

### 3. Google OAuth Setup

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select existing
3. Enable **Google+ API**
4. Go to **Credentials** → **Create Credentials** → **OAuth Client ID**
5. Configure OAuth consent screen
6. Create OAuth 2.0 Client ID:
   - Application type: Web application
   - Authorized redirect URIs: 
     - `http://localhost:5000/auth/google/callback` (development)
     - `https://your-backend-url/auth/google/callback` (production)
7. Copy **Client ID** and **Client Secret**

### 4. Environment Variables

Create `.env` file in backend directory:

```env
PORT=5000
MONGO_URI=your_mongodb_connection_string
JWT_SECRET=your_super_secret_jwt_key_change_this_in_production
GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret
CLIENT_URL=http://localhost:3000
SESSION_SECRET=your_session_secret_key
```

Create `.env` file in frontend directory:

```env
REACT_APP_API_URL=http://localhost:5000
REACT_APP_SOCKET_URL=http://localhost:5000
```

---

## Project Structure

```
chatnest/
├── backend/
│   ├── config/
│   │   └── passport.js
│   ├── models/
│   │   ├── User.js
│   │   ├── Room.js
│   │   └── Message.js
│   ├── routes/
│   │   ├── auth.js
│   │   ├── rooms.js
│   │   └── messages.js
│   ├── middleware/
│   │   └── auth.js
│   ├── utils/
│   │   └── generateRoomCode.js
│   ├── server.js
│   ├── package.json
│   └── .env
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   │   ├── Home.js
│   │   │   ├── Room.js
│   │   │   ├── CreateRoom.js
│   │   │   └── JoinRoom.js
│   │   ├── context/
│   │   │   └── AuthContext.js
│   │   ├── App.js
│   │   ├── App.css
│   │   └── index.js
│   ├── package.json
│   └── .env
└── README.md
```

---

## Running the Application

### Development Mode

**Terminal 1 - Backend:**
```bash
cd backend
npm run dev
```

**Terminal 2 - Frontend:**
```bash
cd frontend
npm start
```

Access the application at `http://localhost:3000`

### Production Build

**Backend:**
```bash
cd backend
npm start
```

**Frontend:**
```bash
cd frontend
npm run build
```

---

## API Endpoints

### Authentication
- `GET /auth/google` - Initiate Google OAuth
- `GET /auth/google/callback` - OAuth callback
- `GET /auth/user` - Get current user
- `GET /auth/logout` - Logout user

### Rooms
- `POST /api/rooms/create` - Create a new room
- `GET /api/rooms/:roomCode` - Get room details
- `POST /api/rooms/:roomCode/join` - Join a room
- `POST /api/rooms/:roomCode/leave` - Leave a room
- `DELETE /api/rooms/:roomCode/remove/:userId` - Remove user from room
- `GET /api/rooms/user/all` - Get user's rooms

### Messages
- `GET /api/messages/:roomCode` - Get room messages
- `POST /api/messages/:roomCode` - Send message (via Socket.io)

### WebSocket Events
- `connection` - User connects
- `joinRoom` - User joins a room
- `sendMessage` - Send message to room
- `receiveMessage` - Receive message from room
- `userJoined` - Notification when user joins
- `userLeft` - Notification when user leaves
- `userRemoved` - Notification when user is removed
- `disconnect` - User disconnects

---

## Deployment Instructions

### Deploy to Vercel

#### Backend Deployment

1. **Prepare Backend for Vercel**

Create `vercel.json` in backend directory:
```json
{
  "version": 2,
  "builds": [
    {
      "src": "server.js",
      "use": "@vercel/node"
    }
  ],
  "routes": [
    {
      "src": "/(.*)",
      "dest": "server.js"
    }
  ]
}
```

2. **Install Vercel CLI**
```bash
npm install -g vercel
```

3. **Deploy Backend**
```bash
cd backend
vercel --prod
```

4. **Set Environment Variables in Vercel Dashboard**
   - Go to your Vercel project settings
   - Add all environment variables from `.env`
   - Update `CLIENT_URL` to your frontend URL

#### Frontend Deployment

1. **Update Frontend Environment Variables**

Update `.env` in frontend:
```env
REACT_APP_API_URL=https://your-backend-url.vercel.app
REACT_APP_SOCKET_URL=https://your-backend-url.vercel.app
```

2. **Deploy Frontend**
```bash
cd frontend
vercel --prod
```

3. **Update Google OAuth Settings**
   - Go to Google Cloud Console
   - Update authorized redirect URIs with production URL
   - Add: `https://your-backend-url.vercel.app/auth/google/callback`

4. **Update Backend Environment Variables**
   - Update `CLIENT_URL` in Vercel backend settings to your frontend URL

#### Important Notes for Vercel

⚠️ **WebSocket Limitations**: Vercel has limited WebSocket support. For production, consider:
- **Railway** (recommended for Socket.io)
- **Render**
- **Heroku**
- **DigitalOcean App Platform**

These platforms provide better WebSocket support for real-time features.

#### Alternative: Deploy Backend to Railway

1. Go to [Railway.app](https://railway.app/)
2. Create new project from GitHub repo
3. Add environment variables
4. Deploy (Railway handles WebSocket automatically)
5. Get deployment URL
6. Update frontend `REACT_APP_API_URL` and `REACT_APP_SOCKET_URL`

---

## Usage

1. **Sign In**: Click "Sign in with Google"
2. **Create Room**: Enter room title and click "Create Room"
3. **Share Code**: Copy the 6-character room code
4. **Join Room**: Others can enter the code to join
5. **Chat**: Send messages in real-time
6. **Manage**: 
   - Anyone can leave the room
   - Room creators can remove users
   - Creators see a "Remove" button next to each user

---

## Security Features

- JWT-based authentication
- Google OAuth 2.0 secure sign-in
- Protected API routes
- Session management
- CORS configuration
- Environment variable protection

---

## Future Enhancements

- [ ] Private/Public room options
- [ ] File sharing
- [ ] Message reactions
- [ ] User presence indicators
- [ ] Message search
- [ ] Room descriptions
- [ ] User profiles
- [ ] Message notifications
- [ ] Dark mode

---

## Troubleshooting

**Issue**: Google OAuth not working
- Check redirect URIs match exactly
- Verify Google+ API is enabled
- Check CLIENT_URL environment variable

**Issue**: WebSocket connection failed
- Verify SOCKET_URL matches backend URL
- Check CORS settings
- Ensure port 5000 is not blocked

**Issue**: MongoDB connection error
- Check MONGO_URI format
- Verify IP whitelist in Atlas
- Check database credentials

---

## License

MIT License - feel free to use this project for learning and development.

---

## Support

For issues and questions, please open an issue on the GitHub repository.

**Happy Chatting! 🎉**
