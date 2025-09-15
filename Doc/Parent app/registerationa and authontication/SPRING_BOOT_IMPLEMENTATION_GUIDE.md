# SafeGuard Parent App - Spring Boot Implementation Guide

## Project Structure

```
src/
├── main/
│   ├── java/
│   │   └── com/
│   │       └── safeguard/
│   │           └── parentapp/
│   │               ├── ParentAppApplication.java
│   │               ├── config/
│   │               │   ├── SecurityConfig.java
│   │               │   ├── JwtConfig.java
│   │               │   ├── RedisConfig.java
│   │               │   └── CorsConfig.java
│   │               ├── controller/
│   │               │   ├── AuthController.java
│   │               │   └── UserController.java
│   │               ├── dto/
│   │               │   ├── request/
│   │               │   │   ├── LoginRequest.java
│   │               │   │   ├── RegisterRequest.java
│   │               │   │   ├── ForgotPasswordRequest.java
│   │               │   │   └── ResetPasswordRequest.java
│   │               │   └── response/
│   │               │       ├── AuthResponse.java
│   │               │       ├── MessageResponse.java
│   │               │       └── UserProfileResponse.java
│   │               ├── entity/
│   │               │   ├── User.java
│   │               │   ├── UserSession.java
│   │               │   └── PasswordResetToken.java
│   │               ├── repository/
│   │               │   ├── UserRepository.java
│   │               │   ├── UserSessionRepository.java
│   │               │   └── PasswordResetTokenRepository.java
│   │               ├── service/
│   │               │   ├── AuthService.java
│   │               │   ├── UserService.java
│   │               │   ├── JwtService.java
│   │               │   ├── PasswordService.java
│   │               │   ├── EmailService.java
│   │               │   └── SessionService.java
│   │               ├── security/
│   │               │   ├── JwtAuthenticationFilter.java
│   │               │   ├── JwtAuthorizationFilter.java
│   │               │   └── CustomUserDetailsService.java
│   │               ├── exception/
│   │               │   ├── GlobalExceptionHandler.java
│   │               │   ├── UserNotFoundException.java
│   │               │   ├── InvalidCredentialsException.java
│   │               │   └── TokenExpiredException.java
│   │               └── util/
│   │                   ├── PasswordValidator.java
│   │                   └── EmailValidator.java
│   └── resources/
│       ├── application.yml
│       ├── application-dev.yml
│       ├── application-prod.yml
│       └── templates/
│           ├── welcome-email.html
│           └── reset-password-email.html
└── test/
    └── java/
        └── com/
            └── safeguard/
                └── parentapp/
                    ├── controller/
                    ├── service/
                    └── repository/
```

## Dependencies (pom.xml)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.0</version>
        <relativePath/>
    </parent>
    
    <groupId>com.safeguard</groupId>
    <artifactId>parent-app</artifactId>
    <version>1.0.0</version>
    <packaging>jar</packaging>
    
    <properties>
        <java.version>17</java.version>
        <jwt.version>0.12.3</jwt.version>
    </properties>
    
    <dependencies>
        <!-- Spring Boot Starters -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
        
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
        
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-mail</artifactId>
        </dependency>
        
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>
        
        <!-- Database -->
        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <scope>runtime</scope>
        </dependency>
        
        <!-- JWT -->
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt-api</artifactId>
            <version>${jwt.version}</version>
        </dependency>
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt-impl</artifactId>
            <version>${jwt.version}</version>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt-jackson</artifactId>
            <version>${jwt.version}</version>
            <scope>runtime</scope>
        </dependency>
        
        <!-- JSON Processing -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
        </dependency>
        
        <!-- Lombok -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        
        <!-- Testing -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        
        <dependency>
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

## Core Implementation Files

### 1. Application Configuration (application.yml)

```yaml
server:
  port: 8080
  servlet:
    context-path: /api

spring:
  application:
    name: safeguard-parent-app
  
  profiles:
    active: dev
  
  datasource:
    url: jdbc:postgresql://localhost:5432/safeguard_db
    username: ${DB_USERNAME:postgres}
    password: ${DB_PASSWORD:password}
    driver-class-name: org.postgresql.Driver
  
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: false
    properties:
      hibernate:
        dialect: org.hibernate.dialect.PostgreSQLDialect
        format_sql: true
  
  redis:
    host: ${REDIS_HOST:localhost}
    port: ${REDIS_PORT:6379}
    password: ${REDIS_PASSWORD:}
    timeout: 2000ms
    lettuce:
      pool:
        max-active: 8
        max-idle: 8
        min-idle: 0
  
  mail:
    host: ${MAIL_HOST:smtp.gmail.com}
    port: ${MAIL_PORT:587}
    username: ${MAIL_USERNAME:}
    password: ${MAIL_PASSWORD:}
    properties:
      mail:
        smtp:
          auth: true
          starttls:
            enable: true

jwt:
  secret: ${JWT_SECRET:mySecretKey}
  expiration: 86400000 # 24 hours
  refresh-expiration: 604800000 # 7 days

app:
  cors:
    allowed-origins: 
      - http://localhost:3000
      - http://localhost:8080
    allowed-methods: GET,POST,PUT,DELETE,OPTIONS
    allowed-headers: "*"
    allow-credentials: true
```

### 2. User Entity (entity/User.java)

```java
package com.safeguard.parentapp.entity;

import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import java.time.LocalDateTime;
import java.util.List;

@Entity
@Table(name = "users")
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class User {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(nullable = false, length = 100)
    private String fullName;
    
    @Column(unique = true, nullable = false, length = 100)
    private String email;
    
    @Column(unique = true, length = 20)
    private String phone;
    
    @Column(nullable = false)
    private String passwordHash;
    
    @Enumerated(EnumType.STRING)
    @Column(nullable = false)
    private ContactType contactType;
    
    @Column(nullable = false)
    private Boolean isActive = true;
    
    @Column(nullable = false)
    private Boolean emailVerified = false;
    
    @Column(nullable = false)
    private Boolean phoneVerified = false;
    
    @CreationTimestamp
    @Column(nullable = false, updatable = false)
    private LocalDateTime createdAt;
    
    @UpdateTimestamp
    private LocalDateTime updatedAt;
    
    private LocalDateTime lastLoginAt;
    
    @OneToMany(mappedBy = "user", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
    private List<UserSession> sessions;
    
    public enum ContactType {
        EMAIL, PHONE
    }
}
```

### 3. Auth Controller (controller/AuthController.java)

```java
package com.safeguard.parentapp.controller;

import com.safeguard.parentapp.dto.request.*;
import com.safeguard.parentapp.dto.response.AuthResponse;
import com.safeguard.parentapp.dto.response.MessageResponse;
import com.safeguard.parentapp.service.AuthService;
import jakarta.validation.Valid;
import lombok.RequiredArgsConstructor;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/auth")
@RequiredArgsConstructor
@CrossOrigin(origins = "*")
public class AuthController {
    
    private final AuthService authService;
    
    @PostMapping("/register")
    public ResponseEntity<AuthResponse> register(@Valid @RequestBody RegisterRequest request) {
        AuthResponse response = authService.register(request);
        return ResponseEntity.status(HttpStatus.CREATED).body(response);
    }
    
    @PostMapping("/login")
    public ResponseEntity<AuthResponse> login(@Valid @RequestBody LoginRequest request) {
        AuthResponse response = authService.login(request);
        return ResponseEntity.ok(response);
    }
    
    @PostMapping("/forgot-password")
    public ResponseEntity<MessageResponse> forgotPassword(@Valid @RequestBody ForgotPasswordRequest request) {
        MessageResponse response = authService.forgotPassword(request.getEmail());
        return ResponseEntity.ok(response);
    }
    
    @PostMapping("/reset-password")
    public ResponseEntity<MessageResponse> resetPassword(@Valid @RequestBody ResetPasswordRequest request) {
        MessageResponse response = authService.resetPassword(request);
        return ResponseEntity.ok(response);
    }
    
    @PostMapping("/refresh")
    public ResponseEntity<AuthResponse> refreshToken(@Valid @RequestBody RefreshTokenRequest request) {
        AuthResponse response = authService.refreshToken(request.getRefreshToken());
        return ResponseEntity.ok(response);
    }
    
    @PostMapping("/logout")
    public ResponseEntity<MessageResponse> logout(@RequestHeader("Authorization") String token) {
        MessageResponse response = authService.logout(token);
        return ResponseEntity.ok(response);
    }
    
    @GetMapping("/verify-email")
    public ResponseEntity<MessageResponse> verifyEmail(@RequestParam String token) {
        MessageResponse response = authService.verifyEmail(token);
        return ResponseEntity.ok(response);
    }
    
    @PostMapping("/resend-verification")
    public ResponseEntity<MessageResponse> resendVerification(@Valid @RequestBody ResendVerificationRequest request) {
        MessageResponse response = authService.resendVerification(request.getEmail());
        return ResponseEntity.ok(response);
    }
}
```

### 4. Auth Service (service/AuthService.java)

```java
package com.safeguard.parentapp.service;

import com.safeguard.parentapp.dto.request.*;
import com.safeguard.parentapp.dto.response.AuthResponse;
import com.safeguard.parentapp.dto.response.MessageResponse;
import com.safeguard.parentapp.entity.User;
import com.safeguard.parentapp.exception.UserNotFoundException;
import com.safeguard.parentapp.repository.UserRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
@RequiredArgsConstructor
@Transactional
public class AuthService {
    
    private final UserRepository userRepository;
    private final JwtService jwtService;
    private final PasswordService passwordService;
    private final EmailService emailService;
    private final SessionService sessionService;
    
    public AuthResponse register(RegisterRequest request) {
        // Check if user already exists
        if (userRepository.existsByEmail(request.getEmail())) {
            throw new RuntimeException("User with this email already exists");
        }
        
        if (request.getPhone() != null && userRepository.existsByPhone(request.getPhone())) {
            throw new RuntimeException("User with this phone number already exists");
        }
        
        // Create new user
        User user = User.builder()
                .fullName(request.getFullName())
                .email(request.getEmail())
                .phone(request.getPhone())
                .passwordHash(passwordService.hashPassword(request.getPassword()))
                .contactType(request.getContactType())
                .isActive(true)
                .emailVerified(false)
                .phoneVerified(false)
                .build();
        
        User savedUser = userRepository.save(user);
        
        // Send welcome email
        emailService.sendWelcomeEmail(savedUser);
        
        // Generate tokens
        String token = jwtService.generateToken(savedUser);
        String refreshToken = jwtService.generateRefreshToken(savedUser);
        
        // Create session
        sessionService.createSession(savedUser.getId(), request.getRememberMe());
        
        return AuthResponse.builder()
                .success(true)
                .message("Account created successfully")
                .user(savedUser)
                .token(token)
                .refreshToken(refreshToken)
                .expiresIn(86400) // 24 hours
                .tokenType("Bearer")
                .build();
    }
    
    public AuthResponse login(LoginRequest request) {
        User user = userRepository.findByEmail(request.getEmail())
                .orElseThrow(() -> new UserNotFoundException("Invalid credentials"));
        
        if (!passwordService.verifyPassword(request.getPassword(), user.getPasswordHash())) {
            throw new RuntimeException("Invalid credentials");
        }
        
        if (!user.getIsActive()) {
            throw new RuntimeException("Account is deactivated");
        }
        
        // Update last login
        user.setLastLoginAt(java.time.LocalDateTime.now());
        userRepository.save(user);
        
        // Generate tokens
        String token = jwtService.generateToken(user);
        String refreshToken = jwtService.generateRefreshToken(user);
        
        // Create session
        sessionService.createSession(user.getId(), request.getRememberMe());
        
        return AuthResponse.builder()
                .success(true)
                .message("Login successful")
                .user(user)
                .token(token)
                .refreshToken(refreshToken)
                .expiresIn(86400) // 24 hours
                .tokenType("Bearer")
                .build();
    }
    
    public MessageResponse forgotPassword(String email) {
        User user = userRepository.findByEmail(email)
                .orElseThrow(() -> new UserNotFoundException("User not found"));
        
        // Generate reset token
        String resetToken = jwtService.generatePasswordResetToken(user);
        
        // Send reset email
        emailService.sendPasswordResetEmail(user, resetToken);
        
        return MessageResponse.builder()
                .success(true)
                .message("Password reset link sent to your email")
                .timestamp(java.time.LocalDateTime.now())
                .build();
    }
    
    public MessageResponse resetPassword(ResetPasswordRequest request) {
        // Validate reset token
        if (!jwtService.validatePasswordResetToken(request.getToken())) {
            throw new RuntimeException("Invalid or expired reset token");
        }
        
        // Get user from token
        String email = jwtService.getEmailFromToken(request.getToken());
        User user = userRepository.findByEmail(email)
                .orElseThrow(() -> new UserNotFoundException("User not found"));
        
        // Update password
        user.setPasswordHash(passwordService.hashPassword(request.getNewPassword()));
        userRepository.save(user);
        
        // Invalidate all sessions
        sessionService.invalidateAllUserSessions(user.getId());
        
        return MessageResponse.builder()
                .success(true)
                .message("Password reset successfully")
                .timestamp(java.time.LocalDateTime.now())
                .build();
    }
    
    public AuthResponse refreshToken(String refreshToken) {
        if (!jwtService.validateRefreshToken(refreshToken)) {
            throw new RuntimeException("Invalid refresh token");
        }
        
        String email = jwtService.getEmailFromRefreshToken(refreshToken);
        User user = userRepository.findByEmail(email)
                .orElseThrow(() -> new UserNotFoundException("User not found"));
        
        String newToken = jwtService.generateToken(user);
        String newRefreshToken = jwtService.generateRefreshToken(user);
        
        return AuthResponse.builder()
                .success(true)
                .message("Token refreshed successfully")
                .user(user)
                .token(newToken)
                .refreshToken(newRefreshToken)
                .expiresIn(86400) // 24 hours
                .tokenType("Bearer")
                .build();
    }
    
    public MessageResponse logout(String token) {
        String cleanToken = token.replace("Bearer ", "");
        sessionService.invalidateSession(cleanToken);
        
        return MessageResponse.builder()
                .success(true)
                .message("Logged out successfully")
                .timestamp(java.time.LocalDateTime.now())
                .build();
    }
    
    public MessageResponse verifyEmail(String token) {
        if (!jwtService.validateEmailVerificationToken(token)) {
            throw new RuntimeException("Invalid or expired verification token");
        }
        
        String email = jwtService.getEmailFromToken(token);
        User user = userRepository.findByEmail(email)
                .orElseThrow(() -> new UserNotFoundException("User not found"));
        
        user.setEmailVerified(true);
        userRepository.save(user);
        
        return MessageResponse.builder()
                .success(true)
                .message("Email verified successfully")
                .timestamp(java.time.LocalDateTime.now())
                .build();
    }
    
    public MessageResponse resendVerification(String email) {
        User user = userRepository.findByEmail(email)
                .orElseThrow(() -> new UserNotFoundException("User not found"));
        
        if (user.getEmailVerified()) {
            throw new RuntimeException("Email already verified");
        }
        
        String verificationToken = jwtService.generateEmailVerificationToken(user);
        emailService.sendEmailVerificationEmail(user, verificationToken);
        
        return MessageResponse.builder()
                .success(true)
                .message("Verification email sent")
                .timestamp(java.time.LocalDateTime.now())
                .build();
    }
}
```

### 5. JWT Service (service/JwtService.java)

```java
package com.safeguard.parentapp.service;

import com.safeguard.parentapp.entity.User;
import io.jsonwebtoken.*;
import io.jsonwebtoken.security.Keys;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;

import javax.crypto.SecretKey;
import java.util.Date;
import java.util.HashMap;
import java.util.Map;

@Service
@Slf4j
public class JwtService {
    
    @Value("${jwt.secret}")
    private String secret;
    
    @Value("${jwt.expiration}")
    private Long expiration;
    
    @Value("${jwt.refresh-expiration}")
    private Long refreshExpiration;
    
    private SecretKey getSigningKey() {
        return Keys.hmacShaKeyFor(secret.getBytes());
    }
    
    public String generateToken(User user) {
        Map<String, Object> claims = new HashMap<>();
        claims.put("userId", user.getId());
        claims.put("email", user.getEmail());
        claims.put("role", "USER");
        
        return createToken(claims, user.getEmail(), expiration);
    }
    
    public String generateRefreshToken(User user) {
        Map<String, Object> claims = new HashMap<>();
        claims.put("userId", user.getId());
        claims.put("email", user.getEmail());
        claims.put("type", "refresh");
        
        return createToken(claims, user.getEmail(), refreshExpiration);
    }
    
    public String generatePasswordResetToken(User user) {
        Map<String, Object> claims = new HashMap<>();
        claims.put("userId", user.getId());
        claims.put("email", user.getEmail());
        claims.put("type", "password_reset");
        
        return createToken(claims, user.getEmail(), 900000); // 15 minutes
    }
    
    public String generateEmailVerificationToken(User user) {
        Map<String, Object> claims = new HashMap<>();
        claims.put("userId", user.getId());
        claims.put("email", user.getEmail());
        claims.put("type", "email_verification");
        
        return createToken(claims, user.getEmail(), 3600000); // 1 hour
    }
    
    private String createToken(Map<String, Object> claims, String subject, Long expiration) {
        return Jwts.builder()
                .setClaims(claims)
                .setSubject(subject)
                .setIssuedAt(new Date(System.currentTimeMillis()))
                .setExpiration(new Date(System.currentTimeMillis() + expiration))
                .signWith(getSigningKey(), SignatureAlgorithm.HS256)
                .compact();
    }
    
    public Boolean validateToken(String token) {
        try {
            Jwts.parserBuilder()
                    .setSigningKey(getSigningKey())
                    .build()
                    .parseClaimsJws(token);
            return true;
        } catch (JwtException | IllegalArgumentException e) {
            log.error("JWT token validation failed: {}", e.getMessage());
            return false;
        }
    }
    
    public Boolean validateRefreshToken(String token) {
        try {
            Claims claims = Jwts.parserBuilder()
                    .setSigningKey(getSigningKey())
                    .build()
                    .parseClaimsJws(token)
                    .getBody();
            
            return "refresh".equals(claims.get("type"));
        } catch (JwtException | IllegalArgumentException e) {
            log.error("Refresh token validation failed: {}", e.getMessage());
            return false;
        }
    }
    
    public Boolean validatePasswordResetToken(String token) {
        try {
            Claims claims = Jwts.parserBuilder()
                    .setSigningKey(getSigningKey())
                    .build()
                    .parseClaimsJws(token)
                    .getBody();
            
            return "password_reset".equals(claims.get("type"));
        } catch (JwtException | IllegalArgumentException e) {
            log.error("Password reset token validation failed: {}", e.getMessage());
            return false;
        }
    }
    
    public Boolean validateEmailVerificationToken(String token) {
        try {
            Claims claims = Jwts.parserBuilder()
                    .setSigningKey(getSigningKey())
                    .build()
                    .parseClaimsJws(token)
                    .getBody();
            
            return "email_verification".equals(claims.get("type"));
        } catch (JwtException | IllegalArgumentException e) {
            log.error("Email verification token validation failed: {}", e.getMessage());
            return false;
        }
    }
    
    public String getEmailFromToken(String token) {
        Claims claims = Jwts.parserBuilder()
                .setSigningKey(getSigningKey())
                .build()
                .parseClaimsJws(token)
                .getBody();
        
        return claims.getSubject();
    }
    
    public String getEmailFromRefreshToken(String token) {
        Claims claims = Jwts.parserBuilder()
                .setSigningKey(getSigningKey())
                .build()
                .parseClaimsJws(token)
                .getBody();
        
        return claims.getSubject();
    }
    
    public Long getUserIdFromToken(String token) {
        Claims claims = Jwts.parserBuilder()
                .setSigningKey(getSigningKey())
                .build()
                .parseClaimsJws(token)
                .getBody();
        
        return Long.valueOf(claims.get("userId").toString());
    }
}
```

### 6. Security Configuration (config/SecurityConfig.java)

```java
package com.safeguard.parentapp.config;

import com.safeguard.parentapp.security.JwtAuthenticationFilter;
import com.safeguard.parentapp.security.JwtAuthorizationFilter;
import lombok.RequiredArgsConstructor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.config.annotation.authentication.configuration.AuthenticationConfiguration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.SecurityFilterChain;
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;

@Configuration
@EnableWebSecurity
@RequiredArgsConstructor
public class SecurityConfig {
    
    private final JwtAuthenticationFilter jwtAuthenticationFilter;
    private final JwtAuthorizationFilter jwtAuthorizationFilter;
    
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
    
    @Bean
    public AuthenticationManager authenticationManager(
            AuthenticationConfiguration config) throws Exception {
        return config.getAuthenticationManager();
    }
    
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http.csrf(csrf -> csrf.disable())
            .sessionManagement(session -> session.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/auth/register", "/auth/login", "/auth/forgot-password", 
                               "/auth/reset-password", "/auth/verify-email", "/auth/resend-verification")
                .permitAll()
                .anyRequest()
                .authenticated()
            )
            .addFilterBefore(jwtAuthenticationFilter, UsernamePasswordAuthenticationFilter.class)
            .addFilterBefore(jwtAuthorizationFilter, UsernamePasswordAuthenticationFilter.class);
        
        return http.build();
    }
}
```

### 7. Global Exception Handler (exception/GlobalExceptionHandler.java)

```java
package com.safeguard.parentapp.exception;

import com.safeguard.parentapp.dto.response.ErrorResponse;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.FieldError;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import java.time.LocalDateTime;
import java.util.HashMap;
import java.util.Map;

@RestControllerAdvice
@Slf4j
public class GlobalExceptionHandler {
    
    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleUserNotFoundException(UserNotFoundException ex) {
        ErrorResponse error = ErrorResponse.builder()
                .success(false)
                .error(ErrorResponse.ErrorDetail.builder()
                        .code("USER_NOT_FOUND")
                        .message(ex.getMessage())
                        .build())
                .timestamp(LocalDateTime.now())
                .build();
        
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }
    
    @ExceptionHandler(InvalidCredentialsException.class)
    public ResponseEntity<ErrorResponse> handleInvalidCredentialsException(InvalidCredentialsException ex) {
        ErrorResponse error = ErrorResponse.builder()
                .success(false)
                .error(ErrorResponse.ErrorDetail.builder()
                        .code("INVALID_CREDENTIALS")
                        .message(ex.getMessage())
                        .build())
                .timestamp(LocalDateTime.now())
                .build();
        
        return ResponseEntity.status(HttpStatus.UNAUTHORIZED).body(error);
    }
    
    @ExceptionHandler(TokenExpiredException.class)
    public ResponseEntity<ErrorResponse> handleTokenExpiredException(TokenExpiredException ex) {
        ErrorResponse error = ErrorResponse.builder()
                .success(false)
                .error(ErrorResponse.ErrorDetail.builder()
                        .code("TOKEN_EXPIRED")
                        .message(ex.getMessage())
                        .build())
                .timestamp(LocalDateTime.now())
                .build();
        
        return ResponseEntity.status(HttpStatus.UNAUTHORIZED).body(error);
    }
    
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidationExceptions(MethodArgumentNotValidException ex) {
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getAllErrors().forEach((error) -> {
            String fieldName = ((FieldError) error).getField();
            String errorMessage = error.getDefaultMessage();
            errors.put(fieldName, errorMessage);
        });
        
        ErrorResponse error = ErrorResponse.builder()
                .success(false)
                .error(ErrorResponse.ErrorDetail.builder()
                        .code("VALIDATION_ERROR")
                        .message("Invalid input data")
                        .details(errors.entrySet().stream()
                                .map(entry -> ErrorResponse.ErrorDetail.ErrorField.builder()
                                        .field(entry.getKey())
                                        .message(entry.getValue())
                                        .build())
                                .toList())
                        .build())
                .timestamp(LocalDateTime.now())
                .build();
        
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(error);
    }
    
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGenericException(Exception ex) {
        log.error("Unexpected error occurred", ex);
        
        ErrorResponse error = ErrorResponse.builder()
                .success(false)
                .error(ErrorResponse.ErrorDetail.builder()
                        .code("INTERNAL_SERVER_ERROR")
                        .message("An unexpected error occurred")
                        .build())
                .timestamp(LocalDateTime.now())
                .build();
        
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(error);
    }
}
```

## Key Features Implementation

### 1. Security
- JWT-based authentication
- BCrypt password hashing
- Role-based authorization
- CORS configuration
- Input validation

### 2. Database Integration
- JPA/Hibernate with PostgreSQL
- Redis for session management
- Database migrations
- Connection pooling

### 3. Email Service
- SMTP configuration
- HTML email templates
- Async email sending
- Email verification

### 4. Error Handling
- Global exception handler
- Custom exception classes
- Structured error responses
- Logging and monitoring

### 5. API Design
- RESTful endpoints
- Request/Response DTOs
- Validation annotations
- HTTP status codes

### 6. Testing
- Unit tests for services
- Integration tests for controllers
- Security tests
- Mock data setup

This implementation provides a robust, secure, and scalable backend for the SafeGuard Parent App using Spring Boot and follows enterprise-level best practices for Java development.
