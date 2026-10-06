# Whatsapp-backend

A real-time chat backend inspired by WhatsApp, built with Node.js, Express, Socket.IO and PostgreSQL.

## Features

- [ ] User registration and login (JWT)
- [ ] One-to-one real-time chat
- [ ] Message history with pagination
- [ ] Message status (sent, delivered, read)
- [ ] Online / last seen
- [ ] Group chats
- [ ] Media sharing
- [ ] Typing indicators and push notifications

## Tech Stack

| Part | Technology |
|---|---|
| Server | Node.js, Express |
| Real-time | Socket.IO (WebSockets) |
| Database | PostgreSQL |
| Cache / presence | Redis |
| Auth | JWT, bcrypt |
| Deployment | Docker |

## Architecture

```mermaid
flowchart LR
    A[Client A] <-->|WebSocket| S[Node.js Server]
    B[Client B] <-->|WebSocket| S
    S --> DB[(PostgreSQL)]
    S --> R[(Redis)]
```

## Database ER Diagram

```mermaid
erDiagram
    USERS ||--o{ CONVERSATION_MEMBERS : joins
    CONVERSATIONS ||--o{ CONVERSATION_MEMBERS : has
    CONVERSATIONS ||--o{ MESSAGES : contains
    USERS ||--o{ MESSAGES : sends
    MESSAGES ||--o{ MESSAGE_STATUS : tracks
    USERS ||--o{ MESSAGE_STATUS : receives
    MESSAGES |o--o{ MESSAGES : replies_to

    USERS {
        uuid id PK
        string phone UK
        string name
        string avatar_url
        string about
        timestamp last_seen
        timestamp created_at
    }
    CONVERSATIONS {
        uuid id PK
        string type
        timestamp created_at
    }
    CONVERSATION_MEMBERS {
        uuid conversation_id PK
        uuid user_id PK
        string role
        timestamp joined_at
    }
    MESSAGES {
        uuid id PK
        uuid conversation_id FK
        uuid sender_id FK
        text content
        string type
        string media_url
        uuid reply_to_id FK
        timestamp created_at
    }
    MESSAGE_STATUS {
        uuid message_id PK
        uuid user_id PK
        string status
        timestamp updated_at
    }
```

## API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| POST | `/auth/register` | Create account |
| POST | `/auth/login` | Login and get JWT |
| GET | `/users/me` | Current user profile |
| GET | `/conversations` | List conversations |
| POST | `/conversations` | Create direct or group chat |
| GET | `/conversations/:id/messages` | Message history |

## WebSocket Events

- Client to server: `message:send`, `message:read`, `typing:start`, `typing:stop`
- Server to client: `message:new`, `message:status`, `typing`, `presence:update`

## Getting Started

```bash
git clone https://github.com/<your-username>/Whatsapp-backend.git
cd Whatsapp-backend
npm install
cp .env.example .env
npm run dev
```

Server runs at `http://localhost:5000`. Check `http://localhost:5000/health`.

## Folder Structure

```
src/
├── config/
├── models/
├── routes/
├── controllers/
├── services/
├── sockets/
├── middleware/
└── utils/
```

## Contributing

1. Fork the repo or create a branch: `git checkout -b feature/your-feature`
2. Commit your changes: `git commit -m "feat: your message"`
3. Push and open a Pull Request

## License

MIT
