# Ledger Service Backend

A robust Node.js/Express backend API for financial ledger management, featuring secure user authentication, account management, and transaction processing with enterprise-grade reliability.

## 🎯 Overview

This backend service provides a complete ledger system that enables users to:
- Create secure accounts with full authentication
- Process financial transactions with idempotency guarantees
- Track account balances with an immutable ledger system
- Receive email notifications for account activities

## 🏗️ Architecture

The application follows a modular MVC-inspired architecture with clear separation of concerns:

```
src/
├── routes/          # API route definitions
├── controllers/     # Business logic handlers
├── services/        # External service integrations (email)
├── middleware/      # Authentication & authorization
├── models/          # MongoDB data schemas
└── config/          # Database configuration
```

## 🚀 Tech Stack

- **Runtime**: Node.js
- **Framework**: Express.js 5.2.1
- **Database**: MongoDB with Mongoose 9.1.5
- **Authentication**: JWT (jsonwebtoken 9.0.3) with bcryptjs 3.0.3
- **Email**: Nodemailer 7.0.12
- **Development**: Nodemon with dotenv configuration

## 📋 API Endpoints

### Authentication (`/api/auth`)
- `POST /register` - Register a new user account
- `POST /login` - Authenticate and receive JWT token
- `POST /logout` - Invalidate token with blacklist mechanism

### Accounts (`/api/accounts`)
- `POST /` - Create a new financial account
- `GET /` - Retrieve all accounts for logged-in user
- `GET /balance/:accountId` - Get current account balance

### Transactions (`/api/transactions`)
- `POST /` - Create and process a transaction between accounts
- `POST /system/initial-funds` - System-initiated fund transfers

## � Prerequisites

Before you begin, ensure you have the following installed:
- **Node.js** (v14 or higher) - [Download](https://nodejs.org/)
- **npm** (comes with Node.js)
- **MongoDB** (local or MongoDB Atlas cloud) - [Setup Guide](https://www.mongodb.com/docs/manual/installation/)
- **Git** - [Download](https://git-scm.com/)

## ⚙️ Installation & Setup

### 1. Clone the Repository
```bash
git clone https://github.com/kirtigayatri/Ledger-Service-API.git
cd Ledger-Service-API
```

### 2. Install Dependencies
```bash
npm install
```

### 3. Configure Environment Variables

Create a `.env` file in the root directory and add the following configuration:

```env
# Server Configuration
PORT=3000
NODE_ENV=development

# MongoDB Connection
MONGODB_URI=mongodb://localhost:27017/ledger-service
# Or for MongoDB Atlas:
# MONGODB_URI=mongodb+srv://<username>:<password>@<cluster>.mongodb.net/ledger-service

# JWT Configuration
JWT_SECRET=your_super_secret_jwt_key_here
JWT_EXPIRY=3d

# Email Configuration (Nodemailer)
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
EMAIL_USER=your_email@gmail.com
EMAIL_PASSWORD=your_app_password
EMAIL_FROM=noreply@ledgerservice.com
```

**Note**: For Gmail, use an [App Password](https://support.google.com/accounts/answer/185833) instead of your regular password.

### 4. Start the Server

**Development Mode** (with auto-restart on file changes):
```bash
npm run dev
```

**Production Mode**:
```bash
npm start
```

The server will start on `http://localhost:3000` by default.

## 🧪 Testing the API

Use **Postman**, **Insomnia**, or **cURL** to test the endpoints.

### Example: Register a User
```bash
curl -X POST http://localhost:3000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "email": "user@example.com",
    "name": "John Doe",
    "password": "SecurePassword123"
  }'
```

### Example: Login
```bash
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "user@example.com",
    "password": "SecurePassword123"
  }'
```

Use the returned JWT token in the `Authorization` header for protected endpoints:
```bash
curl -X GET http://localhost:3000/api/accounts \
  -H "Authorization: Bearer <your_jwt_token>"
```

## �🔒 Security Features

- **Password Security**: Bcrypt hashing with salt rounds (10)
- **JWT Authentication**: 3-day token expiration with refresh capability
- **Token Blacklisting**: Logout invalidates tokens to prevent reuse
- **Idempotency Keys**: Prevents duplicate transactions
- **Input Validation**: Email regex validation and required field checks
- **Protected Routes**: Authentication middleware on all sensitive endpoints
- **Immutable Ledger**: Ledger entries cannot be modified or deleted after creation

## 💳 Transaction Processing

The system implements a 10-step transaction flow ensuring data integrity:

1. Request validation
2. Idempotency key verification
3. Account status verification
4. Balance derivation from ledger
5. Transaction creation (PENDING status)
6. DEBIT ledger entry creation
7. CREDIT ledger entry creation
8. Transaction status update (COMPLETED)
9. Database session commit
10. Email notification dispatch

**Transaction States**: PENDING → COMPLETED | FAILED | REVERSED

## 📊 Data Models

### User
- Email (unique, validated)
- Name
- Password (hashed)
- System user flag
- Timestamps

### Account
- User reference
- Status (ACTIVE/INACTIVE)
- Balance calculation from ledger
- Timestamps

### Transaction
- From/To account references
- Amount (non-negative)
- Status tracking
- Idempotency key (unique)
- Timestamps

### Ledger Entry
- Account reference
- Amount
- Type (CREDIT/DEBIT)
- Transaction reference
- **Immutable** once created

## 🚦 Getting Started

### Prerequisites
- Node.js (v14+)
- MongoDB instance
- Environment variables configured

### Environment Setup

Create a `.env` file in the project root:

```env
MONGO_URI=mongodb://your_mongo_connection_string
JWT_SECRET=your_jwt_secret_key
NODE_ENV=development
```

### Installation

```bash
# Install dependencies
npm install

# Start development server with auto-reload
npm run dev

# Start production server
npm start
```

The server runs on **port 3000** by default.

## 🔍 Project Structure

```
src/
├── config/
│   └── db.js                    # MongoDB connection setup
├── controllers/
│   ├── auth.controller.js       # Authentication logic
│   ├── account.controller.js    # Account management
│   └── transaction.controller.js # Transaction processing
├── middleware/
│   └── auth.middleware.js       # JWT verification & authorization
├── models/
│   ├── user.model.js           # User schema with password hashing
│   ├── account.model.js        # Account schema
│   ├── transaction.model.js    # Transaction schema
│   ├── ledger.model.js         # Immutable ledger schema
│   └── blackList.model.js      # Token blacklist schema
├── routes/
│   ├── auth.routes.js          # Auth endpoints
│   ├── account.routes.js       # Account endpoints
│   └── transaction.routes.js   # Transaction endpoints
├── services/
│   └── email.service.js        # Email notification service
└── app.js                       # Express app configuration
```

## 💡 Key Features

### ✅ Idempotency
- Every transaction requires a unique idempotency key
- Prevents duplicate processing on network retries
- Returns cached result if duplicate request detected

### ✅ Balance Tracking
- Balances calculated dynamically from ledger entries
- Ensures accuracy without denormalization issues
- Query optimization with indexed account references

### ✅ Email Notifications
- Registration confirmation emails
- Transaction completion notifications
- Integrated with Nodemailer

### ✅ User Authentication
- Secure JWT-based authentication
- Cookie and header token support
- Automatic password hashing on save

## 📝 Response Format

All endpoints follow a consistent JSON response format:

```json
{
  "message": "Success/Error message",
  "status": "success/failed",
  "data": {}
}
```

## 🛡️ Error Handling

- **400 Bad Request**: Invalid input or validation errors
- **401 Unauthorized**: Missing or invalid authentication token
- **422 Unprocessable Entity**: Business logic violations (e.g., duplicate user)
- **500 Internal Server Error**: Server-side failures

## 🔐 Authentication Flow

1. User registers with email, password, and name
2. Password is hashed with bcrypt (10 rounds)
3. JWT token generated with 3-day expiration
4. Token stored in HTTP-only cookie
5. Subsequent requests validated against token blacklist
6. Logout adds token to blacklist for revocation

## 🚀 Production Considerations

- Environment-based configuration
- MongoDB connection error handling with process exit
- Input validation on all endpoints
- Transaction status tracking for failure recovery
- Immutable ledger for audit trail compliance
- Token expiration for session management

## 📄 License

ISC

## 👨‍💻 Author

Backend Ledger Service
