## 1. **Overview**

### What type of project is this?

This is a **backend REST API** application built with Node.js and Express.js.

### What problem does it solve?

The application provides secure user authentication and registration functionality, with built-in security measures to protect against common web threats like bots, rate limiting, and potential attacks. It is designed for managing user accounts in an "acquisitions" system (the full business logic around acquisitions isn't implemented yet - currently only auth is available).

## 2. **Core Architecture**

### High level structure

The application follows a **layered architecture** with clean separation of concerns:

```
/acquisitions/
├── Config Layer (database, logging, security)
├── Routing Layer
├── Controller Layer
├── Service Layer
├── Model Layer (data schemas)
├── Validation Layer
├── Utils Layer (JWT, cookies, helpers)
└── Middleware Layer (security, logging, CORS)
```

The project currently implements only the authentication subsystem but is structured to easily add more features like acquisitions management.

### Major parts organization

- **Routes**: Define API endpoints (`/api/auth/*`)
- **Controllers**: Handle HTTP requests/response logic
- **Services**: Business logic and database operations
- **Models**: Database schemas using Drizzle ORM
- **Validations**: Input validation with Zod schemas
- **Utils**: Shared utilities (JWT, cookies, formatting)
- **Middleware**: Cross-cutting concerns (security, logging)
- **Config**: Environment-specific configurations

## 3. **Key Components**

### Authentication System (`auth.*`)

- **Routes** (`auth.routes.js`): POST endpoints for sign-up, sign-in, sign-out
- **Controller** (`auth.controller.js`): Handles validation, service calls, responses
- **Service** (`auth.service.js`): Password hashing, user creation, authentication logic
- **Model** (`user.model.js`): User entity with PostgreSQL schema
- **Validation** (`auth.validation.js`): Input schemas with Zod

### Security & Infrastructure

- **ArcJet Security** (`arcjet.js`, `security.middleware.js`): Bot detection, SQL injection protection, role-based rate limiting
- **Database** (`database.js`): Neon PostgreSQL connection via Drizzle ORM
- **Logging** (`logger.js`): Winston-based logging with file and console transports
- **JWT & Cookies** (`jwt.js`, `cookies.js`): Token generation and signed cookie management

## 4. **Data Flow and Communication**

### User Sign-Up Flow

```
Client Request
    ↓
ArcJet Security Check (bot/attack protection)
    ↓
Request Validation (Zod schema)
    ↓
Auth Controller (signUp)
    ↓
Auth Service (createUser)
    ↓
Check existing user in DB
    ↓
Hash password with bcrypt
    ↓
Insert new user via Drizzle ORM
    ↓
Generate JWT token
    ↓
Set HTTP-only signed cookie
    ↓
Return success response
```

### Communication Patterns

- **Synchronous HTTP APIs** using Express router
- **Database communication** via Drizzle query builder
- **Security checks** integrated as middleware
- **Logging** via Winston with structured JSON
- **Error handling** with try-catch and custom error responses

## 5. **Tech Stack and Dependencies**

### Core Framework

- **Express.js v5**: Web framework with modern async support
- **Node.js**: Runtime with ES6 modules

### Database

- **PostgreSQL**: Primary database
- **Neon**: Serverless PostgreSQL platform
- **Drizzle ORM**: TypeScript-first ORM for SQL

### Security & Monitoring

- **ArcJet**: Comprehensive security (shields, bot detection, rate limiting)
- **bcrypt**: Password hashing
- **Helmet**: Security headers
- **CORS**: Cross-origin resource sharing
- **Winston**: Advanced logging framework

### Utilities

- **JWT**: JSON Web Tokens for authentication
- **Cookie-parser**: Signed cookie handling
- **Zod**: Runtime type validation
- **Morgan**: HTTP request logging middleware

### Development Tools

- **ESLint + Prettier**: Code quality and formatting
- **Drizzle Kit**: Database migrations and studio

## 6. **Execution Flow**

### Typical Request Flow

```
User clicks "Sign Up"
    ↓
Client sends POST /api/auth/sign-up
    ↓
Middleware Chain:
  - Helmet (security headers)
  - CORS (cross-origin)
  - Security middleware (ArcJet rate limiting/bot detection)
  - Body parser (JSON parsing)
  - Cookie parser
  - Morgan logging
    ↓
Route handler → Auth controller → Validation
    ↓
Service layer → Password hashing
    ↓
Database query (insert user)
    ↓
JWT token generation
    ↓
Set secure HTTP-only cookie
    ↓
JSON response with user data
```

### Startup Flow

```
npm run dev
    ↓
index.js loads .env, imports server.js
    ↓
server.js imports app.js, starts server on PORT
    ↓
app.js configures middleware, routes
    ↓
Drizzle connects to Neon via DATABASE_URL
```

## 7. **Strengths and Tradeoffs**

### Advantages

- **Security First**: Multiple layers of protection (ArcJet, Helmet, bcrypt, JWT)
- **Scalable Architecture**: Clean separation allowing easy feature addition
- **Modern Standards**: ES6 modules, TypeScript-first tools, serverless DB
- **Production Ready**: Comprehensive logging, error handling, validation
- **Developer Experience**: Hot reload, linting, formatting, import maps

### Limitations & Tradeoffs

- **Single Responsibility Focus**: Only implements auth so far, acquisitions logic not implemented
- **PostgreSQL Only**: Tied to specific database platform
- **Basic Roles**: Only user/admin roles defined, no inheritance or permissions defined yet
- **No API Documentation**: REST endpoints exist but no OpenAPI/Swagger docs

## 8. **Final Summary**

The acquisitions app is a secure, modern Node.js REST API foundation specifically for handling user authentication. It uses a clean layered architecture with signed cookies for auth, PostgreSQL for persistence, and comprehensive security measures including ArcJet bot protection. While it currently only implements the authentication system, the modular structure makes it easy to extend with acquisitions management features. It's built for production use with proper logging, validation, and security but would benefit from added API documentation and fixing the noted security middleware bug.
