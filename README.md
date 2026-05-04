# Million Checkboxes Clone (Real-Time Synchronized Checkboxes)

![Node.js](https://img.shields.io/badge/Node.js-43853D?style=for-the-badge&logo=node.js&logoColor=white)
![Express.js](https://img.shields.io/badge/Express.js-404D59?style=for-the-badge)
![Socket.io](https://img.shields.io/badge/Socket.io-black?style=for-the-badge&logo=socket.io&badgeColor=010101)
![Redis](https://img.shields.io/badge/Valkey%20(Redis)-DC382D?style=for-the-badge&logo=redis&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2CA5E0?style=for-the-badge&logo=docker&logoColor=white)

A real-time, highly concurrent application where users can collaboratively toggle checkboxes in sync. Built as a scalable clone of the popular "Million Checkboxes" concept, this project uses a powerful real-time tech stack to ensure state is synchronized across all connected clients instantly.

## ✨ Features

- **Real-Time Synchronization**: Toggled checkboxes update instantly across all connected clients using WebSockets.
- **Robust State Persistence**: Checkbox states are saved persistently in an in-memory data store using Valkey (Redis alternative).
- **Scalable Architecture**: Utilizes Redis Pub/Sub (`internal-server:checkbox:change`) allowing the application to scale across multiple instances seamlessly.
- **Rate Limiting**: Built-in 5.5-second rate limiting per client to prevent spamming and abuse.
- **Dockerized Data Store**: Includes a convenient Docker Compose configuration to easily spin up the required Valkey instance.

## 🛠️ Technology Stack

- **Backend**: Node.js, Express.js
- **Real-Time Communication**: Socket.IO
- **Data Store & Pub/Sub**: Valkey (via `ioredis` Node client)
- **Frontend**: Vanilla HTML/JS/CSS served statically

## 🚀 Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/en/) (v18+ recommended)
- [pnpm](https://pnpm.io/) (`npm i -g pnpm`)
- [Docker Compose](https://docs.docker.com/compose/) (to run the local Valkey instance)

### Installation & Setup

1. **Clone the repository:**
   ```bash
   git clone <your-repo-url>
   cd checkbox2
   ```

2. **Install dependencies:**
   ```bash
   pnpm install
   ```

3. **Start the Data Store (Valkey):**
   ```bash
   docker-compose up -d
   ```
   *This spins up a Valkey container on port 6379, which acts as a drop-in replacement for Redis.*

4. **Run the Application:**
   ```bash
   pnpm start
   ```
   *The server will start on `http://localhost:7000`.*

5. **Open your browser:**
   Navigate to `http://localhost:7000` in multiple tabs or browsers to see the real-time sync in action!

## 📂 Project Structure

```
.
├── index.js               # Main application entry point (Express & Socket.IO setup)
├── redis-connection.js    # Valkey/Redis connection & Pub/Sub setup
├── docker-compose.yml     # Docker configuration for Valkey service
├── package.json           # Project metadata and scripts
└── public/
    └── index.html         # Frontend client file
```

## 🧠 How it Works

1. When a user connects, the server fetches the current state of the checkboxes from Valkey and sends it via HTTP to the client (`/checkboxes`).
2. When a user toggles a checkbox, the frontend emits a `client:checkbox:change` event over WebSockets.
3. The server checks the **Rate Limiter** using an in-memory map based on the Socket ID.
4. If allowed, the server:
   - Updates the global checkbox state array in Valkey.
   - Publishes the change to the `internal-server:checkbox:change` Redis channel.
5. The server's Redis subscriber picks up the published message and broadcasts the state change to all connected Socket.IO clients, updating their UI instantly.

## 🤝 Contributing

Contributions, issues, and feature requests are welcome! Feel free to check the [issues page](#) if you want to contribute.

## 📝 License

This project is licensed under the [ISC License](LICENSE).
