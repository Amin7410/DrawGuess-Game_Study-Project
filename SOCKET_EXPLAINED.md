# 🔌 Giải Thích Chi Tiết: Socket, WebSocket, Socket.IO

## 📚 Mục Lục
1. [Socket Là Gì?](#1-socket-là-gì)
2. [TCP vs UDP](#2-tcp-vs-udp)
3. [WebSocket Là Gì?](#3-websocket-là-gì)
4. [Socket.IO Là Gì?](#4-socketio-là-gì)
5. [Luồng Xử Lý](#5-luồng-xử-lý)
6. [Multicast & Multithreading](#6-multicast--multithreading)

---

## 1. 🔌 Socket Là Gì?

### Định Nghĩa:
**Socket** = Điểm cuối (endpoint) của kết nối mạng giữa 2 máy tính.

### Ví Dụ Đơn Giản:
```
Socket = Ổ cắm điện
- Máy tính A có 1 ổ cắm (socket)
- Máy tính B có 1 ổ cắm (socket)
- Dây điện = Kết nối mạng
- Cắm vào → Giao tiếp được
```

### Trong Lập Trình:
```javascript
// Socket = Đối tượng đại diện cho 1 kết nối
socket.id = "abc123"  // ID duy nhất
socket.emit('message', data)  // Gửi data
socket.on('message', callback)  // Nhận data
```

### Socket Trong Dự Án:
```javascript
// Server: Mỗi client kết nối = 1 socket
gameNamespace.on('connection', (socket) => {
  console.log('Socket ID:', socket.id);  // Mỗi người chơi có 1 ID
  
  socket.on('join-game', (data) => {
    // Xử lý khi người chơi join
  });
});
```

---

## 2. 🔀 TCP vs UDP

### 2.1. TCP (Transmission Control Protocol)

**Đặc điểm**:
- ✅ **Reliable** (Đáng tin cậy): Đảm bảo data đến đích
- ✅ **Ordered** (Có thứ tự): Data đến đúng thứ tự gửi
- ✅ **Connection-oriented**: Phải thiết lập kết nối trước
- ❌ **Chậm hơn UDP**: Do phải check và retry

**Cách Hoạt Động**:
```
Client                    Server
  |                          |
  |--- SYN ----------------->|  (Xin kết nối)
  |<-- SYN-ACK --------------|  (OK, đồng ý)
  |--- ACK ----------------->|  (Xác nhận)
  |                          |
  |=== CONNECTED ============|
  |                          |
  |--- Data packet 1 ------->|
  |<-- ACK ------------------|  (Đã nhận packet 1)
  |--- Data packet 2 ------->|
  |<-- ACK ------------------|  (Đã nhận packet 2)
```

**Ví Dụ**: HTTP, HTTPS, WebSocket (dùng TCP)

### 2.2. UDP (User Datagram Protocol)

**Đặc điểm**:
- ✅ **Fast** (Nhanh): Không cần check/retry
- ❌ **Unreliable**: Data có thể mất
- ❌ **Unordered**: Data đến không đúng thứ tự
- ✅ **Connectionless**: Không cần thiết lập kết nối

**Cách Hoạt Động**:
```
Client                    Server
  |                          |
  |--- Data packet 1 ------->|  (Gửi luôn, không care)
  |--- Data packet 2 ------->|
  |--- Data packet 3 ------->|
  |                          |
  (Không có ACK, không biết đã nhận chưa)
```

**Ví Dụ**: Video streaming, Gaming (FPS games), DNS

### 2.3. Dự Án Này Dùng Gì?

**Dùng TCP** (qua WebSocket):
- ✅ Cần đảm bảo data đến đích (chat, drawing)
- ✅ Cần thứ tự (vẽ stroke theo thứ tự)
- ✅ Không cần tốc độ cực nhanh

```javascript
// WebSocket dùng TCP
const io = new Server(server, {
  transports: ['websocket', 'polling']  // Cả 2 đều dùng TCP
});
```

---

## 3. 🌐 WebSocket Là Gì?

### Định Nghĩa:
**WebSocket** = Giao thức cho phép giao tiếp **2 chiều** (bidirectional) giữa client và server qua **1 kết nối duy nhất**.

### So Sánh HTTP vs WebSocket:

#### HTTP (Request-Response):
```
Client                    Server
  |                          |
  |--- Request ------------->|  (Xin data)
  |<-- Response -------------|  (Trả data)
  |                          |
  |--- Request ------------->|  (Xin data lại)
  |<-- Response -------------|  (Trả data lại)
  |                          |
  (Mỗi lần phải tạo kết nối mới)
```

#### WebSocket (Persistent Connection):
```
Client                    Server
  |                          |
  |--- Handshake ----------->|  (Xin upgrade lên WebSocket)
  |<-- Upgrade OK -----------|  (OK, upgrade)
  |                          |
  |=== CONNECTED ============|  (Kết nối liên tục)
  |                          |
  |--- Message 1 ----------->|
  |<-- Message 2 ------------|
  |--- Message 3 ----------->|
  |<-- Message 4 ------------|
  |                          |
  (Kết nối mở suốt, không cần tạo lại)
```

### WebSocket Handshake:

**Bước 1: Client gửi HTTP request đặc biệt**
```http
GET /game HTTP/1.1
Host: drawguess-game.onrender.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13
```

**Bước 2: Server trả lời**
```http
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
```

**Bước 3: Kết nối WebSocket được thiết lập**
```
Client <======= WebSocket Connection =======> Server
       (Bidirectional, Persistent)
```

### Trong Code:

**Server** (`index.js`):
```javascript
const http = require('http');
const { Server } = require('socket.io');

const server = http.createServer(app);  // HTTP server
const io = new Server(server, {         // WebSocket server
  cors: {
    origin: allowedOrigins,
    methods: ["GET", "POST"]
  }
});

// WebSocket sẽ tự động upgrade từ HTTP
```

**Client** (`app.js`):
```javascript
const socket = io(serverUrl + '/game', {
  transports: ['websocket', 'polling']  // Ưu tiên WebSocket
});

// Socket.IO tự động:
// 1. Gửi HTTP request với Upgrade header
// 2. Nhận 101 Switching Protocols
// 3. Thiết lập WebSocket connection
```

---

## 4. 🚀 Socket.IO Là Gì?

### Định Nghĩa:
**Socket.IO** = Thư viện JavaScript **bọc ngoài** WebSocket, cung cấp thêm nhiều tính năng.

### WebSocket vs Socket.IO:

| Tính Năng | WebSocket | Socket.IO |
|-----------|-----------|-----------|
| **Giao thức** | Giao thức chuẩn | Thư viện (library) |
| **Fallback** | ❌ Không | ✅ Polling nếu WebSocket fail |
| **Auto-reconnect** | ❌ Phải tự code | ✅ Tự động |
| **Rooms** | ❌ Không có | ✅ Có sẵn |
| **Namespaces** | ❌ Không có | ✅ Có sẵn |
| **Events** | ❌ Chỉ có `message` | ✅ Custom events |
| **Acknowledgements** | ❌ Không có | ✅ Có callback |

### Socket.IO = WebSocket + Nhiều Tính Năng:

```
┌─────────────────────────────────────┐
│         Socket.IO Library           │
│  - Auto-reconnection                │
│  - Fallback to polling              │
│  - Rooms & Namespaces               │
│  - Custom events                    │
│  - Acknowledgements                 │
│  - Broadcasting                     │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│         WebSocket Protocol          │
│  - Bidirectional communication      │
│  - Persistent connection            │
│  - Low latency                      │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│              TCP                    │
│  - Reliable, ordered delivery       │
└─────────────────────────────────────┘
```

### Tính Năng Socket.IO Trong Dự Án:

#### 1. **Namespaces** (Không gian tên)
```javascript
// Server
const gameNamespace = io.of('/game');    // Namespace cho game
const adminNamespace = io.of('/admin');  // Namespace cho admin

// Client
const socket = io(serverUrl + '/game');  // Kết nối vào /game
```

#### 2. **Rooms** (Phòng)
```javascript
// Server: Join room
socket.join(roomId);  // Player join vào room

// Broadcast to room
socket.to(roomId).emit('draw', data);  // Gửi cho room (trừ sender)
gameNamespace.to(roomId).emit('timer-update', data);  // Gửi cho cả room
```

#### 3. **Custom Events**
```javascript
// Server
socket.on('join-game', (data) => { ... });
socket.on('draw', (data) => { ... });
socket.on('chat-message', (data) => { ... });

// Client
socket.emit('join-game', { playerName, roomId });
socket.emit('draw', { x0, y0, x1, y1, color });
```

#### 4. **Acknowledgements** (Callback)
```javascript
// Client gửi với callback
socket.emit('lobby:create-room', data, (response) => {
  if (response.success) {
    console.log('Room created:', response.roomId);
  }
});

// Server trả lời qua callback
socket.on('lobby:create-room', (data, callback) => {
  const roomId = createRoom(data);
  callback({ success: true, roomId });  // Gọi callback
});
```

#### 5. **Auto-Reconnection**
```javascript
const socket = io(serverUrl, {
  reconnectionAttempts: 5,  // Thử reconnect 5 lần
  timeout: 10000            // Timeout 10s
});

socket.on('reconnect', (attemptNumber) => {
  console.log('Reconnected after', attemptNumber, 'attempts');
});
```

---

## 5. 🔄 Luồng Xử Lý

### 5.1. Luồng Kết Nối

```
┌─────────────────────────────────────────────────────────────┐
│  BƯỚC 1: CLIENT MỞ TRANG WEB                                │
└──────────────┬──────────────────────────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────────────────────────┐
│  BƯỚC 2: LOAD HTML, CSS, JS                                 │
│  - Browser tải index.html                                   │
│  - Load app.js, config.js                                   │
│  - Load Socket.IO client library                            │
└──────────────┬──────────────────────────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────────────────────────┐
│  BƯỚC 3: KHỞI TẠO SOCKET.IO CLIENT                          │
│  const socket = io(serverUrl + '/game', {                   │
│    transports: ['websocket', 'polling']                     │
│  });                                                         │
└──────────────┬──────────────────────────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────────────────────────┐
│  BƯỚC 4: WEBSOCKET HANDSHAKE                                │
│  Client: GET /game HTTP/1.1                                 │
│          Upgrade: websocket                                 │
│  Server: HTTP/1.1 101 Switching Protocols                   │
│          Upgrade: websocket                                 │
└──────────────┬──────────────────────────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────────────────────────┐
│  BƯỚC 5: KẾT NỐI THÀNH CÔNG                                 │
│  Client: socket.on('connect', () => {                       │
│    console.log('Connected!', socket.id);                    │
│  });                                                         │
│                                                              │
│  Server: gameNamespace.on('connection', (socket) => {       │
│    console.log('New connection:', socket.id);               │
│  });                                                         │
└─────────────────────────────────────────────────────────────┘
```

### 5.2. Luồng Tạo Room

```
┌─────────────────────────────────────────────────────────────┐
│  CLIENT                                                     │
└──────────────┬──────────────────────────────────────────────┘
               │
               │ 1. User nhập tên, room ID, password
               │ 2. Click "Create Room"
               │
               ▼
┌─────────────────────────────────────────────────────────────┐
│  socket.emit('lobby:create-room', {                         │
│    playerName: 'Amin',                                      │
│    roomId: '123',                                           │
│    password: 'abc'                                          │
│  }, (response) => {                                         │
│    // Callback nhận response                                │
│  });                                                         │
└──────────────┬──────────────────────────────────────────────┘
               │
               │ Event qua WebSocket
               │
               ▼
┌─────────────────────────────────────────────────────────────┐
│  SERVER (mainHandler.js)                                    │
└──────────────┬──────────────────────────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────────────────────────┐
│  socket.on('lobby:create-room', (data, callback) => {       │
│    // 1. Validate data                                      │
│    if (!data.playerName) {                                  │
│      return callback({ success: false, message: 'Error' }); │
│    }                                                         │
│                                                              │
│    // 2. Tạo room mới                                       │
│    const newRoom = new GameRoom(roomId, broadcast, {...});  │
│    rooms.set(roomId, newRoom);                              │
│                                                              │
│    // 3. Broadcast room list update                         │
│    broadcastRoomListUpdate();                               │
│                                                              │
│    // 4. Gọi callback                                       │
│    callback({ success: true, roomId });                     │
│  });                                                         │
└──────────────┬──────────────────────────────────────────────┘
               │
               │ Response qua WebSocket
               │
               ▼
┌─────────────────────────────────────────────────────────────┐
│  CLIENT                                                     │
│  (response) => {                                            │
│    if (response.success) {                                  │
│      window.location.href = `game?room=${response.roomId}`; │
│    }                                                         │
│  }                                                           │
└─────────────────────────────────────────────────────────────┘
```

### 5.3. Luồng Vẽ (Drawing)

```
┌─────────────────────────────────────────────────────────────┐
│  PLAYER 1 (Drawer)                                          │
└──────────────┬──────────────────────────────────────────────┘
               │
               │ 1. User vẽ trên canvas
               │ 2. Mouse move event
               │
               ▼
┌─────────────────────────────────────────────────────────────┐
│  function draw(e) {                                         │
│    // Vẽ local                                              │
│    drawLocal(x0, y0, x1, y1, color, size);                  │
│                                                              │
│    // Gửi lên server                                        │
│    socket.emit('draw', {                                    │
│      x0, y0, x1, y1, color, size, tool                      │
│    });                                                       │
│  }                                                           │
└──────────────┬──────────────────────────────────────────────┘
               │
               │ Event qua WebSocket
               │
               ▼
┌─────────────────────────────────────────────────────────────┐
│  SERVER                                                     │
│  socket.on('draw', (data) => {                              │
│    const player = players.get(socket.id);                   │
│                                                              │
│    // Broadcast to room (trừ sender)                        │
│    socket.to(player.roomId).emit('draw', data);             │
│  });                                                         │
└──────────────┬──────────────────────────────────────────────┘
               │
               │ Broadcast qua WebSocket
               │
               ▼
┌─────────────────────────────────────────────────────────────┐
│  PLAYER 2, 3, 4... (Guessers)                               │
│  socket.on('draw', (data) => {                              │
│    if (!isDrawer) {                                         │
│      drawRemote(data);  // Vẽ lên canvas                    │
│    }                                                         │
│  });                                                         │
└─────────────────────────────────────────────────────────────┘
```

### 5.4. Node.js Event Loop

Node.js xử lý **bất đồng bộ** (asynchronous) bằng **Event Loop**:

```
┌───────────────────────────┐
│      Event Queue          │
│  - socket.on('draw')      │
│  - socket.on('chat')      │
│  - setTimeout callback    │
│  - ...                    │
└──────────┬────────────────┘
           │
           ▼
┌───────────────────────────┐
│      Event Loop           │
│  while (true) {           │
│    event = queue.pop();   │
│    event.execute();       │
│  }                        │
└──────────┬────────────────┘
           │
           ▼
┌───────────────────────────┐
│   Execute Callback        │
│   - Xử lý logic           │
│   - Gọi database          │
│   - Emit events           │
└───────────────────────────┘
```

**Ví dụ**:
```javascript
// Event 1: Player A vẽ
socket.on('draw', (data) => {
  // Xử lý ngay, không block
  socket.to(roomId).emit('draw', data);
});

// Event 2: Player B chat (xảy ra đồng thời)
socket.on('chat-message', (data) => {
  // Xử lý ngay, không đợi Event 1
  gameNamespace.to(roomId).emit('chat-message', data);
});

// Cả 2 events xử lý song song (concurrent)
// Không cần multithread!
```

---

## 6. 🔀 Multicast & Multithreading

### 6.1. Multicast Trong Dự Án

**Multicast** = Gửi message cho **nhiều clients cùng lúc**.

#### Các Loại Broadcasting:

**1. Unicast** (1-to-1):
```javascript
// Gửi cho 1 client cụ thể
socket.emit('word-selected', { word: 'cat' });
```

**2. Broadcast** (1-to-many, trừ sender):
```javascript
// Gửi cho tất cả trong room, trừ sender
socket.to(roomId).emit('draw', data);
```

**3. Multicast** (1-to-many, bao gồm sender):
```javascript
// Gửi cho tất cả trong room, kể cả sender
gameNamespace.to(roomId).emit('timer-update', { timeLeft: 60 });
```

**4. Broadcast All** (1-to-all):
```javascript
// Gửi cho tất cả clients trong namespace
gameNamespace.emit('room-list-update', rooms);
```

#### Trong Code:

```javascript
// Ví dụ: Timer update
function startTimer(onTimerEnd) {
  this.timer = setInterval(() => {
    this.timeLeft--;
    
    // MULTICAST: Gửi cho tất cả players trong room
    this.broadcast('timer-update', { timeLeft: this.timeLeft });
    //           ↓
    //    gameNamespace.to(roomId).emit(...)
    //           ↓
    //    ┌─────────┬─────────┬─────────┐
    //    Player 1  Player 2  Player 3  Player 4
    //    (Tất cả đều nhận cùng lúc)
  }, 1000);
}
```

### 6.2. Multithreading

**Câu hỏi**: Dự án có dùng multithread không?

**Trả lời**: **KHÔNG** - Node.js là **single-threaded**!

#### Node.js Single-Threaded Model:

```
┌─────────────────────────────────────┐
│     Main Thread (JavaScript)        │
│  - Xử lý tất cả logic               │
│  - Event loop                       │
│  - Không block                      │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│     Thread Pool (C++)               │
│  - File I/O                         │
│  - Network I/O                      │
│  - Crypto operations                │
│  (Tự động, không cần code)          │
└─────────────────────────────────────┘
```

#### Tại Sao Không Cần Multithread?

**1. Event-Driven Architecture**:
```javascript
// Không block, xử lý bất đồng bộ
socket.on('draw', (data) => {
  // Xử lý nhanh, không đợi
  socket.to(roomId).emit('draw', data);
});

// Nhiều events xử lý đồng thời (concurrent)
// Nhưng chỉ 1 thread!
```

**2. Non-Blocking I/O**:
```javascript
// Không block thread chính
setTimeout(() => {
  startNewRound(room);
}, 5000);  // Đợi 5s, nhưng không block

// Trong lúc đợi, vẫn xử lý events khác
```

**3. Scalability**:
```
1 Node.js process = Xử lý được 1000+ connections
Lý do: Event loop + Non-blocking I/O
```

#### So Sánh:

| | Multithreading | Node.js Event Loop |
|---|---|---|
| **Threads** | Nhiều threads | 1 thread chính |
| **Complexity** | Phức tạp (race conditions) | Đơn giản |
| **Memory** | Nhiều (mỗi thread = 1-2MB) | Ít |
| **Context Switching** | Chậm | Nhanh |
| **Scalability** | Giới hạn bởi threads | Giới hạn bởi memory |

#### Khi Nào Cần Multithread?

**Cần**:
- CPU-intensive tasks (image processing, video encoding)
- Heavy computation

**Không cần** (như dự án này):
- I/O-intensive (network, database)
- Real-time communication
- Event-driven applications

---

## 📊 Tổng Kết

### Stack Công Nghệ:

```
┌─────────────────────────────────────┐
│  Application Layer                  │
│  - Socket.IO (Custom events)        │
│  - Namespaces, Rooms                │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│  WebSocket Protocol                 │
│  - Bidirectional                    │
│  - Persistent connection            │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│  TCP Protocol                       │
│  - Reliable, ordered                │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│  IP Protocol                        │
│  - Routing, addressing              │
└─────────────────────────────────────┘
```

### Câu Trả Lời Ngắn Gọn:

1. **Socket là gì?** 
   - Điểm cuối của kết nối mạng

2. **TCP hay UDP?** 
   - **TCP** (qua WebSocket)

3. **WebSocket là gì?** 
   - Giao thức giao tiếp 2 chiều, persistent connection

4. **Socket.IO là gì?** 
   - Thư viện bọc WebSocket, thêm nhiều tính năng

5. **Luồng xử lý?** 
   - Event-driven, non-blocking, single-threaded

6. **Multicast?** 
   - **Có** - Broadcasting to rooms

7. **Multithreading?** 
   - **Không** - Node.js single-threaded, dùng Event Loop

---

**File này giải thích đầy đủ! Đọc kỹ để hiểu rõ! 📚**
