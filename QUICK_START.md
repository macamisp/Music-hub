# 🚀 Quick Start Guide - Music Hub

This guide will help you get started with building the Music Hub application **right now**!

---

## 📋 Pre-Implementation Checklist

Before writing any code, complete these essential setup steps:

### 1. ✅ Create Spotify Developer Account (5 minutes)

1. **Visit**: https://developer.spotify.com/dashboard
2. **Login** with your Spotify account (create one if needed)
3. **Click**: "Create an App"
4. **Fill in**:
   - App Name: `Music Hub`
   - App Description: `A modern music streaming web application`
   - Website: `http://localhost:5173` (for now)
   - Redirect URI: `http://localhost:5173/callback`
5. **Accept** terms and create
6. **Save** these credentials:
   ```
   Client ID: [COPY THIS]
   Client Secret: [COPY THIS - Click "Show Client Secret"]
   ```

### 2. ✅ Set Up MongoDB (5 minutes)

**Option A: MongoDB Atlas (Recommended - Free Cloud)**
1. Visit: https://www.mongodb.com/cloud/atlas/register
2. Create free account
3. Create a **FREE** M0 cluster
4. Create database user (username + password)
5. Add IP: `0.0.0.0/0` (allow from anywhere - for development)
6. Get connection string:
   ```
   mongodb+srv://<username>:<password>@cluster0.xxxxx.mongodb.net/music-hub?retryWrites=true&w=majority
   ```

**Option B: Local MongoDB**
```bash
# Install MongoDB locally
# Windows: Download from https://www.mongodb.com/try/download/community
# Mac: brew install mongodb-community
# Linux: sudo apt-get install mongodb

# Start MongoDB
mongod
```

### 3. ✅ Verify Node.js Installation

```bash
# Check Node.js version (should be 18+)
node --version

# Check npm version
npm --version

# If not installed, download from: https://nodejs.org/
```

---

## 🏗️ Project Initialization

### Step 1: Initialize Frontend (React + Vite)

```bash
# Navigate to your project directory
cd "d:/PORJECTS/MUsic hub"

# Create frontend with Vite
npm create vite@latest frontend -- --template react

# Navigate to frontend
cd frontend

# Install dependencies
npm install

# Install additional packages
npm install react-router-dom axios howler

# Install dev dependencies
npm install -D @vitejs/plugin-react
```

### Step 2: Initialize Backend (Node.js + Express)

```bash
# Navigate back to root
cd ..

# Create backend directory
mkdir backend
cd backend

# Initialize npm project
npm init -y

# Install production dependencies
npm install express cors dotenv axios mongoose jsonwebtoken bcryptjs cookie-parser

# Install dev dependencies
npm install -D nodemon
```

### Step 3: Create Environment Files

**Frontend** - Create `frontend/.env`:
```env
VITE_API_URL=http://localhost:5000/api
VITE_SPOTIFY_CLIENT_ID=your_client_id_here
VITE_REDIRECT_URI=http://localhost:5173/callback
```

**Backend** - Create `backend/.env`:
```env
PORT=5000
MONGODB_URI=your_mongodb_connection_string_here
JWT_SECRET=your_super_secret_random_string_here
SPOTIFY_CLIENT_ID=your_client_id_here
SPOTIFY_CLIENT_SECRET=your_client_secret_here
SPOTIFY_REDIRECT_URI=http://localhost:5173/callback
NODE_ENV=development
CORS_ORIGIN=http://localhost:5173
```

**Generate JWT Secret**:
```bash
# Run this in terminal to generate a random secret
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

### Step 4: Update package.json Scripts

**Backend** - Edit `backend/package.json`:
```json
{
  "name": "music-hub-backend",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "start": "node src/server.js",
    "dev": "nodemon src/server.js"
  }
}
```

---

## 🎯 First Code - Backend Setup

### Create Backend Structure

```bash
# From backend directory
mkdir -p src/config src/controllers src/models src/routes src/middleware src/services
```

### 1. Create `backend/src/server.js`

```javascript
import express from 'express';
import cors from 'cors';
import dotenv from 'dotenv';
import mongoose from 'mongoose';

dotenv.config();

const app = express();
const PORT = process.env.PORT || 5000;

// Middleware
app.use(cors({
  origin: process.env.CORS_ORIGIN || 'http://localhost:5173',
  credentials: true
}));
app.use(express.json());

// Test route
app.get('/api/health', (req, res) => {
  res.json({ status: 'OK', message: 'Music Hub API is running!' });
});

// MongoDB connection
mongoose.connect(process.env.MONGODB_URI)
  .then(() => {
    console.log('✅ Connected to MongoDB');
    app.listen(PORT, () => {
      console.log(`🚀 Server running on http://localhost:${PORT}`);
    });
  })
  .catch((error) => {
    console.error('❌ MongoDB connection error:', error);
  });
```

### 2. Test Backend

```bash
# Start backend
npm run dev

# You should see:
# ✅ Connected to MongoDB
# 🚀 Server running on http://localhost:5000

# Test in browser: http://localhost:5000/api/health
```

---

## 🎨 First Code - Frontend Setup

### 1. Update `frontend/src/App.jsx`

```javascript
import { useState, useEffect } from 'react';
import './App.css';

function App() {
  const [apiStatus, setApiStatus] = useState('Checking...');

  useEffect(() => {
    // Test API connection
    fetch('http://localhost:5000/api/health')
      .then(res => res.json())
      .then(data => setApiStatus(data.message))
      .catch(() => setApiStatus('API connection failed'));
  }, []);

  return (
    <div className="App">
      <header className="App-header">
        <h1>🎵 Music Hub</h1>
        <p>Your Personal Music Streaming Platform</p>
        <div className="status">
          <strong>API Status:</strong> {apiStatus}
        </div>
      </header>
    </div>
  );
}

export default App;
```

### 2. Update `frontend/src/App.css`

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  background: linear-gradient(135deg, #121212 0%, #1a1a1a 100%);
  color: #ffffff;
  min-height: 100vh;
}

.App {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}

.App-header {
  text-align: center;
  padding: 2rem;
  background: rgba(255, 255, 255, 0.05);
  backdrop-filter: blur(10px);
  border-radius: 20px;
  border: 1px solid rgba(255, 255, 255, 0.1);
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
  min-width: 400px;
}

h1 {
  font-size: 3rem;
  margin-bottom: 1rem;
  background: linear-gradient(135deg, #1DB954 0%, #1ed760 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

p {
  font-size: 1.2rem;
  color: #b3b3b3;
  margin-bottom: 2rem;
}

.status {
  padding: 1rem;
  background: rgba(29, 185, 84, 0.1);
  border-radius: 10px;
  border: 1px solid rgba(29, 185, 84, 0.3);
  margin-top: 2rem;
}

.status strong {
  color: #1DB954;
}
```

### 3. Test Frontend

```bash
# Start frontend (in frontend directory)
npm run dev

# Visit: http://localhost:5173
# You should see a beautiful landing page with API status!
```

---

## ✅ Verification Checklist

After completing the above steps, verify:

- [ ] Backend server starts without errors
- [ ] MongoDB connection successful
- [ ] Frontend loads at http://localhost:5173
- [ ] API health check shows "OK"
- [ ] No console errors in browser
- [ ] Spotify credentials saved
- [ ] Environment files created

---

## 🎯 Next Steps

Once everything is working:

1. **Read** `IMPLEMENTATION_PLAN.md` - Follow Phase 2 onwards
2. **Reference** `SPOTIFY_API_REFERENCE.md` - When integrating Spotify API
3. **Check** `TECH_STACK_AND_RESOURCES.md` - For additional resources

### Immediate Next Tasks:

1. **Create User Model** (`backend/src/models/User.js`)
2. **Set up Authentication Routes** (`backend/src/routes/auth.js`)
3. **Implement Spotify OAuth** (`backend/src/services/spotifyService.js`)
4. **Create Login Page** (`frontend/src/pages/Login.jsx`)
5. **Build Music Player Component** (`frontend/src/components/Player.jsx`)

---

## 🆘 Troubleshooting

### Backend won't start
```bash
# Check if port 5000 is in use
netstat -ano | findstr :5000

# Kill process if needed (Windows)
taskkill /PID <PID> /F

# Or use different port in .env
PORT=5001
```

### MongoDB connection fails
- Check connection string format
- Verify username/password
- Check network access (0.0.0.0/0 for Atlas)
- Ensure MongoDB service is running (local)

### Frontend can't connect to backend
- Verify backend is running
- Check CORS settings
- Verify API URL in frontend .env
- Check browser console for errors

### Spotify API issues
- Verify Client ID and Secret
- Check redirect URI matches exactly
- Ensure app is not in development mode restrictions

---

## 💡 Pro Tips

1. **Use Git**: Initialize git repo and commit after each working feature
   ```bash
   git init
   git add .
   git commit -m "Initial setup"
   ```

2. **Test API with Postman**: Download Postman to test backend endpoints

3. **Browser DevTools**: Keep console open to catch errors early

4. **Hot Reload**: Both frontend and backend support hot reload - changes reflect immediately

5. **Environment Variables**: Never commit `.env` files to git
   ```bash
   # Add to .gitignore
   echo ".env" >> .gitignore
   ```

---

## 🎉 You're Ready!

You now have:
- ✅ Complete project documentation
- ✅ Development environment set up
- ✅ Backend server running
- ✅ Frontend application running
- ✅ Database connected
- ✅ Spotify API credentials

**Time to build something amazing! 🚀**

---

**Need Help?** Refer to the detailed documentation files in this directory.

**Happy Coding! 🎵**
