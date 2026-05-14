# Notification System Design & Implementation

## Stage 1: API Design
To support a robust notification system, we need RESTful endpoints that allow clients to fetch, filter, and update notifications.

**1. Fetch Notifications**
- `GET /api/notifications`
- **Query Parameters:**
  - `userId` (required): Fetch notifications for a specific user.
  - `isRead` (optional, boolean): Filter by read/unread status.
  - `type` (optional, string): Filter by type (e.g., placement, event, general).
  - `limit` & `offset` (optional): For pagination.
- **Response:** Array of Notification objects.

**2. Priority Inbox**
- `GET /api/notifications/priority`
- **Query Parameters:**
  - `userId` (required)
  - `limit` (optional, default 10)
- **Response:** Top N notifications sorted by priority score and recency.

**3. Mark as Read**
- `PATCH /api/notifications/:id/read`
- **Body:** `{ "isRead": true }`
- **Response:** Updated Notification object.

---

## Stage 2: Database Schema & Justification
Assuming a NoSQL Database (MongoDB) is used as it provides flexibility for varying notification payloads.

**Schema (Notification):**
- `_id`: ObjectId (Primary Key)
- `userId`: String (Indexed for fast retrieval per user)
- `type`: String (Enum: 'placement', 'event', 'general')
- `message`: String
- `link`: String (URL to navigate when clicked)
- `isRead`: Boolean (Default: false, Indexed for filtering)
- `timestamp`: Date (Default: Date.now, Indexed for sorting)

**Justification:** 
- **Read-Heavy Nature:** Notification systems are typically read-heavy. Indexing `userId`, `isRead`, and `timestamp` allows for O(log N) query performance when fetching a user's unread notifications.
- **Flexibility:** A document database allows us to easily add new fields (like `metadata` for specific event types) without schema migrations.

---

## Stage 3: SQL Optimization (If using Relational DB)
If we were using PostgreSQL or MySQL, the query to fetch unread notifications would be:
`SELECT * FROM notifications WHERE user_id = ? AND is_read = false ORDER BY timestamp DESC LIMIT 20;`

**Optimizations:**
1. **Composite Index:** Create a composite index on `(user_id, is_read, timestamp DESC)`. This allows the database to instantly locate the rows and return them already sorted, avoiding a full table scan and an expensive "filesort" operation.
2. **Pagination:** Use Keyset Pagination (cursor-based) instead of `OFFSET` to prevent performance degradation on deep pages.

---

## Stage 4: Performance Improvements
To handle high traffic loads (e.g., thousands of students checking notifications simultaneously):
1. **Caching:** Introduce Redis to cache the top N unread notifications for active users. The cache is invalidated or updated asynchronously when a new notification is generated or marked as read.
2. **Database Read Replicas:** Route `GET` requests to read replicas to reduce the load on the primary writer database.
3. **Connection Pooling:** Ensure the backend maintains a pool of database connections to reduce the overhead of establishing new connections per request.

---

## Stage 5: Solving the Bottleneck (Email/In-App Delay)
**Problem:** Sending emails synchronously while generating in-app notifications causes bottlenecks.
**Solution:** Decouple the processes using an Event-Driven Architecture and Message Queues (e.g., RabbitMQ, Kafka, or AWS SQS).

1. **Producer:** When an event occurs (e.g., new placement drive), the main application simply publishes a `NotificationEvent` to the message queue and immediately responds to the user.
2. **Consumers (Workers):**
   - **In-App Worker:** Consumes the event and writes the notification to the database (fast).
   - **Email Worker:** Consumes the event, formats the email template, and calls the SMTP server/Email Provider (SendGrid/SES).
3. **Batching:** The Email Worker can batch notifications (e.g., "Daily Digest") to prevent spamming users and hitting rate limits.
4. **Retry Mechanism:** If an email fails to send, the message queue can route it to a Dead Letter Queue (DLQ) for retries with exponential backoff.
