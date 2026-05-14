# Notification System

A complete MERN stack web application built for the Campus Hiring Evaluation - Full Stack problem statement.

## 🚀 Features

- **Global Logging Middleware:** Logs method, URL, timestamp, and status code for all requests. Also forwards logs to the evaluation API.
- **RESTful Backend:** Built with Node.js, Express, and MongoDB.
- **Automatic DB Seeding:** Pulls notifications from the evaluation API on startup using the Auth token.
- **Priority Inbox (Stage 6):** Custom algorithm scoring notifications based on type (Placement > Event) and recency.
- **Modern React Frontend:** Built with Vite. Features a premium UI with dark mode, glassmorphism, and smooth animations.

---

## 🛠️ Tech Stack
- **Backend:** Node.js, Express.js, MongoDB, Mongoose
- **Frontend:** React, Vite, Axios, React Router, Lucide-React (Icons)
- **Styling:** Vanilla CSS (Premium Aesthetic)

---

## ⚙️ Setup Instructions

### 1. Database Setup
You will need a MongoDB connection. The app is pre-configured to look for a local MongoDB instance at `mongodb://127.0.0.1:27017/notifications-db`. 

### 2. Backend Setup
1. Navigate to the `/notification_app_be` directory:
   ```bash
   cd notification_app_be
   ```
2. Install dependencies:
   ```bash
   npm install
   ```
3. Open `.env` and fill in your evaluation credentials:
   ```env
   PORT=5000
   MONGO_URI=mongodb://127.0.0.1:27017/notifications-db
   CLIENT_ID=your_evaluation_client_id
   CLIENT_SECRET=your_evaluation_client_secret
   ```
4. Start the backend:
   ```bash
   npm start
   ```

### 3. Frontend Setup
1. Open a new terminal and navigate to the `/notification_app_fe` directory:
   ```bash
   cd notification_app_fe
   ```
2. Install dependencies:
   ```bash
   npm install
   ```
3. Start the Vite development server:
   ```bash
   npm run dev
   ```
4. Open the link provided by Vite (usually `http://localhost:5173`) in your browser.

---

## 📝 Design Document
The answers to Stages 1-5 (API Design, Database Schema, SQL Optimization, Performance, and Bottleneck Solving) are located in the root directory: `notification_system_design.md`.
