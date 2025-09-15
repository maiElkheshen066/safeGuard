# SafeGuard Parent App - Authentication Design Document

## Project Overview
This document provides comprehensive design diagrams for the authentication system of the SafeGuard Parent App, including user registration, login, and forgot password functionality. The diagrams ensure proper flow understanding and system integration for the graduation doctoral project.

---

## Table of Contents
1. [System Architecture Overview](#system-architecture-overview)
2. [User Registration Flow](#user-registration-flow)
3. [User Login Flow](#user-login-flow)
4. [Forgot Password Flow](#forgot-password-flow)
5. [Activity Diagrams](#activity-diagrams)
6. [State Diagrams](#state-diagrams)
7. [Class Diagrams](#class-diagrams)
8. [API Design](#api-design)
9. [Security Considerations](#security-considerations)
10. [Error Handling](#error-handling)

---

## System Architecture Overview

### High-Level Architecture
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Parent App    │    │   Backend API   │    │   Database      │
│   (Flutter)     │◄──►│   (Spring Boot) │◄──►│   (PostgreSQL)  │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         │                       │                       │
         ▼                       ▼                       ▼
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Child App     │    │   Auth Service  │    │   Redis Cache   │
│   (Flutter)     │    │   (JWT/OAuth)   │    │   (Sessions)    │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

---

## User Registration Flow

### Sequence Diagram - User Registration

```mermaid
sequenceDiagram
    participant U as User
    participant UI as React UI
    participant API as Backend API
    participant DB as Database
    participant Email as Email Service
    participant Cache as Redis Cache

    U->>UI: Click "Create Account"
    UI->>U: Show Registration Form
    
    U->>UI: Enter Full Name
    U->>UI: Select Email/Phone
    U->>UI: Enter Email/Phone
    U->>UI: Enter Password
    U->>UI: Confirm Password
    U->>UI: Accept Terms & Conditions
    
    UI->>UI: Validate Form Data
    UI->>UI: Check Password Strength
    UI->>UI: Verify Password Match
    
    U->>UI: Click "Create Account"
    UI->>API: POST /api/auth/register
    Note over UI,API: {fullName, email/phone, password, termsAccepted}
    
    API->>API: Validate Input Data
    API->>API: Hash Password (bcrypt)
    API->>DB: Check if User Exists
    DB-->>API: User Status
    
    alt User Already Exists
        API-->>UI: 409 Conflict
        UI-->>U: Show Error Message
    else User Does Not Exist
        API->>DB: Create User Record
        DB-->>API: User Created Successfully
        
        API->>Email: Send Welcome Email
        Email-->>API: Email Sent
        
        API->>Cache: Store User Session
        Cache-->>API: Session Stored
        
        API-->>UI: 201 Created + JWT Token
        UI->>UI: Store JWT Token
        UI->>UI: Navigate to Dashboard
        UI-->>U: Show Success Message
    end
```

### Activity Diagram - User Registration

```mermaid
flowchart TD
    A[Start Registration] --> B[Open Registration Form]
    B --> C[Enter Full Name]
    C --> D[Select Contact Type]
    D --> E{Email or Phone?}
    E -->|Email| F[Enter Email Address]
    E -->|Phone| G[Enter Phone Number]
    F --> H[Enter Password]
    G --> H
    H --> I[Confirm Password]
    I --> J[Accept Terms & Conditions]
    J --> K[Validate Form Data]
    K --> L{Validation Passed?}
    L -->|No| M[Show Validation Errors]
    M --> C
    L -->|Yes| N[Check Password Strength]
    N --> O{Password Strong Enough?}
    O -->|No| P[Show Password Requirements]
    P --> H
    O -->|Yes| Q[Submit Registration]
    Q --> R[Call Registration API]
    R --> S{User Already Exists?}
    S -->|Yes| T[Show User Exists Error]
    T --> C
    S -->|No| U[Create User Account]
    U --> V[Send Welcome Email]
    V --> W[Store User Session]
    W --> X[Generate JWT Token]
    X --> Y[Navigate to Dashboard]
    Y --> Z[End - Registration Complete]
```

---

## User Login Flow

### Sequence Diagram - User Login

```mermaid
sequenceDiagram
    participant U as User
    participant UI as React UI
    participant API as Backend API
    participant DB as Database
    participant Cache as Redis Cache
    participant Auth as Auth Service

    U->>UI: Click "Sign In"
    UI->>U: Show Login Form
    
    U->>UI: Select Email/Phone
    U->>UI: Enter Email/Phone
    U->>UI: Enter Password
    U->>UI: Toggle Remember Me (Optional)
    
    U->>UI: Click "Sign In"
    UI->>API: POST /api/auth/login
    Note over UI,API: {email/phone, password, rememberMe}
    
    API->>API: Validate Input Data
    API->>DB: Find User by Email/Phone
    DB-->>API: User Data
    
    alt User Not Found
        API-->>UI: 404 Not Found
        UI-->>U: Show Invalid Credentials Error
    else User Found
        API->>API: Verify Password (bcrypt)
        
        alt Password Incorrect
            API-->>UI: 401 Unauthorized
            UI-->>U: Show Invalid Credentials Error
        else Password Correct
            API->>Auth: Generate JWT Token
            Auth-->>API: JWT Token
            
            alt Remember Me Checked
                API->>Cache: Store Long-term Session
                Cache-->>API: Session Stored
            end
            
            API-->>UI: 200 OK + JWT Token + User Data
            UI->>UI: Store JWT Token
            UI->>UI: Navigate to Dashboard
            UI-->>U: Show Welcome Message
        end
    end
```

### Activity Diagram - User Login

```mermaid
flowchart TD
    A[Start Login] --> B[Open Login Form]
    B --> C[Select Login Type]
    C --> D{Email or Phone?}
    D -->|Email| E[Enter Email Address]
    D -->|Phone| F[Enter Phone Number]
    E --> G[Enter Password]
    F --> G
    G --> H[Toggle Remember Me]
    H --> I[Click Sign In]
    I --> J[Validate Form Data]
    J --> K{Validation Passed?}
    K -->|No| L[Show Validation Errors]
    L --> C
    K -->|Yes| M[Call Login API]
    M --> N[Find User in Database]
    N --> O{User Found?}
    O -->|No| P[Show Invalid Credentials]
    P --> C
    O -->|Yes| Q[Verify Password]
    Q --> R{Password Correct?}
    R -->|No| S[Show Invalid Credentials]
    S --> C
    R -->|Yes| T[Generate JWT Token]
    T --> U{Remember Me?}
    U -->|Yes| V[Store Long-term Session]
    U -->|No| W[Store Short-term Session]
    V --> X[Navigate to Dashboard]
    W --> X
    X --> Y[End - Login Complete]
```

---

## Forgot Password Flow

### Sequence Diagram - Forgot Password

```mermaid
sequenceDiagram
    participant U as User
    participant UI as React UI
    participant API as Backend API
    participant DB as Database
    participant Email as Email Service
    participant Cache as Redis Cache

    U->>UI: Click "Forgot Password?"
    UI->>U: Show Forgot Password Form
    
    U->>UI: Enter Email Address
    U->>UI: Click "Send Reset Link"
    
    UI->>API: POST /api/auth/forgot-password
    Note over UI,API: {email}
    
    API->>API: Validate Email Format
    API->>DB: Find User by Email
    DB-->>API: User Data
    
    alt User Not Found
        API-->>UI: 404 Not Found
        UI-->>U: Show "Email Not Found" Error
    else User Found
        API->>API: Generate Reset Token
        API->>API: Set Token Expiry (15 minutes)
        API->>Cache: Store Reset Token
        Cache-->>API: Token Stored
        
        API->>Email: Send Password Reset Email
        Note over API,Email: {email, resetToken, expiry}
        Email-->>API: Email Sent Successfully
        
        API-->>UI: 200 OK + Success Message
        UI-->>U: Show "Reset Link Sent" Message
        
        Note over U,UI: User checks email and clicks reset link
        
        U->>UI: Click Reset Link from Email
        UI->>API: GET /api/auth/reset-password?token=xxx
        API->>Cache: Validate Reset Token
        Cache-->>API: Token Status
        
        alt Token Invalid/Expired
            API-->>UI: 400 Bad Request
            UI-->>U: Show "Invalid/Expired Link" Error
        else Token Valid
            UI->>U: Show Reset Password Form
            U->>UI: Enter New Password
            U->>UI: Confirm New Password
            U->>UI: Click "Reset Password"
            
            UI->>API: POST /api/auth/reset-password
            Note over UI,API: {token, newPassword}
            
            API->>API: Validate New Password
            API->>API: Hash New Password
            API->>DB: Update User Password
            DB-->>API: Password Updated
            
            API->>Cache: Invalidate Reset Token
            Cache-->>API: Token Invalidated
            
            API-->>UI: 200 OK + Success Message
            UI->>UI: Navigate to Login
            UI-->>U: Show "Password Reset Successfully" Message
        end
    end
```

### Activity Diagram - Forgot Password

```mermaid
flowchart TD
    A[Start Forgot Password] --> B[Click Forgot Password Link]
    B --> C[Show Forgot Password Form]
    C --> D[Enter Email Address]
    D --> E[Click Send Reset Link]
    E --> F[Validate Email Format]
    F --> G{Valid Email?}
    G -->|No| H[Show Email Format Error]
    H --> D
    G -->|Yes| I[Call Forgot Password API]
    I --> J[Find User by Email]
    J --> K{User Found?}
    K -->|No| L[Show Email Not Found Error]
    L --> D
    K -->|Yes| M[Generate Reset Token]
    M --> N[Set Token Expiry]
    N --> O[Store Token in Cache]
    O --> P[Send Reset Email]
    P --> Q[Show Success Message]
    Q --> R[User Checks Email]
    R --> S[Click Reset Link]
    S --> T[Validate Reset Token]
    T --> U{Token Valid?}
    U -->|No| V[Show Invalid Link Error]
    V --> C
    U -->|Yes| W[Show Reset Password Form]
    W --> X[Enter New Password]
    X --> Y[Confirm New Password]
    Y --> Z[Validate Password]
    Z --> AA{Password Valid?}
    AA -->|No| BB[Show Password Error]
    BB --> X
    AA -->|Yes| CC[Submit New Password]
    CC --> DD[Update Password in DB]
    DD --> EE[Invalidate Reset Token]
    EE --> FF[Navigate to Login]
    FF --> GG[End - Password Reset Complete]
```

---

## State Diagrams

### Authentication State Diagram

```mermaid
stateDiagram-v2
    [*] --> SplashScreen
    SplashScreen --> LoginScreen : Click Login
    SplashScreen --> RegisterScreen : Click Register
    SplashScreen --> Dashboard : Guest Access
    
    LoginScreen --> RegisterScreen : Click Create Account
    LoginScreen --> ForgotPasswordScreen : Click Forgot Password
    LoginScreen --> Dashboard : Login Success
    LoginScreen --> LoginScreen : Login Failed
    
    RegisterScreen --> LoginScreen : Click Sign In
    RegisterScreen --> Dashboard : Registration Success
    RegisterScreen --> RegisterScreen : Registration Failed
    
    ForgotPasswordScreen --> LoginScreen : Back to Login
    ForgotPasswordScreen --> ResetPasswordScreen : Click Reset Link
    ForgotPasswordScreen --> ForgotPasswordScreen : Email Not Found
    
    ResetPasswordScreen --> LoginScreen : Password Reset Success
    ResetPasswordScreen --> ResetPasswordScreen : Reset Failed
    
    Dashboard --> LoginScreen : Logout
    Dashboard --> [*] : App Close
```

### User Session State Diagram

```mermaid
stateDiagram-v2
    [*] --> NoSession
    NoSession --> Authenticating : Login Attempt
    NoSession --> GuestSession : Guest Access
    
    Authenticating --> Authenticated : Login Success
    Authenticating --> NoSession : Login Failed
    
    Authenticated --> SessionExpired : Token Expiry
    Authenticated --> NoSession : Logout
    Authenticated --> Authenticated : Token Refresh
    
    GuestSession --> NoSession : Logout
    GuestSession --> Authenticated : Login Success
    
    SessionExpired --> NoSession : Auto Logout
    SessionExpired --> Authenticated : Re-authentication
```

---

## Class Diagrams

### Authentication Classes

```mermaid
classDiagram
    class User {
        +String id
        +String fullName
        +String email
        +String phone
        +String passwordHash
        +DateTime createdAt
        +DateTime updatedAt
        +Boolean isActive
        +Boolean emailVerified
        +Boolean phoneVerified
        +validateEmail()
        +validatePhone()
        +hashPassword()
        +verifyPassword()
    }
    
    class AuthService {
        +registerUser(userData)
        +loginUser(credentials)
        +forgotPassword(email)
        +resetPassword(token, newPassword)
        +validateToken(token)
        +refreshToken(refreshToken)
        +logoutUser(userId)
    }
    
    class JWTService {
        +generateToken(payload)
        +verifyToken(token)
        +decodeToken(token)
        +refreshToken(token)
        +revokeToken(token)
    }
    
    class PasswordService {
        +hashPassword(password)
        +verifyPassword(password, hash)
        +generateResetToken()
        +validatePasswordStrength(password)
    }
    
    class EmailService {
        +sendWelcomeEmail(user)
        +sendResetEmail(email, token)
        +sendVerificationEmail(email, token)
    }
    
    class SessionService {
        +createSession(userId, rememberMe)
        +validateSession(sessionId)
        +refreshSession(sessionId)
        +destroySession(sessionId)
    }
    
    class LoginComponent {
        +String email
        +String password
        +Boolean rememberMe
        +Boolean isLoading
        +handleLogin()
        +handleForgotPassword()
        +validateForm()
    }
    
    class RegisterComponent {
        +String fullName
        +String email
        +String password
        +String confirmPassword
        +Boolean agreeToTerms
        +Boolean isLoading
        +handleRegister()
        +validateForm()
        +checkPasswordStrength()
    }
    
    class ForgotPasswordComponent {
        +String email
        +Boolean isLoading
        +handleSubmit()
        +validateEmail()
    }
    
    class ResetPasswordComponent {
        +String token
        +String newPassword
        +String confirmPassword
        +Boolean isLoading
        +handleReset()
        +validateForm()
    }
    
    User ||--o{ AuthService : uses
    AuthService ||--o{ JWTService : uses
    AuthService ||--o{ PasswordService : uses
    AuthService ||--o{ EmailService : uses
    AuthService ||--o{ SessionService : uses
    LoginComponent ||--o{ AuthService : calls
    RegisterComponent ||--o{ AuthService : calls
    ForgotPasswordComponent ||--o{ AuthService : calls
    ResetPasswordComponent ||--o{ AuthService : calls
```

---

## API Design

### Authentication Endpoints

```mermaid
graph TB
    subgraph "Authentication API"
        A[POST /api/auth/register] --> A1[Create new user account]
        B[POST /api/auth/login] --> B1[Authenticate user]
        C[POST /api/auth/forgot-password] --> C1[Send reset email]
        D[POST /api/auth/reset-password] --> D1[Reset user password]
        E[POST /api/auth/refresh] --> E1[Refresh JWT token]
        F[POST /api/auth/logout] --> F1[Logout user]
        G[GET /api/auth/verify-email] --> G1[Verify email address]
        H[POST /api/auth/resend-verification] --> H1[Resend verification email]
    end
    
    subgraph "Request/Response Flow"
        I[Client Request] --> J[API Gateway]
        J --> K[Authentication Middleware]
        K --> L[Route Handler]
        L --> M[Service Layer]
        M --> N[Database]
        N --> O[Response]
        O --> P[Client Response]
    end
```

### Complete API Reference Table

| Method | Endpoint | Description | Request Body | Response | Status Codes | Authentication |
|--------|----------|-------------|--------------|----------|--------------|----------------|
| **POST** | `/api/auth/register` | Register new user | `RegisterRequest` | `AuthResponse` | 201, 400, 409 | None |
| **POST** | `/api/auth/login` | User login | `LoginRequest` | `AuthResponse` | 200, 401, 400 | None |
| **POST** | `/api/auth/forgot-password` | Send password reset email | `ForgotPasswordRequest` | `MessageResponse` | 200, 404, 400 | None |
| **POST** | `/api/auth/reset-password` | Reset user password | `ResetPasswordRequest` | `MessageResponse` | 200, 400, 401 | None |
| **POST** | `/api/auth/refresh` | Refresh JWT token | `RefreshTokenRequest` | `TokenResponse` | 200, 401 | None |
| **POST** | `/api/auth/logout` | User logout | `LogoutRequest` | `MessageResponse` | 200, 401 | Required |
| **GET** | `/api/auth/verify-email` | Verify email address | Query: `token` | `MessageResponse` | 200, 400 | None |
| **POST** | `/api/auth/resend-verification` | Resend verification email | `ResendVerificationRequest` | `MessageResponse` | 200, 400 | None |
| **GET** | `/api/auth/profile` | Get user profile | None | `UserProfileResponse` | 200, 401 | Required |
| **PUT** | `/api/auth/profile` | Update user profile | `UpdateProfileRequest` | `UserProfileResponse` | 200, 400, 401 | Required |
| **POST** | `/api/auth/change-password` | Change password | `ChangePasswordRequest` | `MessageResponse` | 200, 400, 401 | Required |
| **POST** | `/api/auth/verify-phone` | Verify phone number | `VerifyPhoneRequest` | `MessageResponse` | 200, 400, 401 | Required |
| **GET** | `/api/auth/sessions` | Get active sessions | None | `SessionsResponse` | 200, 401 | Required |
| **DELETE** | `/api/auth/sessions/{sessionId}` | Revoke session | None | `MessageResponse` | 200, 401, 404 | Required |

### Request/Response Models

#### RegisterRequest
```json
{
  "fullName": "string",
  "email": "string",
  "phone": "string",
  "password": "string",
  "contactType": "EMAIL | PHONE",
  "termsAccepted": boolean
}
```

#### LoginRequest
```json
{
  "email": "string",
  "phone": "string",
  "password": "string",
  "loginType": "EMAIL | PHONE",
  "rememberMe": boolean
}
```

#### ForgotPasswordRequest
```json
{
  "email": "string"
}
```

#### ResetPasswordRequest
```json
{
  "token": "string",
  "newPassword": "string"
}
```

#### AuthResponse
```json
{
  "success": boolean,
  "message": "string",
  "data": {
    "user": {
      "id": "string",
      "fullName": "string",
      "email": "string",
      "phone": "string",
      "isActive": boolean,
      "emailVerified": boolean,
      "phoneVerified": boolean,
      "createdAt": "datetime",
      "lastLoginAt": "datetime"
    },
    "token": "string",
    "refreshToken": "string",
    "expiresIn": number,
    "tokenType": "Bearer"
  }
}
```

#### MessageResponse
```json
{
  "success": boolean,
  "message": "string",
  "timestamp": "datetime"
}
```

#### ErrorResponse
```json
{
  "success": false,
  "error": {
    "code": "string",
    "message": "string",
    "details": [
      {
        "field": "string",
        "message": "string"
      }
    ]
  },
  "timestamp": "datetime",
  "requestId": "string"
}
```

### API Request/Response Examples

#### Registration Request
```json
POST /api/auth/register
{
  "fullName": "John Doe",
  "email": "john.doe@example.com",
  "password": "SecurePass123!",
  "contactType": "email",
  "termsAccepted": true
}
```

#### Registration Response
```json
{
  "success": true,
  "message": "Account created successfully",
  "data": {
    "user": {
      "id": "user_123",
      "fullName": "John Doe",
      "email": "john.doe@example.com",
      "isActive": true,
      "emailVerified": false
    },
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "refresh_token_123"
  }
}
```

#### Login Request
```json
POST /api/auth/login
{
  "email": "john.doe@example.com",
  "password": "SecurePass123!",
  "rememberMe": true
}
```

#### Login Response
```json
{
  "success": true,
  "message": "Login successful",
  "data": {
    "user": {
      "id": "user_123",
      "fullName": "John Doe",
      "email": "john.doe@example.com",
      "isActive": true,
      "emailVerified": true
    },
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "refresh_token_123",
    "expiresIn": 3600
  }
}
```

---

## Security Considerations

### Security Flow Diagram

```mermaid
flowchart TD
    A[User Input] --> B[Input Validation]
    B --> C[Sanitization]
    C --> D[Rate Limiting]
    D --> E[Authentication Check]
    E --> F[Authorization Check]
    F --> G[Business Logic]
    G --> H[Data Encryption]
    H --> I[Response]
    
    subgraph "Security Layers"
        J[HTTPS/TLS]
        K[JWT Token Validation]
        L[Password Hashing - bcrypt]
        M[SQL Injection Prevention]
        N[XSS Protection]
        O[CSRF Protection]
    end
    
    B --> J
    E --> K
    G --> L
    G --> M
    I --> N
    I --> O
```

### Security Checklist

- [ ] **Input Validation**: All user inputs are validated and sanitized
- [ ] **Password Security**: Passwords are hashed using bcrypt with salt
- [ ] **JWT Security**: Tokens are signed with secure secret and have expiration
- [ ] **Rate Limiting**: API endpoints are protected against brute force attacks
- [ ] **HTTPS**: All communications are encrypted in transit
- [ ] **SQL Injection**: Parameterized queries prevent SQL injection
- [ ] **XSS Protection**: Output encoding prevents cross-site scripting
- [ ] **CSRF Protection**: CSRF tokens prevent cross-site request forgery
- [ ] **Session Management**: Secure session handling with proper expiration
- [ ] **Error Handling**: Generic error messages prevent information leakage

---

## Error Handling

### Error Flow Diagram

```mermaid
flowchart TD
    A[API Request] --> B{Request Valid?}
    B -->|No| C[400 Bad Request]
    B -->|Yes| D{User Authenticated?}
    D -->|No| E[401 Unauthorized]
    D -->|Yes| F{User Authorized?}
    F -->|No| G[403 Forbidden]
    F -->|Yes| H{Resource Exists?}
    H -->|No| I[404 Not Found]
    H -->|Yes| J{Business Logic Valid?}
    J -->|No| K[422 Unprocessable Entity]
    J -->|Yes| L[200 Success]
    
    C --> M[Return Error Response]
    E --> M
    G --> M
    I --> M
    K --> M
    L --> N[Return Success Response]
```

### Error Response Format

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input data",
    "details": [
      {
        "field": "email",
        "message": "Email format is invalid"
      },
      {
        "field": "password",
        "message": "Password must be at least 8 characters"
      }
    ]
  },
  "timestamp": "2024-01-15T10:30:00Z",
  "requestId": "req_123456"
}
```

---

## Implementation Notes

### Frontend Implementation (Flutter)
- **State Management**: Provider/Riverpod for state management
- **Form Validation**: FormBuilder with real-time validation
- **Error Handling**: SnackBar and Dialog for user-friendly error messages
- **Loading States**: CircularProgressIndicator during API calls
- **Responsive Design**: Adaptive design for different screen sizes
- **Navigation**: Navigator 2.0 with named routes
- **HTTP Client**: Dio for API communication
- **Local Storage**: SharedPreferences for token storage

### Backend Implementation (Spring Boot)
- **Spring Boot**: RESTful API with Spring Security
- **JWT Authentication**: Spring Security JWT for token-based authentication
- **Password Hashing**: BCryptPasswordEncoder for secure password storage
- **Database**: JPA/Hibernate with PostgreSQL
- **Caching**: Spring Cache with Redis
- **Validation**: Bean Validation (JSR-303)
- **Exception Handling**: Global exception handler

### Testing Strategy
- **Unit Tests**: Test individual components and functions
- **Integration Tests**: Test API endpoints and database interactions
- **E2E Tests**: Test complete user flows
- **Security Tests**: Test for vulnerabilities and security issues

---

## Conclusion

This authentication design document provides comprehensive diagrams and specifications for implementing a secure, user-friendly authentication system for the SafeGuard Parent App. The design ensures:

1. **Security**: Multiple layers of security protection
2. **Usability**: Intuitive user flows and clear error handling
3. **Scalability**: Architecture that can handle growth
4. **Maintainability**: Clean code structure and proper documentation
5. **Compliance**: Following security best practices and standards

The diagrams and specifications in this document serve as a complete guide for implementing the authentication system in your graduation doctoral project.
