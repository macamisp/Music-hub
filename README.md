# 🎵 Music Hub - Spotify-Like Web Application

A modern, feature-rich music streaming web application built with React, Node.js, and the Spotify Web API. Experience premium UI/UX with glassmorphism effects, smooth animations, and personalized music recommendations.

![Music Hub Banner](https://via.placeholder.com/1200x400/1DB954/ffffff?text=Music+Hub+-+Your+Personal+Music+Streaming+Platform)

---

## ✨ Features

### 🎧 Core Features
- **Music Streaming**: Play 30-second previews of millions of tracks
- **Advanced Search**: Search for tracks, albums, artists, and playlists
- **Playlist Management**: Create, edit, and organize your playlists
- **Library Management**: Save your favorite tracks, albums, and artists
- **Personalized Recommendations**: Discover new music based on your taste
- **Recently Played**: Keep track of your listening history

### 🎨 Premium UI/UX
- **Dark Theme**: Sleek, modern dark interface inspired by Spotify
- **Glassmorphism**: Frosted glass effects for a premium look
- **Smooth Animations**: 60fps transitions and micro-interactions
- **Responsive Design**: Optimized for desktop, tablet, and mobile
- **Audio Visualizations**: Real-time waveform and spectrum analyzer

### 🚀 Advanced Features
- **OAuth Authentication**: Secure login with Spotify
- **Real-time Player**: Full-featured music player with queue management
- **Keyboard Shortcuts**: Control playback without touching your mouse
- **PWA Support**: Install as a standalone app
- **Offline Mode**: Access cached content without internet

---

## 🛠️ Tech Stack

### Frontend
- **React 18** - UI library
- **Vite** - Build tool and dev server
- **React Router** - Client-side routing
- **Context API** - State management
- **Howler.js** - Audio playback
- **Web Audio API** - Visualizations

### Backend
- **Node.js** - Runtime environment
- **Express.js** - Web framework
- **MongoDB** - Database
- **Mongoose** - ODM
- **JWT** - Authentication
- **Axios** - HTTP client

### APIs
- **Spotify Web API** - Music data and streaming
- **Deezer API** - Alternative music source
- **Musixmatch API** - Lyrics (optional)

---

## 📁 Project Structure

```
music-hub/
├── frontend/                 # React frontend application
│   ├── src/
│   │   ├── components/      # Reusable UI components
│   │   ├── pages/           # Page components
│   │   ├── context/         # React context providers
│   │   ├── services/        # API services
│   │   ├── hooks/           # Custom React hooks
│   │   ├── styles/          # CSS files
│   │   └── utils/           # Utility functions
│   └── package.json
│
├── backend/                  # Node.js backend server
│   ├── src/
│   │   ├── controllers/     # Route controllers
│   │   ├── models/          # Database models
│   │   ├── routes/          # API routes
│   │   ├── middleware/      # Custom middleware
│   │   ├── services/        # Business logic
│   │   └── config/          # Configuration files
│   └── package.json
│
├── docs/                     # Documentation
│   ├── TECH_STACK_AND_RESOURCES.md
│   ├── IMPLEMENTATION_PLAN.md
│   └── SPOTIFY_API_REFERENCE.md
│
└── README.md
```

---

## 🚀 Getting Started

### Prerequisites
- Node.js 18+ installed
- npm or pnpm package manager
- MongoDB Atlas account (free tier)
- Spotify Developer account

### 1. Clone the Repository
```bash
git clone https://github.com/yourusername/music-hub.git
cd music-hub
```

### 2. Set Up Spotify API
1. Go to [Spotify Developer Dashboard](https://developer.spotify.com/dashboard)
2. Create a new app
3. Note your **Client ID** and **Client Secret**
4. Add redirect URI: `http://localhost:5173/callback`

### 3. Set Up MongoDB
1. Create account at [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)
2. Create a new cluster (free tier)
3. Get your connection string

### 4. Configure Environment Variables

**Frontend** (`frontend/.env`)
```env
VITE_API_URL=http://localhost:5000/api
VITE_SPOTIFY_CLIENT_ID=your_spotify_client_id
VITE_REDIRECT_URI=http://localhost:5173/callback
```

**Backend** (`backend/.env`)
```env
PORT=5000
MONGODB_URI=your_mongodb_connection_string
JWT_SECRET=your_random_secret_key
SPOTIFY_CLIENT_ID=your_spotify_client_id
SPOTIFY_CLIENT_SECRET=your_spotify_client_secret
SPOTIFY_REDIRECT_URI=http://localhost:5173/callback
NODE_ENV=development
```

### 5. Install Dependencies

**Frontend**
```bash
cd frontend
npm install
```

**Backend**
```bash
cd backend
npm install
```

### 6. Run the Application

**Start Backend** (Terminal 1)
```bash
cd backend
npm run dev
```

**Start Frontend** (Terminal 2)
```bash
cd frontend
npm run dev
```

Visit `http://localhost:5173` in your browser 🎉

---

## 📖 Documentation

- **[Tech Stack & Resources](./TECH_STACK_AND_RESOURCES.md)** - Detailed tech stack breakdown and resources
- **[Implementation Plan](./IMPLEMENTATION_PLAN.md)** - Step-by-step development guide
- **[Spotify API Reference](./SPOTIFY_API_REFERENCE.md)** - Complete API documentation with examples

---

## 🎯 Roadmap

### Phase 1: Foundation ✅
- [x] Project setup
- [x] Documentation
- [ ] Basic UI components
- [ ] Authentication flow

### Phase 2: Core Features 🚧
- [ ] Music player
- [ ] Search functionality
- [ ] Playlist management
- [ ] Library management

### Phase 3: Advanced Features 📋
- [ ] Recommendations engine
- [ ] Audio visualizations
- [ ] Lyrics integration
- [ ] Social features

### Phase 4: Polish & Deploy 📋
- [ ] Performance optimization
- [ ] Accessibility improvements
- [ ] Testing
- [ ] Production deployment

---

## 🎨 Screenshots

> Screenshots will be added as development progresses

### Home Page
![Home Page](https://via.placeholder.com/800x500/121212/1DB954?text=Home+Page)

### Search
![Search](https://via.placeholder.com/800x500/121212/1DB954?text=Search+Page)

### Player
![Player](https://via.placeholder.com/800x500/121212/1DB954?text=Music+Player)

---

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## ⚠️ Disclaimer

This project is for educational purposes only. It uses the Spotify Web API which provides 30-second preview URLs for free tier users. Full playback requires Spotify Premium and the Web Playback SDK.

---

## 🙏 Acknowledgments

- [Spotify Web API](https://developer.spotify.com/documentation/web-api) for providing the music data
- [Spotify Design](https://spotify.design/) for design inspiration
- All open-source libraries used in this project

---

## 📧 Contact

Your Name - [@yourtwitter](https://twitter.com/yourtwitter)

Project Link: [https://github.com/yourusername/music-hub](https://github.com/yourusername/music-hub)

---

## 🌟 Show Your Support

Give a ⭐️ if you like this project!

---

**Built with ❤️ and 🎵**
