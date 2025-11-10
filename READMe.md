# 🚀 Redis ORM – A MongoDB-like Query Layer for Redis

[![npm version](https://img.shields.io/npm/v/redis-lite-orm.svg?color=brightgreen)](https://www.npmjs.com/package/redis-lite-orm)
[![license](https://img.shields.io/badge/license-MIT-blue.svg)](./LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](./CONTRIBUTING.md)
[![Node.js](https://img.shields.io/badge/node-%3E%3D16.0-orange.svg)](https://nodejs.org)
[![Redis](https://img.shields.io/badge/redis-7.x-red.svg)](https://redis.io)

> MongoDB-like ORM for Redis with auto-indexing, TTLs, and query filters — powered by ioredis.

---

## ✨ Overview

**Redis ORM** is a lightweight Node.js library that makes Redis feel like **MongoDB** — you can query, update, and delete Redis records using familiar syntax like `.find()`, `.set()`, and `.update()`.

It’s built on top of [ioredis](https://github.com/luin/ioredis) and designed for simplicity, scalability, and developer productivity.

---

## ⚙️ Features

✅ MongoDB-style API: `find`, `findOne`, `set`, `update`, `delete`  
✅ Query operators: `$in`, `$gt`, `$lt`, `$ne`, `$regex`  
✅ **Auto-indexing** using Redis sets for O(1) lookups  
✅ **Query optimization** with Redis `SINTER` for multi-field filtering  
✅ **TTL support** (key expiration)  
✅ Optional **Redis hash mode (`HSET`)** for structured storage  
✅ Plug-and-play **repository pattern**  
✅ Written in modern **ES Modules (import/export)**  

---

## 🧱 Architecture Overview
```text
RedisRepository
├── collection["users"]
│ ├── .set() → Insert / Update
│ ├── .find() → Query with indexes
│ ├── .update() → Modify + reindex
│ ├── .delete() → Remove + cleanup index
│
├── Uses Redis sets for auto-indexing:
│ users:index:role:CTO → [users:1, users:3]
│
└── Built on ioredis
```

---

## 🧩 Installation

```bash
npm install redis-lite-orm
```
Requires Node.js ≥ 16 and a running Redis instance.

---

## 🧠 Usage Example
```js
import redis from "redis-lite-orm";

(async () => {
  // Insert documents
  await redis.collection["users"].set({ userId: 1, name: "Sandeep", role: "CTO", age: 30 });
  await redis.collection["users"].set({ userId: 2, name: "Akhilesh", role: "CEO", age: 40 });
  await redis.collection["users"].set({ userId: 3, name: "John", role: "CTO", age: 25 });

  // Query using indexed fields
  console.log(await redis.collection["users"].find({ role: "CTO" }));

  // Query with operators
  console.log(await redis.collection["users"].find({ age: { $gt: 25 } }));

  // Regex search
  console.log(await redis.collection["users"].find({ name: { $regex: "^S", $options: "i" } }));

  // Update documents
  await redis.collection["users"].update({ userId: 3 }, { role: "Lead Dev" });

  // Delete documents
  await redis.collection["users"].delete({ role: "CEO" });
})();
```

---

## ⚡ Query Operators Supported
| Operator     | Example                                     | Description               |
| ------------ | ------------------------------------------- | ------------------------- |
| `$in`        | `{ role: { $in: ["CTO", "Dev"] } }`         | Matches any value in list |
| `$ne`        | `{ age: { $ne: 30 } }`                      | Not equal                 |
| `$gt`, `$lt` | `{ age: { $gt: 25 } }`                      | Greater / less than       |
| `$regex`     | `{ name: { $regex: "^S", $options: "i" } }` | Regex match               |

---

## 🧮 Performance Benchmark
| Query Type               | Without Index | With Auto Index         |
| ------------------------ | ------------- | ----------------------- |
| `.find({ role: "CTO" })` | O(N)          | **O(1)**                |
| `.find({ role, age })`   | O(N²)         | **O(1)** (via `SINTER`) |

---

## 🕒 TTL Support
You can assign a TTL (in seconds) when setting data:
```js
await redis.collection["users"].set(
  { userId: 100, name: "Temp User" },
  60 // expires after 60 seconds
);
```

---

## ⚙️ Configuration
Default configuration:
```js
const redis = new RedisRepository(client, {
  useHash: false,        // Use Redis hashes (HSET) if true
  ttl: 0,                // Default expiration time (seconds)
  indexes: ["role", "age"] // Auto-indexed fields
});
```
Environment variables:
| Variable         | Default     | Description             |
| ---------------- | ----------- | ----------------------- |
| `REDIS_HOST`     | `127.0.0.1` | Redis host              |
| `REDIS_PORT`     | `6379`      | Redis port              |
| `REDIS_PASSWORD` | —           | Optional Redis password |

---

### 🧰 Example Directory Structure
```text
redis-lite-orm/
├── src/
│   ├── index.js
│   ├── config.js
│   └── redis/
│       ├── collection.js
│       └── repository.js
├── package.json
├── README.md
├── LICENSE
└── CONTRIBUTING.md
```

---

## 🧱 Local Development

```bash
git clone https://github.com/sandeepkhore/redis-lite-orm.git
cd redis-lite-orm
npm install
```
Run locally:
```bash
node example.js
```
Ensure Redis is running:
```bash
redis-server
```
or via Docker:
```bash
docker run --name redis -p 6379:6379 -d redis
```

---

## 🧩 Contributing
Contributions are welcome 💜

Please read the CONTRIBUTING.md
 before submitting a pull request.

Quick Steps
1. Fork the repo
2. Create a feature branch (git checkout -b feature/add-pagination)
3. Commit changes (git commit -m "feat: add pagination support")
4. Push to your fork
5. Open a Pull Request 🚀

---

## 🧠 Roadmap
- Pagination (limit, skip)
- .count() and .exists() helpers
- Compound indexes
- Batch operations (pipelines)
- Schema validation layer
- TypeScript type definitions

---

## 🧑‍💻 Author
Sandeep Khore
⚡ Building GenAI & scalable backend systems
📬 LinkedIn
 • GitHub

## 🪪 License
MIT License © 2025 Sandeep Khore


