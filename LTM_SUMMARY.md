# 📚 Tóm Tắt: Khái Niệm LTM Trong DrawGuess

## 🎯 Dự Án Sử Dụng Gì?

### 1. **Mô Hình Client-Server** ⭐⭐⭐
- Server trung tâm quản lý game logic
- Nhiều clients (browsers) kết nối đồng thời
- **File**: `drawguess-server/index.js`, `drawguess-webapp/public/app.js`

### 2. **Giao Thức HTTP/HTTPS** ⭐⭐⭐
- Phục vụ static files (HTML, CSS, JS)
- REST API endpoints (`/api`, `/health`)
- **File**: `drawguess-server/index.js` (Express routes)

### 3. **Giao Thức WebSocket** ⭐⭐⭐
- Real-time bidirectional communication
- Persistent connection (kết nối liên tục)
- Low latency (<100ms)
- **File**: Socket.IO implementation

### 4. **Socket.IO Protocol** ⭐⭐⭐
- Abstraction layer trên WebSocket
- Auto-reconnection
- Fallback to polling
- **File**: Toàn bộ socket events

### 5. **Namespaces** ⭐⭐
- `/game` - Cho gameplay
- `/admin` - Cho admin panel
- Tách biệt logic và bảo mật
- **File**: `drawguess-server/index.js` (dòng 60-61)

### 6. **Rooms (Phòng)** ⭐⭐⭐
- Nhóm players thành rooms
- Broadcast messages trong room
- Mỗi room = 1 game session
- **File**: `drawguess-server/sockets/mainHandler.js`

### 7. **Event-Driven Architecture** ⭐⭐⭐
- Giao tiếp dựa trên events
- 20+ events khác nhau
- Non-blocking I/O
- **File**: Tất cả socket handlers

### 8. **CORS (Cross-Origin Resource Sharing)** ⭐⭐
- Cho phép frontend gọi backend từ domain khác
- Bảo mật: Chỉ cho phép origins tin cậy
- **File**: `drawguess-server/index.js` (dòng 13-21)

### 9. **State Management** ⭐⭐⭐
- Server lưu global state (rooms, players)
- Đồng bộ state giữa clients
- **File**: `drawguess-server/game/GameRoom.js`

### 10. **Real-Time Synchronization** ⭐⭐⭐
- Drawing sync (vẽ đồng bộ)
- Chat sync
- Timer sync
- Player state sync
- **File**: Drawing events, chat events

### 11. **Security** ⭐⭐
- **Rate Limiting**: Chống spam events
- **Input Validation**: Validate player data, room codes
- **Sanitization**: Làm sạch chat messages
- **File**: `mainHandler.js` (middleware, validation)

### 12. **Connection Management** ⭐⭐
- Auto-reconnection (5 attempts)
- Disconnect handling
- Cleanup resources
- **File**: Socket.IO config, disconnect handler

### 13. **Network Topology: Star** ⭐⭐
- Server là trung tâm
- Clients không giao tiếp trực tiếp
- Mọi message qua server
- **Lý do**: Dễ quản lý, bảo mật tốt

### 14. **Broadcasting** ⭐⭐⭐
- `socket.emit()` - Gửi cho 1 client
- `socket.to(room).emit()` - Gửi cho room (trừ sender)
- `namespace.to(room).emit()` - Gửi cho cả room
- **File**: Khắp nơi trong socket handlers

### 15. **Performance Optimization** ⭐⭐
- Throttling draw events
- Efficient broadcasting (chỉ gửi cho room cần thiết)
- State cleanup (xóa room empty)
- **File**: Draw handler, disconnect handler

---

## 📊 Bảng Tổng Hợp

| Khái Niệm | Quan Trọng | Vị Trí Trong Code |
|-----------|------------|-------------------|
| **Client-Server** | ⭐⭐⭐ | `index.js`, `app.js` |
| **HTTP/HTTPS** | ⭐⭐⭐ | Express routes |
| **WebSocket** | ⭐⭐⭐ | Socket.IO |
| **Event-Driven** | ⭐⭐⭐ | Socket events |
| **Namespaces** | ⭐⭐ | `/game`, `/admin` |
| **Rooms** | ⭐⭐⭐ | Game rooms |
| **Broadcasting** | ⭐⭐⭐ | `emit()`, `to()` |
| **CORS** | ⭐⭐ | CORS config |
| **State Management** | ⭐⭐⭐ | `GameRoom.js` |
| **Real-time Sync** | ⭐⭐⭐ | Draw, chat, timer |
| **Security** | ⭐⭐ | Rate limit, validation |
| **Connection Mgmt** | ⭐⭐ | Reconnect, disconnect |

---

## 🎓 Cho Báo Cáo Môn Học

### Điểm Nổi Bật:

1. **Đầy đủ khái niệm**: 15+ khái niệm LTM quan trọng
2. **Thực tế**: Ứng dụng thực tế, không chỉ lý thuyết
3. **Production**: Deploy được lên cloud (Render)
4. **Modern**: Công nghệ hiện đại (Socket.IO, WebSocket)
5. **Scalable**: Hỗ trợ nhiều rooms, nhiều players
6. **Secure**: Validation, rate limiting, CORS

### Các Events Chính:

**Lobby**:
- `get-room-list`, `lobby:quick-play`, `lobby:create-room`

**Game**:
- `join-game`, `game-state`, `player-joined`, `player-left`
- `choose-word`, `word-selected`, `word-hint`
- `next-round`, `timer-update`, `round-end`, `game-over`

**Drawing**:
- `draw`, `clear-canvas`

**Chat**:
- `chat-message`, `correct-answer`

### Kiến Trúc:

```
Browser (Client)
    ↓ HTTPS + WebSocket
Render Server
    ├─ Express (HTTP/HTTPS)
    ├─ Socket.IO Server
    │   ├─ /game namespace
    │   └─ /admin namespace
    └─ GameRoom (State Management)
```

---

## 📖 Đọc Thêm

Chi tiết đầy đủ: **`LTM_CONCEPTS.md`**

---

**Dự án này hoàn toàn phù hợp và xuất sắc cho môn Lập Trình Mạng! 🎉**
