# Spotify API Quick Reference Guide

## 🎯 Getting Started with Spotify API

### 1. Create Spotify Developer Account
1. Go to https://developer.spotify.com/dashboard
2. Log in with your Spotify account
3. Click "Create an App"
4. Fill in app details:
   - App name: "Music Hub"
   - App description: "A Spotify-like music streaming application"
   - Redirect URI: `http://localhost:5173/callback`
5. Save your **Client ID** and **Client Secret**

---

## 🔐 Authentication Flows

### Authorization Code Flow with PKCE (Recommended for Frontend)

**Step 1: Generate Code Verifier and Challenge**
```javascript
// utils/pkce.js
function generateRandomString(length) {
  const possible = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
  const values = crypto.getRandomValues(new Uint8Array(length));
  return values.reduce((acc, x) => acc + possible[x % possible.length], "");
}

async function sha256(plain) {
  const encoder = new TextEncoder();
  const data = encoder.encode(plain);
  return window.crypto.subtle.digest('SHA-256', data);
}

function base64encode(input) {
  return btoa(String.fromCharCode(...new Uint8Array(input)))
    .replace(/=/g, '')
    .replace(/\+/g, '-')
    .replace(/\//g, '_');
}

export async function generateCodeChallenge(codeVerifier) {
  const hashed = await sha256(codeVerifier);
  return base64encode(hashed);
}

export function generateCodeVerifier() {
  return generateRandomString(64);
}
```

**Step 2: Redirect to Spotify Authorization**
```javascript
// services/spotifyAuth.js
const CLIENT_ID = import.meta.env.VITE_SPOTIFY_CLIENT_ID;
const REDIRECT_URI = import.meta.env.VITE_REDIRECT_URI;

export async function redirectToSpotifyAuth() {
  const codeVerifier = generateCodeVerifier();
  const codeChallenge = await generateCodeChallenge(codeVerifier);
  
  // Store code verifier for later use
  localStorage.setItem('code_verifier', codeVerifier);
  
  const scope = [
    'user-read-private',
    'user-read-email',
    'user-library-read',
    'user-library-modify',
    'playlist-read-private',
    'playlist-modify-public',
    'playlist-modify-private',
    'user-read-playback-state',
    'user-modify-playback-state',
    'user-read-currently-playing',
    'user-read-recently-played',
    'user-top-read',
    'streaming'
  ].join(' ');
  
  const params = new URLSearchParams({
    client_id: CLIENT_ID,
    response_type: 'code',
    redirect_uri: REDIRECT_URI,
    scope: scope,
    code_challenge_method: 'S256',
    code_challenge: codeChallenge,
  });
  
  window.location.href = `https://accounts.spotify.com/authorize?${params.toString()}`;
}
```

**Step 3: Handle Callback and Get Access Token**
```javascript
// services/spotifyAuth.js
export async function handleSpotifyCallback(code) {
  const codeVerifier = localStorage.getItem('code_verifier');
  
  const response = await fetch('https://accounts.spotify.com/api/token', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/x-www-form-urlencoded',
    },
    body: new URLSearchParams({
      client_id: CLIENT_ID,
      grant_type: 'authorization_code',
      code: code,
      redirect_uri: REDIRECT_URI,
      code_verifier: codeVerifier,
    }),
  });
  
  const data = await response.json();
  
  // Store tokens
  localStorage.setItem('access_token', data.access_token);
  localStorage.setItem('refresh_token', data.refresh_token);
  localStorage.setItem('expires_at', Date.now() + data.expires_in * 1000);
  
  return data;
}
```

**Step 4: Refresh Access Token**
```javascript
export async function refreshAccessToken() {
  const refreshToken = localStorage.getItem('refresh_token');
  
  const response = await fetch('https://accounts.spotify.com/api/token', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/x-www-form-urlencoded',
    },
    body: new URLSearchParams({
      client_id: CLIENT_ID,
      grant_type: 'refresh_token',
      refresh_token: refreshToken,
    }),
  });
  
  const data = await response.json();
  
  localStorage.setItem('access_token', data.access_token);
  localStorage.setItem('expires_at', Date.now() + data.expires_in * 1000);
  
  return data.access_token;
}
```

---

## 📡 API Endpoints Reference

### Base URL
```
https://api.spotify.com/v1
```

### Common Headers
```javascript
const headers = {
  'Authorization': `Bearer ${accessToken}`,
  'Content-Type': 'application/json',
};
```

---

## 🎵 Search API

### Search for Items
```javascript
// Search for tracks, artists, albums, playlists
async function search(query, types = ['track', 'artist', 'album']) {
  const params = new URLSearchParams({
    q: query,
    type: types.join(','),
    limit: 20,
  });
  
  const response = await fetch(
    `https://api.spotify.com/v1/search?${params}`,
    { headers }
  );
  
  return await response.json();
}

// Example response structure
{
  "tracks": {
    "items": [
      {
        "id": "track_id",
        "name": "Track Name",
        "artists": [{ "id": "artist_id", "name": "Artist Name" }],
        "album": {
          "id": "album_id",
          "name": "Album Name",
          "images": [{ "url": "image_url", "height": 640, "width": 640 }]
        },
        "duration_ms": 240000,
        "preview_url": "https://p.scdn.co/mp3-preview/...",
        "uri": "spotify:track:..."
      }
    ]
  }
}
```

---

## 🎼 Track APIs

### Get Track
```javascript
async function getTrack(trackId) {
  const response = await fetch(
    `https://api.spotify.com/v1/tracks/${trackId}`,
    { headers }
  );
  return await response.json();
}
```

### Get Multiple Tracks
```javascript
async function getTracks(trackIds) {
  const params = new URLSearchParams({
    ids: trackIds.join(',')
  });
  
  const response = await fetch(
    `https://api.spotify.com/v1/tracks?${params}`,
    { headers }
  );
  return await response.json();
}
```

### Get Track Audio Features
```javascript
async function getAudioFeatures(trackId) {
  const response = await fetch(
    `https://api.spotify.com/v1/audio-features/${trackId}`,
    { headers }
  );
  return await response.json();
}

// Returns: danceability, energy, key, loudness, mode, speechiness, 
// acousticness, instrumentalness, liveness, valence, tempo, etc.
```

---

## 💿 Album APIs

### Get Album
```javascript
async function getAlbum(albumId) {
  const response = await fetch(
    `https://api.spotify.com/v1/albums/${albumId}`,
    { headers }
  );
  return await response.json();
}
```

### Get Album Tracks
```javascript
async function getAlbumTracks(albumId, limit = 50, offset = 0) {
  const params = new URLSearchParams({ limit, offset });
  
  const response = await fetch(
    `https://api.spotify.com/v1/albums/${albumId}/tracks?${params}`,
    { headers }
  );
  return await response.json();
}
```

### Get New Releases
```javascript
async function getNewReleases(limit = 20) {
  const params = new URLSearchParams({ limit });
  
  const response = await fetch(
    `https://api.spotify.com/v1/browse/new-releases?${params}`,
    { headers }
  );
  return await response.json();
}
```

---

## 🎤 Artist APIs

### Get Artist
```javascript
async function getArtist(artistId) {
  const response = await fetch(
    `https://api.spotify.com/v1/artists/${artistId}`,
    { headers }
  );
  return await response.json();
}
```

### Get Artist's Top Tracks
```javascript
async function getArtistTopTracks(artistId, market = 'US') {
  const response = await fetch(
    `https://api.spotify.com/v1/artists/${artistId}/top-tracks?market=${market}`,
    { headers }
  );
  return await response.json();
}
```

### Get Artist's Albums
```javascript
async function getArtistAlbums(artistId, limit = 20) {
  const params = new URLSearchParams({ limit });
  
  const response = await fetch(
    `https://api.spotify.com/v1/artists/${artistId}/albums?${params}`,
    { headers }
  );
  return await response.json();
}
```

### Get Related Artists
```javascript
async function getRelatedArtists(artistId) {
  const response = await fetch(
    `https://api.spotify.com/v1/artists/${artistId}/related-artists`,
    { headers }
  );
  return await response.json();
}
```

---

## 📝 Playlist APIs

### Get Playlist
```javascript
async function getPlaylist(playlistId) {
  const response = await fetch(
    `https://api.spotify.com/v1/playlists/${playlistId}`,
    { headers }
  );
  return await response.json();
}
```

### Get Featured Playlists
```javascript
async function getFeaturedPlaylists(limit = 20) {
  const params = new URLSearchParams({ limit });
  
  const response = await fetch(
    `https://api.spotify.com/v1/browse/featured-playlists?${params}`,
    { headers }
  );
  return await response.json();
}
```

### Get Category Playlists
```javascript
async function getCategoryPlaylists(categoryId, limit = 20) {
  const params = new URLSearchParams({ limit });
  
  const response = await fetch(
    `https://api.spotify.com/v1/browse/categories/${categoryId}/playlists?${params}`,
    { headers }
  );
  return await response.json();
}
```

### Create Playlist
```javascript
async function createPlaylist(userId, name, description = '', isPublic = true) {
  const response = await fetch(
    `https://api.spotify.com/v1/users/${userId}/playlists`,
    {
      method: 'POST',
      headers,
      body: JSON.stringify({
        name,
        description,
        public: isPublic,
      }),
    }
  );
  return await response.json();
}
```

### Add Items to Playlist
```javascript
async function addTracksToPlaylist(playlistId, trackUris) {
  const response = await fetch(
    `https://api.spotify.com/v1/playlists/${playlistId}/tracks`,
    {
      method: 'POST',
      headers,
      body: JSON.stringify({
        uris: trackUris, // ["spotify:track:4iV5W9uYEdYUVa79Axb7Rh", ...]
      }),
    }
  );
  return await response.json();
}
```

### Remove Items from Playlist
```javascript
async function removeTracksFromPlaylist(playlistId, trackUris) {
  const response = await fetch(
    `https://api.spotify.com/v1/playlists/${playlistId}/tracks`,
    {
      method: 'DELETE',
      headers,
      body: JSON.stringify({
        tracks: trackUris.map(uri => ({ uri })),
      }),
    }
  );
  return await response.json();
}
```

---

## 👤 User Library APIs

### Get User's Saved Tracks
```javascript
async function getSavedTracks(limit = 20, offset = 0) {
  const params = new URLSearchParams({ limit, offset });
  
  const response = await fetch(
    `https://api.spotify.com/v1/me/tracks?${params}`,
    { headers }
  );
  return await response.json();
}
```

### Save Tracks for User
```javascript
async function saveTracks(trackIds) {
  const params = new URLSearchParams({
    ids: trackIds.join(',')
  });
  
  const response = await fetch(
    `https://api.spotify.com/v1/me/tracks?${params}`,
    {
      method: 'PUT',
      headers,
    }
  );
  return response.ok;
}
```

### Remove Saved Tracks
```javascript
async function removeSavedTracks(trackIds) {
  const params = new URLSearchParams({
    ids: trackIds.join(',')
  });
  
  const response = await fetch(
    `https://api.spotify.com/v1/me/tracks?${params}`,
    {
      method: 'DELETE',
      headers,
    }
  );
  return response.ok;
}
```

### Check if Tracks are Saved
```javascript
async function checkSavedTracks(trackIds) {
  const params = new URLSearchParams({
    ids: trackIds.join(',')
  });
  
  const response = await fetch(
    `https://api.spotify.com/v1/me/tracks/contains?${params}`,
    { headers }
  );
  return await response.json(); // Returns array of booleans
}
```

---

## 🎧 Personalization APIs

### Get User's Top Items
```javascript
async function getTopItems(type = 'tracks', timeRange = 'medium_term', limit = 20) {
  // type: 'tracks' or 'artists'
  // timeRange: 'short_term' (4 weeks), 'medium_term' (6 months), 'long_term' (years)
  
  const params = new URLSearchParams({ time_range: timeRange, limit });
  
  const response = await fetch(
    `https://api.spotify.com/v1/me/top/${type}?${params}`,
    { headers }
  );
  return await response.json();
}
```

### Get Recommendations
```javascript
async function getRecommendations(seedTracks = [], seedArtists = [], seedGenres = []) {
  const params = new URLSearchParams({
    seed_tracks: seedTracks.join(','),
    seed_artists: seedArtists.join(','),
    seed_genres: seedGenres.join(','),
    limit: 20,
  });
  
  const response = await fetch(
    `https://api.spotify.com/v1/recommendations?${params}`,
    { headers }
  );
  return await response.json();
}
```

### Get Recently Played
```javascript
async function getRecentlyPlayed(limit = 20) {
  const params = new URLSearchParams({ limit });
  
  const response = await fetch(
    `https://api.spotify.com/v1/me/player/recently-played?${params}`,
    { headers }
  );
  return await response.json();
}
```

---

## 👨‍💼 User Profile APIs

### Get Current User's Profile
```javascript
async function getCurrentUser() {
  const response = await fetch(
    'https://api.spotify.com/v1/me',
    { headers }
  );
  return await response.json();
}

// Returns: id, display_name, email, images, followers, etc.
```

### Get User's Playlists
```javascript
async function getUserPlaylists(limit = 20, offset = 0) {
  const params = new URLSearchParams({ limit, offset });
  
  const response = await fetch(
    `https://api.spotify.com/v1/me/playlists?${params}`,
    { headers }
  );
  return await response.json();
}
```

---

## 🎮 Player APIs (Requires Premium)

### Get Playback State
```javascript
async function getPlaybackState() {
  const response = await fetch(
    'https://api.spotify.com/v1/me/player',
    { headers }
  );
  
  if (response.status === 204) return null; // No active device
  return await response.json();
}
```

### Get Currently Playing Track
```javascript
async function getCurrentlyPlaying() {
  const response = await fetch(
    'https://api.spotify.com/v1/me/player/currently-playing',
    { headers }
  );
  
  if (response.status === 204) return null;
  return await response.json();
}
```

### Start/Resume Playback
```javascript
async function play(deviceId, contextUri = null, uris = null) {
  const body = {};
  if (contextUri) body.context_uri = contextUri;
  if (uris) body.uris = uris;
  
  const response = await fetch(
    `https://api.spotify.com/v1/me/player/play?device_id=${deviceId}`,
    {
      method: 'PUT',
      headers,
      body: JSON.stringify(body),
    }
  );
  return response.ok;
}
```

### Pause Playback
```javascript
async function pause(deviceId) {
  const response = await fetch(
    `https://api.spotify.com/v1/me/player/pause?device_id=${deviceId}`,
    {
      method: 'PUT',
      headers,
    }
  );
  return response.ok;
}
```

---

## 🔧 Utility Service Class

```javascript
// services/spotifyApi.js
class SpotifyAPI {
  constructor() {
    this.baseURL = 'https://api.spotify.com/v1';
    this.accessToken = null;
  }
  
  setAccessToken(token) {
    this.accessToken = token;
  }
  
  async request(endpoint, options = {}) {
    // Check if token is expired and refresh if needed
    const expiresAt = localStorage.getItem('expires_at');
    if (Date.now() >= expiresAt) {
      await refreshAccessToken();
      this.accessToken = localStorage.getItem('access_token');
    }
    
    const response = await fetch(`${this.baseURL}${endpoint}`, {
      ...options,
      headers: {
        'Authorization': `Bearer ${this.accessToken}`,
        'Content-Type': 'application/json',
        ...options.headers,
      },
    });
    
    if (!response.ok) {
      throw new Error(`Spotify API Error: ${response.status}`);
    }
    
    if (response.status === 204) return null;
    return await response.json();
  }
  
  // Search
  search(query, types = ['track'], limit = 20) {
    const params = new URLSearchParams({
      q: query,
      type: types.join(','),
      limit,
    });
    return this.request(`/search?${params}`);
  }
  
  // Tracks
  getTrack(id) {
    return this.request(`/tracks/${id}`);
  }
  
  // Albums
  getAlbum(id) {
    return this.request(`/albums/${id}`);
  }
  
  // Artists
  getArtist(id) {
    return this.request(`/artists/${id}`);
  }
  
  // Playlists
  getPlaylist(id) {
    return this.request(`/playlists/${id}`);
  }
  
  getFeaturedPlaylists(limit = 20) {
    return this.request(`/browse/featured-playlists?limit=${limit}`);
  }
  
  // User Library
  getSavedTracks(limit = 20, offset = 0) {
    return this.request(`/me/tracks?limit=${limit}&offset=${offset}`);
  }
  
  saveTrack(id) {
    return this.request(`/me/tracks?ids=${id}`, { method: 'PUT' });
  }
  
  removeTrack(id) {
    return this.request(`/me/tracks?ids=${id}`, { method: 'DELETE' });
  }
  
  // Recommendations
  getRecommendations(seeds) {
    const params = new URLSearchParams(seeds);
    return this.request(`/recommendations?${params}`);
  }
  
  // User
  getCurrentUser() {
    return this.request('/me');
  }
  
  getUserPlaylists(limit = 20) {
    return this.request(`/me/playlists?limit=${limit}`);
  }
}

export default new SpotifyAPI();
```

---

## 📊 Rate Limits

- **Rate Limit**: 180 requests per minute per user
- **Retry-After**: Check `Retry-After` header if you get 429 status
- **Best Practice**: Implement exponential backoff

---

## ⚠️ Important Notes

1. **Preview URLs**: Only 30-second previews available for free tier
2. **Full Playback**: Requires Spotify Premium and Web Playback SDK
3. **Token Expiry**: Access tokens expire after 1 hour
4. **Scopes**: Request only necessary scopes
5. **CORS**: Use backend proxy for production to hide client secret

---

## 🎯 Common Scopes

```javascript
const SCOPES = [
  'user-read-private',           // Read user profile
  'user-read-email',             // Read user email
  'user-library-read',           // Read saved tracks/albums
  'user-library-modify',         // Modify saved tracks/albums
  'playlist-read-private',       // Read private playlists
  'playlist-modify-public',      // Modify public playlists
  'playlist-modify-private',     // Modify private playlists
  'user-read-playback-state',    // Read playback state
  'user-modify-playback-state',  // Control playback
  'user-read-currently-playing', // Read currently playing
  'user-read-recently-played',   // Read recently played
  'user-top-read',               // Read top artists/tracks
  'streaming',                   // Web Playback SDK
];
```

---

## 🔗 Useful Links

- [Spotify Web API Documentation](https://developer.spotify.com/documentation/web-api)
- [Spotify Web Playback SDK](https://developer.spotify.com/documentation/web-playback-sdk)
- [API Console](https://developer.spotify.com/console/)
- [Scopes Reference](https://developer.spotify.com/documentation/web-api/concepts/scopes)

---

**Happy Coding! 🎵**
