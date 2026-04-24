# 🍱 Daanpath — Path of Giving

> *Real-time food rescue platform connecting donors, volunteers & NGOs — because no meal should go to waste*

[![MIT License](https://img.shields.io/badge/License-MIT-green.svg)](https://choosealicense.com/licenses/mit/)
[![Node.js](https://img.shields.io/badge/Node.js-18+-339933?logo=node.js)](https://nodejs.org/)
[![MongoDB](https://img.shields.io/badge/MongoDB-Atlas-47A248?logo=mongodb)](https://mongodb.com/)
[![Socket.io](https://img.shields.io/badge/Socket.io-Real--time-010101?logo=socket.io)](https://socket.io/)

---

## 🌍 The Problem

Every day, tonnes of food go to waste at weddings, restaurants, and events across India — while millions go to bed hungry. Existing solutions are either manual (phone calls, WhatsApp groups) or too slow to act on perishable surplus in time.

**Daanpath solves this with a real-time dispatch engine** — the moment food is listed, the nearest available volunteer is automatically matched and dispatched, just like Uber does for rides.

---

## ✨ Features

### Core
- 📍 **Geospatial Matching** — finds nearest volunteers using MongoDB `$geoNear` queries
- ⚡ **Real-time Dispatch** — Uber-style volunteer assignment via Socket.io
- 🔄 **Live Status Tracking** — donors track pickup in real time
- 🗓️ **Pre-scheduling** — caterers register food before events, system pre-assigns volunteers
- 📸 **Proof of Delivery** — volunteers upload photo confirmation on pickup and drop

### Roles
| Role | What they do |
|------|-------------|
| 🍽️ Donor | Restaurant, caterer, or individual posting surplus food |
| 🚴 Volunteer | Gets dispatched for nearby pickups and deliveries |
| 🏠 NGO | Verified receiver organization managing incoming food |
| 🛡️ Admin | Verifies users, manages platform, views analytics |

### Impact Tracking
- 📊 Meals saved per donor / volunteer / NGO
- 🌱 CO₂ saved from food waste
- 🏆 Volunteer reputation and reliability scoring

### AI Integration
- 🤖 Food safety check on uploaded photos
- 🧮 Auto meal-count estimation from quantity description
- 🔔 Smart expiry alerts with auto-broadcast to wider volunteer radius

---

## 🛠️ Tech Stack

### Backend
| Technology | Purpose |
|------------|---------|
| Node.js + Express | REST API server |
| MongoDB + Mongoose | Database with geospatial indexes |
| Socket.io | Real-time WebSocket communication |
| Redis | Volunteer location caching + race condition prevention |
| JWT | Authentication with refresh token rotation |
| Cloudinary | Image uploads (food photos, delivery proof) |
| node-cron | Listing expiry and scheduled reminders |

### Frontend
| Technology | Purpose |
|------------|---------|
| React.js | UI framework |
| Google Maps API | Location picking and nearby search |
| Socket.io Client | Real-time updates |

### DevOps
| Technology | Purpose |
|------------|---------|
| Docker + docker-compose | Local development environment |
| Render | Backend deployment |
| Vercel | Frontend deployment |

---

## 🏗️ System Architecture

```
Client (React)
     │
     ├── REST API calls ──────────────► Express Server (Node.js)
     │                                        │
     └── WebSocket (Socket.io) ◄──────────────┤
                                              │
                                    ┌─────────┴──────────┐
                                    │                    │
                                 MongoDB              Redis
                             (data + geo)      (cache + locks)
                                    │
                                Cloudinary
                              (image storage)
```

---

## 🚀 Getting Started

### Prerequisites
- Node.js 18+
- MongoDB Atlas account (free tier works)
- Cloudinary account (free tier works)
- Redis (local or Redis Cloud free tier)

### Installation

**1. Clone the repository**
```bash
git clone https://github.com/your-username/daanpath.git
cd daanpath
```

**2. Setup the server**
```bash
cd server
npm install
```

**3. Configure environment variables**
```bash
cp .env.example .env
```

Fill in your `.env` file:
```env
PORT=8000
MONGODB_URI=your_mongodb_connection_string
JWT_SECRET=your_jwt_secret
JWT_REFRESH_SECRET=your_refresh_secret
NODE_ENV=development
CORS_ORIGIN=http://localhost:5173
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret
```

**4. Run the development server**
```bash
npm run dev
```

Server runs at `http://localhost:8000`

**5. Setup the client**
```bash
cd ../client
npm install
npm run dev
```

Client runs at `http://localhost:5173`

---

## 📁 Project Structure

```
daanpath/
├── server/
│   ├── config/
│   │   ├── db.js               # MongoDB connection
│   │   └── cloudinary.js       # Cloudinary config
│   ├── controllers/
│   │   ├── auth.controller.js
│   │   ├── listing.controller.js
│   │   ├── dispatch.controller.js
│   │   ├── user.controller.js
│   │   └── impact.controller.js
│   ├── middleware/
│   │   ├── auth.middleware.js   # JWT verification
│   │   ├── role.middleware.js   # Role-based access
│   │   └── error.middleware.js
│   ├── models/
│   │   ├── User.model.js
│   │   ├── FoodListing.model.js
│   │   ├── Dispatch.model.js
│   │   ├── Notification.model.js
│   │   └── ImpactLog.model.js
│   ├── routes/
│   │   ├── auth.routes.js
│   │   ├── listing.routes.js
│   │   ├── dispatch.routes.js
│   │   ├── user.routes.js
│   │   └── impact.routes.js
│   ├── socket/
│   │   └── socket.js           # Socket.io logic
│   ├── utils/
│   │   ├── asyncHandler.js
│   │   ├── ApiError.js
│   │   └── ApiResponse.js
│   ├── .env.example
│   └── index.js
│
└── client/
    ├── src/
    │   ├── components/
    │   ├── pages/
    │   ├── context/
    │   └── App.jsx
    └── index.html
```

---

## 🔌 API Reference

### Auth
| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| POST | `/api/auth/register` | Register new user | ❌ |
| POST | `/api/auth/login` | Login and get tokens | ❌ |
| POST | `/api/auth/logout` | Logout and clear tokens | ✅ |
| POST | `/api/auth/refresh-token` | Get new access token | ❌ |
| GET | `/api/auth/me` | Get current user | ✅ |

### Listings
| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| POST | `/api/listings` | Create food listing | ✅ Donor |
| GET | `/api/listings/nearby` | Get nearby listings (geo) | ✅ |
| GET | `/api/listings/:id` | Get single listing | ✅ |
| PATCH | `/api/listings/:id/status` | Update listing status | ✅ |
| DELETE | `/api/listings/:id` | Delete listing | ✅ Donor |

### Dispatch
| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| POST | `/api/dispatch/assign` | Auto-assign volunteer | ✅ System |
| PATCH | `/api/dispatch/:id/accept` | Volunteer accepts | ✅ Volunteer |
| PATCH | `/api/dispatch/:id/picked` | Mark as picked up | ✅ Volunteer |
| PATCH | `/api/dispatch/:id/delivered` | Mark as delivered | ✅ Volunteer |
| GET | `/api/dispatch/volunteer/:id` | Volunteer's dispatches | ✅ |

### Impact
| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| GET | `/api/impact/platform` | Platform-wide stats | ❌ |
| GET | `/api/impact/donor/:id` | Donor impact stats | ✅ |
| GET | `/api/impact/volunteer/:id` | Volunteer impact stats | ✅ |
| GET | `/api/impact/ngo/:id` | NGO impact stats | ✅ |

---

## ⚙️ Key Technical Decisions

### Why Redis for volunteer location caching?
Volunteer locations update every few seconds via Socket.io. Hitting MongoDB on every location update would be expensive. Redis stores live locations in memory with sub-millisecond reads, and we only persist to MongoDB periodically.

### How race conditions are prevented in dispatch?
When a listing is created, multiple volunteers might be matched simultaneously. We use Redis `SET NX` (set if not exists) as a distributed lock — only one volunteer can claim a listing, even across multiple server instances.

### Why MongoDB geospatial indexes?
MongoDB's `2dsphere` index enables `$geoNear` queries that find and sort volunteers by distance in a single database operation — no application-level filtering needed.

---

## 👥 Team

| Name | Role |
|------|------|
| Person A | Backend Core — Auth, Listings, Dispatch Engine |
| Person B | Backend Support — Socket.io, Notifications, Deployment |

---

## 🤝 Contributing

Contributions are welcome. Please open an issue first to discuss what you would like to change.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'feat: add some amazing feature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgements

Built with the belief that technology can bridge the gap between surplus and scarcity. Inspired by the Indian value of **Annadaan** — the gift of food.

> *"Annadanam Param Danam"* — the gift of food is the greatest gift.
