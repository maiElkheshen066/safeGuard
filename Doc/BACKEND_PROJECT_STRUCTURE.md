# SafeGuard Parent App - Backend Project Structure

## Overview
This document outlines the complete backend project structure for the SafeGuard Parent App, including all folders, classes, services, and files organized by feature and functionality.

## Root Project Structure
```
Parent App/
├── backend/
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/
│   │   │   │   └── com/safeguard/parent/
│   │   │   └── resources/
│   │   └── test/
│   │       └── java/
│   │           └── com/safeguard/parent/
│   ├── target/
│   ├── pom.xml
│   ├── application.yml
│   ├── application-dev.yml
│   ├── application-prod.yml
│   ├── Dockerfile
│   ├── docker-compose.yml
│   └── README.md
```

## Detailed Source Structure

### 1. Main Java Package Structure
```
src/main/java/com/safeguard/parent/
├── ParentApplication.java
├── config/
│   ├── SecurityConfig.java
│   ├── WebSocketConfig.java
│   ├── RedisConfig.java
│   ├── DatabaseConfig.java
│   ├── SwaggerConfig.java
│   ├── CorsConfig.java
│   ├── CacheConfig.java
│   └── ValidationConfig.java
├── controller/
│   ├── AuthController.java
│   ├── DashboardController.java
│   ├── ChildController.java
│   ├── LocationController.java
│   ├── GeofencingController.java
│   ├── MonitoringController.java
│   ├── NotificationController.java
│   ├── FamilyController.java
│   ├── SettingsController.java
│   ├── EmergencyController.java
│   ├── ReportsController.java
│   └── AccessibilityController.java
├── service/
│   ├── AuthService.java
│   ├── DashboardService.java
│   ├── ChildService.java
│   ├── LocationService.java
│   ├── GeofencingService.java
│   ├── MonitoringService.java
│   ├── NotificationService.java
│   ├── FamilyService.java
│   ├── SettingsService.java
│   ├── EmergencyService.java
│   ├── ReportsService.java
│   ├── AccessibilityService.java
│   └── impl/
│       ├── AuthServiceImpl.java
│       ├── DashboardServiceImpl.java
│       ├── ChildServiceImpl.java
│       ├── LocationServiceImpl.java
│       ├── GeofencingServiceImpl.java
│       ├── MonitoringServiceImpl.java
│       ├── NotificationServiceImpl.java
│       ├── FamilyServiceImpl.java
│       ├── SettingsServiceImpl.java
│       ├── EmergencyServiceImpl.java
│       ├── ReportsServiceImpl.java
│       └── AccessibilityServiceImpl.java
├── repository/
│   ├── UserRepository.java
│   ├── ChildRepository.java
│   ├── LocationRepository.java
│   ├── GeofenceRepository.java
│   ├── MonitoringDataRepository.java
│   ├── NotificationRepository.java
│   ├── FamilyRepository.java
│   ├── SettingsRepository.java
│   ├── EmergencyContactRepository.java
│   ├── ReportRepository.java
│   └── AccessibilityRepository.java
├── entity/
│   ├── User.java
│   ├── Child.java
│   ├── Location.java
│   ├── Geofence.java
│   ├── MonitoringData.java
│   ├── Notification.java
│   ├── Family.java
│   ├── Settings.java
│   ├── EmergencyContact.java
│   ├── Report.java
│   └── Accessibility.java
├── dto/
│   ├── request/
│   │   ├── LoginRequest.java
│   │   ├── RegisterRequest.java
│   │   ├── ChildRequest.java
│   │   ├── LocationRequest.java
│   │   ├── GeofenceRequest.java
│   │   ├── MonitoringRequest.java
│   │   ├── NotificationRequest.java
│   │   ├── FamilyRequest.java
│   │   ├── SettingsRequest.java
│   │   ├── EmergencyRequest.java
│   │   └── ReportRequest.java
│   ├── response/
│   │   ├── AuthResponse.java
│   │   ├── DashboardResponse.java
│   │   ├── ChildResponse.java
│   │   ├── LocationResponse.java
│   │   ├── GeofenceResponse.java
│   │   ├── MonitoringResponse.java
│   │   ├── NotificationResponse.java
│   │   ├── FamilyResponse.java
│   │   ├── SettingsResponse.java
│   │   ├── EmergencyResponse.java
│   │   └── ReportResponse.java
│   └── common/
│       ├── ApiResponse.java
│       ├── ErrorResponse.java
│       ├── PaginationResponse.java
│       └── BaseResponse.java
├── mapper/
│   ├── UserMapper.java
│   ├── ChildMapper.java
│   ├── LocationMapper.java
│   ├── GeofenceMapper.java
│   ├── MonitoringMapper.java
│   ├── NotificationMapper.java
│   ├── FamilyMapper.java
│   ├── SettingsMapper.java
│   ├── EmergencyMapper.java
│   └── ReportMapper.java
├── exception/
│   ├── GlobalExceptionHandler.java
│   ├── ResourceNotFoundException.java
│   ├── ValidationException.java
│   ├── AuthenticationException.java
│   ├── AuthorizationException.java
│   ├── BusinessException.java
│   └── TechnicalException.java
├── security/
│   ├── JwtAuthenticationFilter.java
│   ├── JwtAuthorizationFilter.java
│   ├── JwtTokenProvider.java
│   ├── PasswordEncoder.java
│   ├── SecurityContextHolder.java
│   └── UserPrincipal.java
├── websocket/
│   ├── WebSocketHandler.java
│   ├── LocationWebSocketHandler.java
│   ├── NotificationWebSocketHandler.java
│   ├── MonitoringWebSocketHandler.java
│   └── WebSocketMessage.java
├── util/
│   ├── DateUtils.java
│   ├── StringUtils.java
│   ├── ValidationUtils.java
│   ├── EncryptionUtils.java
│   ├── LocationUtils.java
│   ├── GeofencingUtils.java
│   ├── NotificationUtils.java
│   └── ReportUtils.java
├── constant/
│   ├── ApiConstants.java
│   ├── SecurityConstants.java
│   ├── ErrorConstants.java
│   ├── ValidationConstants.java
│   └── BusinessConstants.java
└── aspect/
    ├── LoggingAspect.java
    ├── SecurityAspect.java
    ├── PerformanceAspect.java
    └── ValidationAspect.java
```

### 2. Test Structure
```
src/test/java/com/safeguard/parent/
├── controller/
│   ├── AuthControllerTest.java
│   ├── DashboardControllerTest.java
│   ├── ChildControllerTest.java
│   ├── LocationControllerTest.java
│   ├── GeofencingControllerTest.java
│   ├── MonitoringControllerTest.java
│   ├── NotificationControllerTest.java
│   ├── FamilyControllerTest.java
│   ├── SettingsControllerTest.java
│   ├── EmergencyControllerTest.java
│   ├── ReportsControllerTest.java
│   └── AccessibilityControllerTest.java
├── service/
│   ├── AuthServiceTest.java
│   ├── DashboardServiceTest.java
│   ├── ChildServiceTest.java
│   ├── LocationServiceTest.java
│   ├── GeofencingServiceTest.java
│   ├── MonitoringServiceTest.java
│   ├── NotificationServiceTest.java
│   ├── FamilyServiceTest.java
│   ├── SettingsServiceTest.java
│   ├── EmergencyServiceTest.java
│   ├── ReportsServiceTest.java
│   └── AccessibilityServiceTest.java
├── repository/
│   ├── UserRepositoryTest.java
│   ├── ChildRepositoryTest.java
│   ├── LocationRepositoryTest.java
│   ├── GeofenceRepositoryTest.java
│   ├── MonitoringDataRepositoryTest.java
│   ├── NotificationRepositoryTest.java
│   ├── FamilyRepositoryTest.java
│   ├── SettingsRepositoryTest.java
│   ├── EmergencyContactRepositoryTest.java
│   └── ReportRepositoryTest.java
├── integration/
│   ├── AuthIntegrationTest.java
│   ├── DashboardIntegrationTest.java
│   ├── ChildIntegrationTest.java
│   ├── LocationIntegrationTest.java
│   ├── GeofencingIntegrationTest.java
│   ├── MonitoringIntegrationTest.java
│   ├── NotificationIntegrationTest.java
│   ├── FamilyIntegrationTest.java
│   ├── SettingsIntegrationTest.java
│   ├── EmergencyIntegrationTest.java
│   └── ReportsIntegrationTest.java
├── util/
│   ├── DateUtilsTest.java
│   ├── StringUtilsTest.java
│   ├── ValidationUtilsTest.java
│   ├── EncryptionUtilsTest.java
│   ├── LocationUtilsTest.java
│   ├── GeofencingUtilsTest.java
│   ├── NotificationUtilsTest.java
│   └── ReportUtilsTest.java
└── config/
    ├── TestConfig.java
    ├── TestDatabaseConfig.java
    └── TestSecurityConfig.java
```

### 3. Resources Structure
```
src/main/resources/
├── application.yml
├── application-dev.yml
├── application-prod.yml
├── application-test.yml
├── logback-spring.xml
├── db/
│   ├── migration/
│   │   ├── V1__Create_initial_tables.sql
│   │   ├── V2__Add_user_tables.sql
│   │   ├── V3__Add_child_tables.sql
│   │   ├── V4__Add_location_tables.sql
│   │   ├── V5__Add_geofencing_tables.sql
│   │   ├── V6__Add_monitoring_tables.sql
│   │   ├── V7__Add_notification_tables.sql
│   │   ├── V8__Add_family_tables.sql
│   │   ├── V9__Add_settings_tables.sql
│   │   ├── V10__Add_emergency_tables.sql
│   │   └── V11__Add_reports_tables.sql
│   └── data/
│       ├── test-data.sql
│       └── sample-data.sql
├── static/
│   ├── images/
│   ├── icons/
│   └── fonts/
├── templates/
│   ├── email/
│   │   ├── welcome.html
│   │   ├── password-reset.html
│   │   ├── notification.html
│   │   └── emergency.html
│   └── reports/
│       ├── safety-report.html
│       ├── location-report.html
│       └── alert-report.html
└── i18n/
    ├── messages.properties
    ├── messages_en.properties
    ├── messages_ar.properties
    └── messages_es.properties
```

## Key Classes and Their Responsibilities

### 1. Controllers
- **AuthController**: Handles authentication, registration, password reset
- **DashboardController**: Manages dashboard data and family overview
- **ChildController**: CRUD operations for child management
- **LocationController**: Real-time location tracking and history
- **GeofencingController**: Geofence creation, management, and alerts
- **MonitoringController**: AI-powered monitoring and alerts
- **NotificationController**: Notification management and preferences
- **FamilyController**: Family management and invitations
- **SettingsController**: User settings and preferences
- **EmergencyController**: Emergency features and SOS functionality
- **ReportsController**: Report generation and analytics
- **AccessibilityController**: Accessibility features and internationalization

### 2. Services
- **AuthService**: Authentication business logic
- **DashboardService**: Dashboard data aggregation
- **ChildService**: Child management business logic
- **LocationService**: Location tracking and analytics
- **GeofencingService**: Geofence calculations and monitoring
- **MonitoringService**: AI monitoring and analysis
- **NotificationService**: Notification delivery and management
- **FamilyService**: Family management and collaboration
- **SettingsService**: Settings management and validation
- **EmergencyService**: Emergency response and SOS
- **ReportsService**: Report generation and analytics
- **AccessibilityService**: Accessibility and i18n features

### 3. Entities
- **User**: User account information
- **Child**: Child profile and information
- **Location**: Location data and history
- **Geofence**: Geofence definitions and settings
- **MonitoringData**: AI monitoring data and results
- **Notification**: Notification records and preferences
- **Family**: Family relationships and permissions
- **Settings**: User settings and preferences
- **EmergencyContact**: Emergency contact information
- **Report**: Generated reports and analytics

### 4. DTOs
- **Request DTOs**: Input validation and data transfer
- **Response DTOs**: Output formatting and data transfer
- **Common DTOs**: Shared response structures

### 5. Repositories
- **JPA Repositories**: Database access and queries
- **Custom Queries**: Complex database operations
- **Native Queries**: Performance-critical operations

## Configuration Files

### Maven Dependencies (pom.xml)
```xml
<dependencies>
    <!-- Spring Boot Starters -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-websocket</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-mail</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    
    <!-- Database -->
    <dependency>
        <groupId>org.postgresql</groupId>
        <artifactId>postgresql</artifactId>
    </dependency>
    <dependency>
        <groupId>org.hibernate</groupId>
        <artifactId>hibernate-spatial</artifactId>
    </dependency>
    
    <!-- JWT -->
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt-api</artifactId>
    </dependency>
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt-impl</artifactId>
    </dependency>
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt-jackson</artifactId>
    </dependency>
    
    <!-- AWS -->
    <dependency>
        <groupId>com.amazonaws</groupId>
        <artifactId>aws-java-sdk-s3</artifactId>
    </dependency>
    
    <!-- External Services -->
    <dependency>
        <groupId>com.twilio.sdk</groupId>
        <artifactId>twilio</artifactId>
    </dependency>
    <dependency>
        <groupId>com.google.maps</groupId>
        <artifactId>google-maps-services</artifactId>
    </dependency>
    
    <!-- Testing -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.testcontainers</groupId>
        <artifactId>junit-jupiter</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.testcontainers</groupId>
        <artifactId>postgresql</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

### Application Configuration (application.yml)
```yaml
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
      ddl-auto: validate
    show-sql: false
    properties:
      hibernate:
        dialect: org.hibernate.dialect.PostgreSQLDialect
        format_sql: true
  
  redis:
    host: ${REDIS_HOST:localhost}
    port: ${REDIS_PORT:6379}
    password: ${REDIS_PASSWORD:}
  
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

server:
  port: ${PORT:8080}
  servlet:
    context-path: /api/v1

management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics
  endpoint:
    health:
      show-details: always

logging:
  level:
    com.safeguard.parent: DEBUG
    org.springframework.web: INFO
  pattern:
    console: "%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n"
```

## Key Features of This Structure

1. **Modular Architecture**: Each feature has its own controller, service, and repository
2. **Clean Code**: Separation of concerns with clear responsibilities
3. **Type Safety**: Comprehensive DTOs and entities
4. **Security**: JWT authentication and authorization
5. **Testing**: Comprehensive test coverage
6. **Documentation**: Swagger/OpenAPI integration
7. **Monitoring**: Actuator endpoints for health checks
8. **Caching**: Redis integration for performance
9. **WebSocket**: Real-time communication support
10. **Internationalization**: Multi-language support

This structure provides a solid foundation for building a scalable, maintainable, and professional Spring Boot application for the SafeGuard Parent App backend.

