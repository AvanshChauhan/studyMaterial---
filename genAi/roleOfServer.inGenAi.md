# Role of a Server and Authenticating to an AI Service Provider (ASP)

## What is a Server?

A server is the backend application that handles business logic, security, database operations, and communication with external services such as AI providers.

The server acts as an intermediary between the client (frontend) and external APIs.

### Responsibilities of a Server

* User authentication and authorization
* Database operations
* Business logic
* API integrations
* Data validation
* Security enforcement
* Rate limiting
* Logging and monitoring

### Architecture

```text
Client (Web/Mobile App)
           │
           ▼
       Server
           │
           ▼
 AI Service Provider
           │
           ▼
      AI Model
```

---

# Why Not Call the AI Provider Directly from the Frontend?

Suppose you have a Gemini API key:

```text
AIzaSyXXXXXXXXXXXX
```

If you place this key in frontend code:

```javascript
const model = new ChatGoogleGenerativeAI({
  apiKey: "AIzaSyXXXXXXXXXXXX"
});
```

Anyone can inspect the browser and steal the key.

This can lead to:

* Unauthorized usage
* Increased costs
* Security breaches
* Rate-limit exhaustion

Therefore API keys should always remain on the server.

---

# Role of the Server in AI Applications

The server:

1. Receives the user request.
2. Validates the user.
3. Applies business rules.
4. Calls the AI provider.
5. Processes the response.
6. Returns the final result.

### Example Flow

```text
User asks:
"Create a workout plan"

        │
        ▼

Frontend sends request

        │
        ▼

Backend Server

        │
        ├─ Verify User
        ├─ Check Subscription
        ├─ Check Rate Limits
        └─ Build Prompt

        ▼

AI Provider API

        ▼

Model Generates Response

        ▼

Server Processes Output

        ▼

Frontend Displays Result
```

---

# What is Authentication?

Authentication is the process of proving identity.

When a server communicates with an AI provider, it must prove that it is an authorized customer.

This is usually done using:

* API Keys
* OAuth Tokens
* Service Accounts
* Access Tokens

---

# Authenticating to an AI Service Provider (ASP)

## Step 1: Obtain Credentials

After creating an account with an AI provider, you receive credentials.

Example:

```text
API_KEY=abc123xyz
```

These credentials identify your application.

---

## Step 2: Store Credentials Securely

Never hardcode credentials.

Use environment variables:

```env
GOOGLE_API_KEY=abc123xyz
```

---

## Step 3: Load Credentials in the Server

```javascript
import dotenv from "dotenv";

dotenv.config();

const apiKey = process.env.GOOGLE_API_KEY;
```

---

## Step 4: Create the AI Client

```javascript
const model = new ChatGoogleGenerativeAI({
  model: "gemini-3.1-flash",
  apiKey: process.env.GOOGLE_API_KEY
});
```

At this point the server is authenticated.

---

## Step 5: Send Requests

```javascript
const response = await model.invoke(
  "Create a vegetarian diet plan"
);
```

The request contains authentication information.

The AI provider verifies:

* API key validity
* Account status
* Quotas
* Billing information
* Permissions

---

## Authentication Flow

```text
Backend Server
      │
      │ API Key
      ▼
AI Provider
      │
      ├─ Validate Key
      ├─ Check Billing
      ├─ Check Quota
      └─ Check Permissions
      ▼
AI Model
      ▼
Response
```

---

# Complete AI Application Flow

```text
User
 │
 ▼
Frontend (React/Next.js)
 │
 ▼
Backend Server (Node.js)
 │
 ├─ Authentication
 ├─ Authorization
 ├─ Database Access
 ├─ Rate Limiting
 └─ AI Integration
 │
 ▼
AI Service Provider
 │
 ▼
LLM (Gemini/GPT/Claude)
 │
 ▼
Generated Response
 │
 ▼
Backend Server
 │
 ▼
Frontend
 │
 ▼
User
```

---

# Key Takeaways

* The server protects API keys and business logic.
* Frontends should never expose AI provider credentials.
* Authentication proves that your server is allowed to use the AI provider's services.
* AI providers typically use API keys, OAuth tokens, or service accounts.
* Every AI request is usually routed through the backend server before reaching the model.
* The server is responsible for security, validation, logging, rate limiting, and communication with external services.
