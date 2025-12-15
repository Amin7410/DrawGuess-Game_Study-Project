# 🔌 Detailed Explanation: Socket, WebSocket, Socket.IO

## 📚 Table of Contents
1. [What is a Socket?](#1-what-is-a-socket)
2. [TCP vs UDP](#2-tcp-vs-udp)
3. [What is a WebSocket?](#3-what-is-a-websocket)
4. [What is Socket.IO?](#4-what-is-socket.IO)
5. [Processing Threads](#5-processing-threads)
6. [Multicast & Multithreading](#6-multicast--multithreading)

---
## 1. 🔌 What is a Socket?

### Definition:
**Socket** = The endpoint of a network connection between two computers.

### Simple Example:
```
Socket = Power outlet
- Computer A has 1 power outlet (socket)
- Computer B has 1 power outlet (socket)
- Power cord = Network connection
- Plug in → Communication is possible
```

### In Programming:
```javascript
// Socket = Object representing a connection
socket.id = "abc123" // Unique ID
socket.emit('message', data) // Send data
socket.on('message', callback) // Receive data
```

### Sockets in a Project:
```javascript
// Server: Each client connection = 1 socket
gameNamespace.on('connection', (socket) => {
console.log('Socket ID:', socket.id); // Each player has 1 ID

socket.on('join-game', (data) => {

// Handle when a player joins

});

});

```

---
## 2. 🔀 TCP vs UDP

### 2.1. TCP (Transmission Control Protocol)

**Characteristics**:

- ✅ **Reliable**: Ensures data reaches its destination
- ✅ **Ordered**: Data arrives in the correct order
- ✅ **Connection-oriented**: Requires establishing a connection beforehand
- ❌ **Slower than UDP**: Due to the need for checking and retrying

**How ​​it Works**:

```
Client Server

| |

|--- SYN ----------------->| (Requesting connection)

|<-- SYN-ACK --------------| (OK, agree)

|--- ACK ----------------->| (Confirmation)

| |
|=== CONNECTED ============|

| |

|--- Data packet 1 ------->|

|<-- ACK ------------------| (Packet 1 received)

|--- Data packet 2 ------->|

|<-- ACK ------------------| (Packet 2 received)
```

**Example**: HTTP, HTTPS, WebSocket (using TCP)

### 2.2. UDP (User Datagram Protocol)

**Characteristics**:

- ✅ **Fast**: No need to check/retry
- ❌ **Unreliable**: Data may be lost
- ❌ **Unordered**: Data arrives out of order
- ✅ **Connectionless**: No need to establish a connection

**How ​​it works**:

```
Client Server

| |

|--- Data packet 1 ------->| (Send immediately, no need to worry)

|--- Data packet 2 ------->|

|--- Data packet 3 ------->|

| |

(No ACK, don't know if received)
```

**Example**: Video streaming, Gaming (FPS games), DNS

### 2.3. What does this project use?

**Uses TCP** (via WebSocket):

- ✅ Needs to ensure data reaches its destination (chat, drawing)
- ✅ Needs to be in order (draw strokes in order)
- ✅ Doesn't need to be extremely fast

```javascript
// WebSocket using TCP
const io = new Server(server, {
transports: ['websocket', 'polling'] // Both use TCP
});

```

---
## 3. 🌐 What is a WebSocket?

### Definition:
**WebSocket** = A protocol that allows **two-way** (bidirectional) communication between a client and a server via a **single connection**.

### Comparison of HTTP vs. WebSocket:

#### HTTP (Request-Response):

Client Server

| |

|--- Request ------------->| (Requests data)

|<-- Response -------------| (Returns data)

| |

--- Request ------------->| (Requests data again)

|<-- Response -------------| (Returns data)

| |

(A new connection must be established each time)

#### WebSocket (Persistent Connection):

Client Server

| |

|--- Handshake ----------->| (Requests an upgrade to WebSocket)

|<-- Upgrade OK -----------| (OK, upgrade)

| |

=== CONNECTED ============| (Continuous connection)

| |

|--- Message 1 ----------->|

|<-- Message 2 ------------|

|--- Message 3 ----------->|

|<-- Message 4 ------------|

| |

(Connection remains open, no need to re-establish)

```
### WebSocket Handshake:

**Step 1: Client sends a special HTTP request**
```http
GET /game HTTP/1.1
Host: drawguess-game.onrender.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13
```

**Step 2: Server responds**
```http
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
```

**Step 3: WebSocket connection is established** establish**
```
Client <======= WebSocket Connection =======> Server 
(Bidirectional, Persistent)
```

### In Code:

**Server** (`index.js`):
```javascript
const http = require('http');
const { Server } = require('socket.io');

const server = http.createServer(app); // HTTP server
const io = new Server(server, { // WebSocket server 
cors: { 
origin: allowedOrigins, 
methods: ["GET", "POST"] 
}
});

// WebSocket will automatically upgrade from HTTP
```
**Client** (`app.js`):
```javascript
const socket = io(serverUrl + '/game', {
transports: ['websocket', 'polling'] // Prioritize WebSocket
});

// Socket.IO automatically:

// 1. Send HTTP Request with Upgrade header
// 2. Get 101 Switching Protocols
// 3. Establish WebSocket connection
```

---
## 4. 🚀 What is Socket.IO?

### Definition:
**Socket.IO** = A JavaScript library **wrapping** around WebSocket, providing additional features.

### WebSocket vs Socket.IO:

| Features | WebSocket | Socket.IO |

|-----------|-----------|-----------|

| **Protocol** | Standard Protocol | Library |

| **Fallback** | ❌ No | ✅ Polling if WebSocket fails |

| **Auto-reconnect** | ❌ Must be coded manually | ✅ Automatic |

| **Rooms** | ❌ None | ✅ Available |

| **Namespaces** | ❌ None | ✅ Available |

**Events** | ❌ Message only | ✅ Custom events |

**Acknowledgements** | ❌ None | ✅ Callback available |

### Socket.IO = WebSocket + Many Features:

```
┌─────────────────────────────────────┐
│ Socket.IO Library │
│ - Auto-reconnection │
│ - Fallback to polling │
│ - Rooms & Namespaces │
│ - Custom events │
│ - Acknowledgments │
│ - Broadcasting │
└───────────────┬──────────────────────┘

│ 

▼
┌─────────────────────────────────────┐
│ WebSocket Protocol │
│ - Bidirectional communication │
│ - Persistent connection │
│ - Low latency │
└───────────────┬──────────────────────┘

│ 

▼
┌─────────────────────────────────────┐
│ TCP │
│ - Reliable, ordered delivery │
└──────────────────────────────────────┘
```

### Socket.IO Features in the Project:

#### 1. **Namespaces**
```javascript
// Server
const gameNamespace = io.of('/game'); // Namespace for the game
const adminNamespace = io.of('/admin'); // Namespace for the admin

// Client
const socket = io(serverUrl + '/game'); // Connect to /game
```

#### 2. **Rooms** (Rooms)
```javascript
// Server: Join room
socket.join(roomId); // Player joins the room

// Broadcast to room
socket.to(roomId).emit('draw', data); // Send to room (except sender)
gameNamespace.to(roomId).emit('timer-update', data); // Send to the whole room
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
// Client sends with callback
socket.emit('lobby:create-room', data, (response) => { 
if (response.success) { 
console.log('Room created:', response.roomId); 
}
});

// Server responds via callback
socket.on('lobby:create-room', (data, callback) => { 
const roomId = createRoom(data); 
callback({ success: true, roomId }); // Call callback
});
```

#### 5. **Auto-Reconnection**
```javascript
const socket = io(serverUrl, { 
reconnectionAttempts: 5, // Try to reconnect 5 times 
timeout: 10000 // Timeout 10s
});

socket.on('reconnect', (attemptNumber) => { 
console.log('Reconnected after', attemptNumber, 'attempts');
});
```

---

## 5. 🔄 Processing Flow

### 5.1. Connection Flow

```
┌─────────────────────────────────────────────────────────────────────────┐
│ STEP 1: CLIENT OPENS WEBSITE │
└───────────────┬──────────────────────────────────────────────────┘

│ 

▼
┌────────────────────────────── ───────────────────────────────┐
│ STEP 2: LOAD HTML, CSS, JS │
│ - Browser downloads index.html │
│ - Load app.js, config.js │
│ - Load Socket.IO client library │
└──────────────┬─────────────── ───────────────────────────────┘ 
│
▼
┌─────────────────────────────────────────────────────────────────────┐
│ STEP 3: INITIATE SOCKET.IO CLIENT │
│ const socket = io(serverUrl + '/game', { │
│ transports: ['websocket', 'polling'] │
│ }); │
└──────────────┬─────────────── ───────────────────────────────┘ 
│ 
▼
┌────────────────────────────── ───────────────────────────────┐
│ STEP 4: WEBSOCKET HANDSHAKE │
│ Client: GET /game HTTP/1.1 │
│ Upgrade: websockets │
│ Server: HTTP/1.1 101 Switching Protocols │
│ Upgrade: websocket │
└──────────────┬─────────────── ───────────────────────────────┘ 
│
▼
┌──────────────────────────────────────────────────────────────────────┐
│ STEP 5: CONNECTION SUCCESSFUL │
│ Client: socket.on('connect', () => { │
│ console.log('Connected!', socket.id);│
│ }); │
│ │
│ Server: gameNamespace.on('connection', (socket) => { │
│ console.log('New connection:', socket.id); │
│ }); │
└──────────────────────────────────────────────────────────────────────────┘
```

### 5.2. Room Creation Flow

```
┌────────────────────────────────────────────────────────────────────────┐
│ CLIENT │
└──────────────┬─────────────── ───────────────────────────────┘ 
│ 
│ 1. User enters name, room ID, password 
│ 2. Click "Create Room" 
│ 
▼
┌────────────────────────────── ───────────────────────────────┐
│ socket.emit('lobby:create-room', { │
│ playerName: 'Amin', │
│ roomId: '123', │
│ password: 'abc' │
│ }, (response) => { │
│ // Callback receives response │
│ }); │
└──────────────┬─────────────── ───────────────────────────────┘ 
│ 
│ Event via WebSocket 
│ 
▼
┌────────────────────────────── ───────────────────────────────┐
│ SERVER (mainHandler.js) │
└───────────────┬──────────────────────────────────────────────────┘

│ 

▼
┌────────────────────────────── ───────────────────────────────┐
│ socket.on('lobby:create-room', (data, callback) => { │
│ // 1. Validate data │
│ if (!data.playerName) { │
│ return callback({ success: false, message: 'Error' }); │
│ } │
│ │
│ // 2. Create a new room │
│ const newRoom = new GameRoom(roomId, broadcast, {...}); │
│ rooms.set(roomId, newRoom); │
│ │
│ // 3. Broadcast room list update │
│ broadcastRoomListUpdate(); │
│ │
│ // 4. Call callback │
│ callback({ success: true, roomId }); │
│ }); │
└──────────────┬─────────────── ───────────────────────────────┘ 
│ 
│ Response via WebSocket 
│ 
▼
┌────────────────────────────── ───────────────────────────────┐
│ CLIENT │
│ (response) => { │
│ if (response.success) { │
│ window.location.href = `game?room=${response.roomId}`; │
│ } │
│ } │
└───────────────────────────────────────────────────────────────────────────────┘
```

### 5.3. Drawing Flow

```
┌──────────────────────────────────────────────────────────────────────┐
│ PLAYER 1 (Drawer) │
└───────────────┬───────────────────────────────────────────────────┘
│

│ 1. User draws on the canvas

│ 2. Mouse move event

│ 

▼
┌────────────────────────────── ───────────────────────────────┐
│ function draw(e) { │
│ // Draw locally │
│ drawLocal(x0, y0, x1, y1, color, size); │
│ │
│ // Send to server │
│ socket.emit('draw', { │
│ x0, y0, x1, y1, color, size, tool │
│ }); │
│ } │
└──────────────┬─────────────── ───────────────────────────────┘ 
│ 
│ Event via WebSocket 
│ 
▼
┌────────────────────────────── ───────────────────────────────┐
│ SERVER │
│ socket.on('draw', (data) => { │
│ const player = players.get(socket.id); │
│ │
│ // Broadcast to room (except sender) │
│ socket.to(player.roomId).emit('draw', data); │
│ }); │
└──────────────┬─────────────── ───────────────────────────────┘ 
│ 
│ Broadcast via WebSocket 
│ 
▼
┌────────────────────────────── ───────────────────────────────┐
│ PLAYER 2, 3, 4... (Guessers) │
│ socket.on('draw', (data) => { │
│ if (!isDrawer) { │
│ drawRemote(data); // Draw on canvas │
│ } │
│ }); │
└──────────────────────────────────────────────────────────────────────────┘
```

### 5.4. Node.js Event Loop

Node.js handles **asynchronous** operations using the **Event Loop**:

```
┌────────────────────────────┐
│ Event Queue │
│ - socket.on('draw') │
│ - socket.on('chat') │

│ - setTimeout callback │

│ - ... │
└──────────┬────────────────┘ 
│ 
▼
┌───────────────────────────┐
│ Event Loop │
│ while (true) { │
│ event = queue.pop(); │
│ event.execute(); │
│ } │
└──────────┬────────────────┘

│

▼
┌────────────────────────────┐
│ Execute Callback │
│ - Logic Processing │

│ - Database Call │

│ - Emit events │
└───────────────────────────┘
```

**Example**:
```javascript
// Event 1: Player A draws
socket.on('draw', (data) => {

// Process immediately, do not block
socket.to(roomId).emit('draw', data);

});

// Event 2: Player B chats (occurs simultaneously)
socket.on('chat-message', (data) => {

// Process immediately, do not wait for Event 1
gameNamespace.to(roomId).emit('chat-message', data);

});

// Both events are processed in parallel (concurrent)

// No multithreading needed!

```

---
## 6. 🔀 Multicast & Multithreading

### 6.1. Multicast in a Project

**Multicast** = Sending messages to **multiple clients at the same time**.

#### Types of Broadcasting:

**1. Unicast** (1-to-1):
```javascript

// Send to a specific client
socket.emit('word-selected', { word: 'cat' });

```

**2. Broadcast** (1-to-many, except sender):
```javascript

// Send to everyone in the room, except the sender
socket.to(roomId).emit('draw', data);

```

**3. Multicast** (1-to-many, including sender):
```javascript
// Send to everyone in the room, including the sender
gameNamespace.to(roomId).emit('timer-update', { timeLeft: 60 });

```

**4. Broadcast All** (1-to-all):

```javascript
// Send to all clients in the namespace
gameNamespace.emit('room-list-update', rooms);

```

#### In Code:

```javascript
// Example: Timer update
function startTimer(onTimerEnd) { 
this.timer = setInterval(() => { 
this.timeLeft--; 

// MULTICAST: Send to all players in the room 
this.broadcast('timer-update', { timeLeft: this.timeLeft }); 
// ↓ 
// gameNamespace.to(roomId).emit(...) 
// ↓ 
// ┌─────────┬─────────┬─────────┐ 
// Player 1 Player 2 Player 3 Player 4

// (All receive at the same time)

}, 1000);

}
```

### 6.2. Multithreading

**Question**: Does the project use multithreading?

**Answer**: **NO** - Node.js is **single-threaded**!

#### Node.js Single-Threaded Model:

```
┌──────────────────────────────────────┐
│ Main Thread (JavaScript) │

│ - Handles all logic │

│ - Event loop │

│ - No blocking │
└───────────────┬──────────────────────┘

│

▼
┌──────────────────────────────────────┐
│ Thread Pool (C++) │
│ - File I/O │
│ - Network I/O │
│ - Crypto operations │
│ (Automatic, no coding required) │
└─────────────────────────────────────┘
```

#### Why Multithreading Is Not Necessary?

**1. Event-Driven Architecture**:
```javascript

// No blocking, asynchronous processing
socket.on('draw', (data) => {

// Fast processing, no waiting
socket.to(roomId).emit('draw', data);

});

// Multiple events processed concurrently

// But only 1 thread!

```
**2. Non-Blocking I/O**:
```javascript
// Does not block the main thread
setTimeout(() => {
startNewRound(room);

}, 5000); // Waits 5 seconds, but does not block

// While waiting, it still processes other events
```

**3. Scalability**:
```
1 Node.js process = Handles 1000+ connections
Reason: Event loop + Non-blocking I/O
```

#### Comparison:

| | Multithreading | Node.js Event Loop |

|---|---|---|

| **Threads** | Multiple threads | 1 main thread |

| **Complexity** | Complex (race conditions) | Simple |

| **Memory** | Many (each thread = 1-2MB) | Few |

| **Context Switching** | Slow | Fast |

| **Scalability** | Limited by threads | Limited by memory |

#### When is Multithreading Needed?

**Needed**:

- CPU-intensive tasks (image processing, video encoding)

- Heavy computation

**Not needed** (like this project):

- I/O-intensive (network, database)
- Real-time communication
- Event-driven applications

---

## 📊 Summary

### Technology Stack:

```
┌─────────────────────────────────────┐
│ Application Layer │
│ - Socket.IO (Custom events) │
│ - Namespaces, Rooms │
└───────────────┬──────────────────────┘

│ 

▼
┌─────────────────────────────────────┐
│ WebSocket Protocol │
│ - Bidirectional │
│ - Persistent connection │
└───────────────┬──────────────────────┘

│ 

▼
┌─────────────────────────────────────┐
│ TCP Protocol │
│ - Reliable, ordered │
└───────────────┬──────────────────────┘

│ 

▼
┌─────────────────────────────────────┐
│ IP Protocol │
│ - Routing, addressing │
└──────────────────────────────────────┘
```

### Short Answers:

1. **What is a Socket?**

- The endpoint of a network connection

2. **TCP or UDP?**

- **TCP** (via WebSocket)

3. **What is a WebSocket?**

- A bidirectional, persistent connection protocol

4. **What is Socket.IO?**

- A library wrapping WebSockets, adding many features

5. **Processing Flow?**

- Event-driven, non-blocking, single-threaded

6. **Multicast?**

- **Yes** - Broadcasting to rooms

7. **Multithreading?**

- **No** - Node.js single-threaded, using the Event Loop

---

**This file explains everything! Read carefully to understand! 📚**
