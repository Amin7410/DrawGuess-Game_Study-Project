# 🎨 DrawGuess - Multiplayer Drawing & Guessing Game

A real-time multiplayer game where players take turns drawing and guessing words. Built with Socket.IO, Express, and vanilla JavaScript.

## 🚀 MUỐN DEPLOY NGAY?

**Đọc file này**: [`START_HERE.md`](./START_HERE.md)

Hoặc chạy:
```powershell
.\deploy-now.ps1
```

**Repo**: `https://github.com/Amin7410/Game.git`

---

## 🎓 Cho Môn Lập Trình Mạng

**Dự án này sử dụng 15+ khái niệm LTM quan trọng**:
- ✅ Client-Server Model
- ✅ HTTP/HTTPS & WebSocket
- ✅ Socket.IO (Namespaces, Rooms, Events)
- ✅ Real-time Synchronization
- ✅ State Management
- ✅ CORS, Security, Performance

**Đọc chi tiết**: 
- [`LTM_SUMMARY.md`](./LTM_SUMMARY.md) - Tóm tắt ngắn gọn
- [`LTM_CONCEPTS.md`](./LTM_CONCEPTS.md) - Chi tiết đầy đủ
- [`SOCKET_EXPLAINED.md`](./SOCKET_EXPLAINED.md) - Giải thích Socket, WebSocket, Socket.IO

---

## 🌐 Hỗ Trợ LAN & Internet

**Game hoạt động TỐT trên cả LAN và Internet!** ✅

| Môi Trường | Latency | Hiệu Suất |
|------------|---------|-----------|
| **LAN** | <10ms | ⭐⭐⭐⭐⭐ Xuất sắc |
| **Internet** | 50-200ms | ⭐⭐⭐⭐ Tốt |

**Đọc chi tiết**: 
- [`NETWORK_PERFORMANCE.md`](./NETWORK_PERFORMANCE.md) - Tóm tắt
- [`LAN_INTERNET_SUPPORT.md`](./LAN_INTERNET_SUPPORT.md) - Chi tiết đầy đủ

---

## 🌟 Features

- ✅ **Real-time multiplayer** - Play with friends online
- ✅ **Quick Play** - Auto-generated room codes for instant games
- ✅ **Custom Rooms** - Create private rooms with passwords
- ✅ **Room Browser** - Browse and join available games
- ✅ **Share Room Codes** - Easy sharing with copy-to-clipboard
- ✅ **Drawing Tools** - Multiple colors, brush sizes, and eraser
- ✅ **Chat System** - Real-time chat for guessers
- ✅ **Scoring System** - Points based on guess speed
- ✅ **Round Management** - Multiple rounds with automatic rotation
- ✅ **Admin Panel** - Monitor server stats and active rooms

---

## 🏗️ Project Structure

```
drawguess-enterprise/
├── drawguess-server/          # Backend (Node.js + Socket.IO)
│   ├── index.js               # Main server file
│   ├── config/                # Configuration
│   ├── sockets/               # Socket.IO handlers
│   │   ├── mainHandler.js     # Game logic
│   │   └── adminHandler.js    # Admin panel
│   ├── game/                  # Game models
│   │   └── GameRoom.js        # Room state management
│   └── utils/                 # Utilities
│       ├── wordList.js
│       └── roomCodeGenerator.js
│
├── drawguess-webapp/          # Frontend (Vanilla JS)
│   └── public/
│       ├── index.html         # Lobby page
│       ├── game.html          # Game page
│       ├── app.js             # Lobby logic
│       ├── game.js            # Game logic
│       ├── config.js          # Client config
│       └── styles.css         # Styles
│
├── render.yaml                # Render deployment config
├── deploy-now.ps1             # Deploy script
├── LTM_CONCEPTS.md            # Network programming concepts
└── README.md                  # This file
```

---

## 🚀 Quick Start

### Local Development

#### 1. Start Backend:
```bash
cd drawguess-server
npm install
npm start
```
Server runs at: `http://localhost:3001`

#### 2. Start Frontend:
```bash
cd drawguess-webapp
npx serve public -p 3000
```
Frontend runs at: `http://localhost:3000`

#### 3. Play:
- Open `http://localhost:3000`
- Enter your name
- Click "Quick Play" or "Create Room"
- Open another tab/browser to join as second player

---

## 🌐 Deploy to Render

### Option 1: Quick Deploy (Recommended)

```powershell
.\deploy-now.ps1
```

Then follow instructions in [`DEPLOY_INSTRUCTIONS.md`](./DEPLOY_INSTRUCTIONS.md)

### Option 2: Manual Deploy

See detailed guide: [`DEPLOY_QUICK.md`](./DEPLOY_QUICK.md)

---

## 🎮 How to Play

### Create a Room:
1. Enter your name
2. Click "Create Room"
3. Set room ID and password (optional)
4. Share room code with friends

### Quick Play:
1. Enter your name
2. Click "Quick Play"
3. Auto-generated room code
4. Share and play!

### Gameplay:
1. **Drawer**: Choose a word and draw it
2. **Guessers**: Type guesses in chat
3. **Points**: Faster guess = more points
4. **Rounds**: Players take turns drawing
5. **Winner**: Highest score after all rounds

---

## 🔧 Tech Stack

### Backend:
- **Node.js** - Runtime
- **Express** - Web framework
- **Socket.IO** - Real-time communication
- **CORS** - Cross-origin support

### Frontend:
- **Vanilla JavaScript** - No frameworks
- **HTML5 Canvas** - Drawing
- **Socket.IO Client** - Real-time events
- **CSS3** - Styling

### Deployment:
- **Render** - Cloud hosting (Free tier)
- **GitHub** - Version control
- **Blueprint** - Infrastructure as Code

---

## 📡 Network Architecture

```
┌─────────────────────────────────────┐
│   Browser (Client)                  │
│   - HTML/CSS/JS                     │
│   - Socket.IO Client                │
│   - Canvas API                      │
└──────────────┬──────────────────────┘
               │
               │ HTTPS + WebSocket
               │
┌──────────────▼──────────────────────┐
│   Render Server                     │
│   https://drawguess-game.onrender   │
│                                     │
│   ┌─────────────────────────────┐  │
│   │  Express Server             │  │
│   │  - Serve static files       │  │
│   │  - REST API (/api, /health) │  │
│   └─────────────────────────────┘  │
│                                     │
│   ┌─────────────────────────────┐  │
│   │  Socket.IO Server           │  │
│   │  - /game namespace          │  │
│   │  - /admin namespace         │  │
│   │  - Rooms management         │  │
│   └─────────────────────────────┘  │
│                                     │
│   ┌─────────────────────────────┐  │
│   │  Game State                 │  │
│   │  - rooms Map                │  │
│   │  - players Map              │  │
│   │  - GameRoom instances       │  │
│   └─────────────────────────────┘  │
└─────────────────────────────────────┘
```

---

## 🎓 Network Programming Concepts

This project demonstrates comprehensive network programming concepts:

### Core Concepts:
1. **Client-Server Model** - Centralized server architecture
2. **HTTP/HTTPS Protocol** - Static files and REST API
3. **WebSocket Protocol** - Real-time bidirectional communication
4. **Socket.IO** - Event-driven messaging with fallback
5. **Namespaces** - Logical separation (`/game`, `/admin`)
6. **Rooms** - Group messaging and broadcast
7. **Event-Driven Architecture** - 20+ custom events
8. **CORS** - Cross-origin resource sharing
9. **State Management** - Server-side state synchronization
10. **Real-Time Sync** - Drawing, chat, timer synchronization

### Advanced Concepts:
11. **Security** - Rate limiting, input validation, sanitization
12. **Connection Management** - Auto-reconnect, disconnect handling
13. **Network Topology** - Star topology (server-centric)
14. **Broadcasting** - Efficient message distribution
15. **Performance** - Throttling, efficient state management

**Full details**: [`LTM_CONCEPTS.md`](./LTM_CONCEPTS.md)

---

## 📊 Socket.IO Events

### Lobby Events:
- `get-room-list` - Get available rooms
- `lobby:quick-play` - Create quick play room
- `lobby:create-room` - Create custom room
- `room-list-update` - Room list updated

### Game Events:
- `join-game` - Join a room
- `game-state` - Current game state
- `player-joined` - New player joined
- `player-left` - Player left
- `choose-word` - Select word to draw
- `word-selected` - Word chosen
- `word-hint` - Word hint for guessers
- `next-round` - New round started
- `timer-update` - Timer tick
- `round-end` - Round ended
- `game-over` - Game finished

### Drawing Events:
- `draw` - Drawing stroke
- `clear-canvas` - Clear canvas

### Chat Events:
- `chat-message` - Chat message
- `correct-answer` - Correct guess

---

## 🔒 Security Features

- ✅ **Rate Limiting** - Prevent event spam (20 events/second)
- ✅ **Input Validation** - Validate all user inputs
- ✅ **Sanitization** - Clean chat messages (XSS prevention)
- ✅ **Password Protection** - Optional room passwords
- ✅ **CORS** - Whitelist allowed origins
- ✅ **Room Validation** - Check room codes format

---

## 📝 Environment Variables

### Development:
```env
PORT=3001
NODE_ENV=development
ALLOWED_ORIGINS=http://localhost:3000,http://127.0.0.1:3000
```

### Production (Render):
```env
PORT=10000
NODE_ENV=production
ALLOWED_ORIGINS=https://drawguess-game.onrender.com
```

---

## 🐛 Troubleshooting

### Connection Issues:
- Check server URL in `config.js`
- Verify CORS settings
- Check browser console (F12)

### Drawing Not Syncing:
- Verify WebSocket connection
- Check if player is drawer
- Look for errors in console

### Room Not Found:
- Verify room code
- Check if room expired (empty rooms auto-delete)

**More help**: [`BUGS_FIXED.md`](./BUGS_FIXED.md)

---

## 📚 Documentation

- [`START_HERE.md`](./START_HERE.md) - Quick start guide
- [`DEPLOY_INSTRUCTIONS.md`](./DEPLOY_INSTRUCTIONS.md) - Deploy to Render
- [`DEPLOY_QUICK.md`](./DEPLOY_QUICK.md) - 5-minute deploy guide
- [`LTM_SUMMARY.md`](./LTM_SUMMARY.md) - Network concepts summary
- [`LTM_CONCEPTS.md`](./LTM_CONCEPTS.md) - Detailed network concepts
- [`BUGS_FIXED.md`](./BUGS_FIXED.md) - Fixed issues
- [`READY_TO_DEPLOY.md`](./READY_TO_DEPLOY.md) - Deployment checklist

---

## 💰 Cost

**$0 - Completely FREE!**

Using Render Free Tier:
- ✅ 750 hours/month
- ✅ 100GB bandwidth
- ✅ WebSocket support
- ✅ Auto SSL (HTTPS)
- ⚠️ Sleeps after 15 minutes (wake up: 30-60s)

---

## 🎉 Demo

**Live Demo**: `https://drawguess-game.onrender.com`

(Replace with your actual deployed URL)

---

## 📄 License

MIT License - Feel free to use for your projects!

---

## 👨‍💻 Author

**Amin7410**
- GitHub: https://github.com/Amin7410
- Repo: https://github.com/Amin7410/Game

---

## 🙏 Acknowledgments

- Socket.IO for real-time communication
- Express for web framework
- Render for free hosting
- Canvas API for drawing

---

**Perfect for Network Programming Course! 🎓**

**Deploy now**: `.\deploy-now.ps1`
