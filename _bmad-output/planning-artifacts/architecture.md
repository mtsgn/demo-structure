# RMN-Arms Backend Architecture Document

## Metadata

| Field | Value |
|-------|-------|
| **Version** | 1.0 |
| **Created** | 2026-02-02 |
| **Status** | Draft |
| **Author** | Generated from source code analysis |

---

## 1. Tổng quan Kiến trúc

### 1.1 Kiến trúc Microservices

Hệ thống RMN-Arms Backend sử dụng kiến trúc **Microservices** với các thành phần chính:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              CLIENT APPLICATIONS                             │
│                    (Web, Mobile, Device, Third-party)                        │
└─────────────────────────────────────────────────────────────────────────────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         API GATEWAY (mtsgn-system-gateway-svc)              │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────────────┐   │
│  │   CORS   │ │  Logger  │ │  Auth    │ │Rate Limit│ │  Priority Router │   │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘ └──────────────────┘   │
└─────────────────────────────────────────────────────────────────────────────┘
                                      │
              ┌───────────────────────┼───────────────────────┐
              │                       │                       │
              ▼                       ▼                       ▼
┌─────────────────────┐ ┌─────────────────────┐ ┌─────────────────────┐
│   Campaign Service  │ │   Supplier Service  │ │   Device Service    │
│   (Future: Epic 2)  │ │   (Future: Epic 3)  │ │   (Future: Epic 4)  │
└─────────────────────┘ └─────────────────────┘ └─────────────────────┘
              │                       │                       │
              └───────────────────────┼───────────────────────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                              DATA LAYER                                      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │ CockroachDB  │  │    Redis     │  │    Kafka     │  │  S3/MinIO    │     │
│  │  (Primary)   │  │   (Cache)    │  │  (Events)    │  │  (Storage)   │     │
│  └──────────────┘  └──────────────┘  └──────────────┘  └──────────────┘     │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 1.2 Services Inventory

| Service | Repository | Mô tả | Status |
|---------|------------|-------|--------|
| **mtsgn-system-gateway-svc** | Gateway | API Gateway với auth, rate limit, proxy | ✅ Implemented |
| **mtsgn-source-base-svc** | Template | Base template cho microservices mới | ✅ Template Ready |
| **mtsgn-aps-be-common-svc** | Shared Lib | Common libraries (token, middleware, cache) | ✅ Implemented |
| **mtsgn-system-common-svc** | gRPC Proto | gRPC definitions và shared utilities | ✅ Implemented |
| **mtsgn-proposal-architect-svc** | User Service | User/Profile management | ✅ Implemented |
| **mtsgn-access-user-svc** | Access Control | User access management | 🔄 In Progress |

---

## 2. Tech Stack

### 2.1 Core Technologies

| Category | Technology | Version | Purpose |
|----------|------------|---------|---------|
| **Language** | Go | 1.21+ | Primary backend language |
| **Database** | CockroachDB | Latest | Distributed SQL database |
| **Cache** | Redis | 7.x | Caching, rate limiting, sessions |
| **Message Queue** | Kafka | Latest | Event streaming |
| **Object Storage** | S3/MinIO | Latest | Media content storage |
| **Container** | Docker | Latest | Containerization |

### 2.2 Go Libraries

| Library | Purpose | Location |
|---------|---------|----------|
| **Gin** | HTTP router & framework | All services |
| **Viper** | Configuration management | `pkg/config/` |
| **GORM** | ORM for database | `pkg/database/` |
| **Zerolog** | Structured logging | `common/logger/` |
| **PASETO** | Token authentication | `common/token/` |
| **go-redis** | Redis client | `pkg/redis/` |
| **Goose** | Database migrations | `pkg/database/migrate.go` |

---

## 3. Cấu trúc Service chuẩn

### 3.1 Folder Structure (từ mtsgn-source-base-svc)

```
service-name/
├── main.go                          # Entry point
├── Dockerfile                       # Container build
├── Makefile                         # Build automation
├── docker-compose.yml               # Local development
├── go.mod / go.sum                  # Dependencies
│
├── config/
│   └── app.development.yaml         # Environment configs
│
├── internal/                        # Private application code
│   ├── app/
│   │   ├── server.go                # HTTP server initialization
│   │   └── routes/
│   │       ├── router.go            # Router setup
│   │       └── v1/routes.go         # API v1 endpoints
│   │
│   ├── common/
│   │   ├── app.go                   # Application context
│   │   └── errors/errors.go         # Error definitions
│   │
│   ├── dto/                         # Data Transfer Objects
│   │   ├── response.go              # Standard response format
│   │   └── {domain}.go              # Domain-specific DTOs
│   │
│   ├── models/                      # Database models
│   │   ├── base.go                  # Base model (ID, timestamps)
│   │   └── {domain}.go              # Domain entities
│   │
│   ├── repositories/                # Data access layer
│   │   └── {domain}.go              # Repository implementations
│   │
│   ├── logic/                       # Business logic
│   │   └── {domain}/logic.go        # Domain logic
│   │
│   ├── services/                    # Service layer (DI container)
│   │   └── {domain}.go              # Service registration
│   │
│   ├── presentation/
│   │   ├── handler/{domain}.go      # HTTP handlers
│   │   └── middlewares/             # Middleware implementations
│   │       ├── auth.go
│   │       ├── logger.go
│   │       └── request_id.go
│   │
│   └── migrations/                  # Database migrations
│       ├── fs.go                    # Embed migrations
│       └── *.sql                    # Migration files
│
└── pkg/                             # Reusable packages
    ├── config/
    │   ├── config.go                # Config loading (Viper)
    │   └── model.go                 # Config structs
    ├── database/
    │   ├── cockroad.go              # CockroachDB connection
    │   ├── migrate.go               # Goose migrations
    │   └── goose_logger.go          # Migration logger
    ├── redis/
    │   └── redis.go                 # Redis client
    ├── grpc/
    │   └── grpcclient.go            # gRPC client utils
    └── utils/                       # Utilities
        ├── file.go
        ├── str.go
        └── time.go
```

### 3.2 Layer Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    PRESENTATION LAYER                        │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │
│  │  Handlers   │  │ Middlewares │  │      Routes         │  │
│  │  (HTTP)     │  │ (Auth,Log)  │  │   (Gin Router)      │  │
│  └─────────────┘  └─────────────┘  └─────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                     SERVICE LAYER                            │
│  ┌─────────────────────────────────────────────────────┐    │
│  │               Services (DI Container)                │    │
│  └─────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                    BUSINESS LOGIC LAYER                      │
│  ┌─────────────────────────────────────────────────────┐    │
│  │                    Logic                              │    │
│  │           (Business Rules, Validation)                │    │
│  └─────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                    DATA ACCESS LAYER                         │
│  ┌─────────────────────────────────────────────────────┐    │
│  │                  Repositories                         │    │
│  │              (GORM, Database Access)                  │    │
│  └─────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                      DATA LAYER                              │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐    │
│  │  Models  │  │   DTOs   │  │  Cache   │  │  Events  │    │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘    │
└─────────────────────────────────────────────────────────────┘
```

---

## 4. API Gateway Architecture

### 4.1 Gateway Components

```go
// Config structure từ mtsgn-system-gateway-svc/pkg/config/model.go
type Config struct {
    AppEnv    string          `mapstructure:"app_env"`
    LogLevel  string          `mapstructure:"log_level"`
    Server    ServerConfig    `mapstructure:"server"`
    Redis     RedisConfig     `mapstructure:"redis"`
    Auth      AuthConfig      `mapstructure:"auth"`
    RateLimit RateLimitConfig `mapstructure:"rate_limit"`
    Services  []ServiceConfig `mapstructure:"services"`
}

type ServiceConfig struct {
    Name     string   `mapstructure:"name"`
    BasePath string   `mapstructure:"base_path"`
    Target   string   `mapstructure:"target"`
    Methods  []string `mapstructure:"methods"`
    SkipAuth bool     `mapstructure:"skip_auth"`
}
```

### 4.2 Gateway Features

| Feature | Implementation | Description |
|---------|---------------|-------------|
| **CORS** | `internal/middleware/cors.go` | Cross-Origin Resource Sharing |
| **Logger** | `internal/middleware/logger.go` | Request/Response logging |
| **Metrics** | `internal/middleware/metrics.go` | Prometheus metrics |
| **Auth** | `internal/handlers/auth.go` | JWT/PASETO authentication |
| **Rate Limit** | `internal/handlers/rate_limit.go` | Sliding window rate limiting |
| **Proxy** | `internal/handlers/proxy.go` | Request proxying to services |
| **Priority Router** | `internal/server/server.go` | Path-based routing with priority |

### 4.3 Priority Router

Gateway sử dụng Priority Router để route requests dựa trên path specificity:

```go
// Depth-based priority - more specific paths have higher priority
service.Priority = len(segments)

// Example:
// /api/v1/campaigns/{id}     → Priority: 4
// /api/v1/campaigns          → Priority: 3
// /api/v1/*                  → Priority: 2
```

---

## 5. Common Libraries

### 5.1 mtsgn-aps-be-common-svc

```
common/
├── token/                    # PASETO Token Management
│   ├── token.go              # Token interface
│   ├── paseto.go             # PASETO implementation
│   └── paseto_test.go        # Unit tests
│
├── middleware/               # Shared Middlewares
│   ├── cors.go               # CORS middleware
│   ├── cors_test.go
│   ├── ratelimit.go          # Rate limiting
│   └── ratelimit_test.go
│
├── logger/                   # Logging
│   ├── logger.go             # Zerolog wrapper
│   ├── type.go               # Log types
│   └── opentelemetry.go      # Tracing integration
│
├── cache/                    # Caching
│   ├── cache.go              # Cache interface
│   ├── redis.go              # Redis implementation
│   └── redis_test.go
│
├── config/                   # Configuration
│   ├── config.go             # Viper-based config
│   └── config_test.go
│
├── errorx/                   # Error Handling
│   └── errorx.go             # Error types & codes
│
└── utils/                    # Utilities
    └── str.go                # String utilities
```

### 5.2 mtsgn-system-common-svc (gRPC)

```
grpc/
├── pb/                       # Protocol Buffers
│   ├── store.pb.go
│   ├── store_svc.pb.go
│   ├── store_svc_grpc.pb.go
│   ├── purchase_order.pb.go
│   ├── inventory_transfer.pb.go
│   └── payment_company_config.pb.go
│
├── context/                  # gRPC Context
│   └── add_variable.go
│
└── grpcclient/               # gRPC Client
    └── grpc_log.go
```

---

## 6. Authentication & Authorization

### 6.1 PASETO Token

```go
// Token structure
type TokenPayload struct {
    UserID    string
    Role      string
    ExpiresAt time.Time
}

// Config
type AuthConfig struct {
    JWTSecret                  string
    AccessTokenExpirationTime  int  // minutes
    RefreshTokenExpirationTime int  // minutes
}
```

### 6.2 Auth Flow

```
┌────────┐     ┌─────────┐     ┌────────────┐     ┌─────────────┐
│ Client │────▶│ Gateway │────▶│ Auth Check │────▶│   Service   │
└────────┘     └─────────┘     └────────────┘     └─────────────┘
    │                              │
    │  1. Request + Token          │
    │                              │
    │              2. Validate Token (PASETO)
    │                              │
    │              3. Extract Claims
    │                              │
    │              4. Check Permissions
    │                              │
    │              5. Proxy to Service
    │                              │
    ◀──────────────────────────────┘
         6. Response
```

---

## 7. Configuration Management

### 7.1 Config Loading (Viper)

```go
// pkg/config/config.go
func LoadConfig(path string) (*Config, error) {
    viper.SetConfigFile(path)
    viper.AutomaticEnv()

    if err := viper.ReadInConfig(); err != nil {
        return nil, err
    }

    var config Config
    if err := viper.Unmarshal(&config); err != nil {
        return nil, err
    }

    return &config, nil
}
```

### 7.2 Config Structure

```yaml
# config/app.development.yaml
app_env: development
log_level: debug

http_server:
  port: 8080
  log_level: debug

cockroachdb:
  host: localhost
  port: 26257
  user: root
  password: ""
  database: mydb
  ssl_mode: disable

redis:
  host: localhost
  port: 6379
  password: ""
  db: 0
```

---

## 8. Database Migrations

### 8.1 Goose Migrations

```go
// internal/migrations/fs.go
//go:embed *.sql
var FS embed.FS

// pkg/database/migrate.go
func Migrate(db *gorm.DB, fs embed.FS) error {
    goose.SetBaseFS(fs)
    goose.SetDialect("postgres")

    sqlDB, _ := db.DB()
    return goose.Up(sqlDB, ".")
}
```

### 8.2 Migration Naming

```
internal/migrations/
├── 20251214110354_first_migrate.sql
├── 20251215120000_add_users_table.sql
└── 20251216130000_add_campaigns_table.sql
```

---

## 9. Error Handling

### 9.1 Error Types

```go
// common/errorx/errorx.go
type AppError struct {
    Code    int
    Message string
    Err     error
}

const (
    ErrInvalidRequest = 400
    ErrUnauthorized   = 401
    ErrForbidden      = 403
    ErrNotFound       = 404
    ErrInternal       = 500
)
```

### 9.2 Response Format

```go
// internal/dto/response.go
type Response struct {
    Success bool        `json:"success"`
    Data    interface{} `json:"data,omitempty"`
    Error   *ErrorInfo  `json:"error,omitempty"`
}

type ErrorInfo struct {
    Code    int    `json:"code"`
    Message string `json:"message"`
}
```

---

## 10. Logging & Observability

### 10.1 Structured Logging (Zerolog)

```go
// common/logger/logger.go
type Config struct {
    Level   Level
    Service string
}

func NewLogger(cfg Config) (*zerolog.Logger, error) {
    level := zerolog.Level(cfg.Level)
    logger := zerolog.New(os.Stdout).
        Level(level).
        With().
        Timestamp().
        Str("service", cfg.Service).
        Logger()

    return &logger, nil
}
```

### 10.2 Request ID Middleware

```go
// internal/presentation/middlewares/request_id.go
func RequestID() gin.HandlerFunc {
    return func(c *gin.Context) {
        requestID := c.GetHeader("X-Request-ID")
        if requestID == "" {
            requestID = uuid.New().String()
        }
        c.Set("request_id", requestID)
        c.Header("X-Request-ID", requestID)
        c.Next()
    }
}
```

---

## 11. Deployment

### 11.1 Dockerfile

```dockerfile
# Multi-stage build
FROM golang:1.21-alpine AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -o main .

FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/main .
COPY --from=builder /app/config ./config
EXPOSE 8080
CMD ["./main"]
```

### 11.2 Docker Compose (Development)

```yaml
version: '3.8'
services:
  app:
    build: .
    ports:
      - "8080:8080"
    environment:
      - APP_ENV=development
    depends_on:
      - cockroachdb
      - redis

  cockroachdb:
    image: cockroachdb/cockroach:latest
    ports:
      - "26257:26257"
    command: start-single-node --insecure

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
```

---

## 12. Mapping với Epics

| Epic | Service | Status | Notes |
|------|---------|--------|-------|
| **Epic 1: Foundation** | `mtsgn-system-gateway-svc`, `mtsgn-source-base-svc` | ✅ Done | Gateway, base template có sẵn |
| **Epic 2: Campaign** | New service (clone từ base-svc) | 🔄 To-do | |
| **Epic 3: Supplier** | New service | 🔄 To-do | |
| **Epic 4: Device** | New service | 🔄 To-do | |
| **Epic 5: Billing** | New service | 🔄 To-do | |
| **Epic 6: CMS** | New service | 🔄 To-do | |
| **Epic 7: Blocking** | New service | 🔄 To-do | |
| **Epic 8: Admin** | New service | 🔄 To-do | |

---

## 13. Appendix

### 13.1 Coding Standards (từ .cursor/rules)

- **biz_rule.mdc** - Business logic rules
- **dto_rule.mdc** - DTO conventions
- **handler_rule.mdc** - Handler patterns
- **logic_rule.mdc** - Logic layer rules
- **repository_rule.mdc** - Repository patterns

### 13.2 References

- Source code: `mtsgn-source-base-svc/` (template)
- Gateway: `mtsgn-system-gateway-svc/`
- Common libs: `mtsgn-aps-be-common-svc/`
- gRPC protos: `mtsgn-system-common-svc/`
