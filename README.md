# OAuth2 POC - Authorization Server + Resource Server

Complete Spring Security OAuth2 implementation with **Authorization Server** issuing JWT tokens and **Resource Server** validating them.

## Architecture

```
┌──────────────────────────┐
│  Authorization Server    │  Port 8443
│  Issues JWT tokens       │  
│  Roles from CSV          │
└──────────┬───────────────┘
           │ JWT with roles
           │
           │ Bearer token in header
           ▼
┌──────────────────────────┐
│  Resource Server         │  Port 8080
│  Validates JWT           │
│  @PreAuthorize("ROLE_X") │
└──────────────────────────┘
```

## Projects

### 1. oauth2-authserver/
Spring Authorization Server that:
- Loads client registrations from CSV
- Loads user credentials from CSV
- Issues JWT tokens with role claims
- Supports `client_credentials` and `authorization_code` grant types
- Implements key rotation for JWT signing

### 2. microservice1/
Resource Server that:
- Validates JWT from Auth Server
- Extracts roles from JWT claims  
- Protects endpoints via `@PreAuthorize` annotations
- Supports both API access (JWT) and browser-based login

## Quick Start

### Start Both Servers

**Terminal 1** - Auth Server:
```bash
cd oauth2-authserver && mvn spring-boot:run
```

**Terminal 2** - Resource Server:  
```bash
cd microservice1 && mvn spring-boot:run
```

### Client Credentials Flow (API Access)

```bash
# Get token
TOKEN=$(curl -s -X POST http://localhost:8443/oauth2/token \
  -u inventoryclient:secret123 \
  -d "grant_type=client_credentials" | jq -r '.access_token')

# Use token
curl http://localhost:8080/endpoint1 \
  -H "Authorization: Bearer $TOKEN"
```

### Form Login (Browser Access)

1. Open browser to: `http://localhost:8443/login`
2. Login with:
   - Username: `admin`, Password: `admin123` (ROLE_ADMIN)
   - Username: `john.doe`, Password: `password123` (ROLE_CUSTOMER)

## Features

- ✅ CSV-driven client/role mappings
- ✅ CSV-driven user management
- ✅ JWT with custom claims
- ✅ Method-level security
- ✅ Key rotation
- ✅ Form login
- ✅ Authorization Code flow support

## Client Credentials

| Client | Secret | Role | Endpoint Access |
|--------|--------|------|-----------------|
| inventoryclient | secret123 | ROLE_ADMIN | /endpoint1 ✅ |
| cartopsclient | secret456 | ROLE_USER | /endpoint2 ✅ |
| reportsclient | secret789 | ROLE_MANAGER | /endpoint3 ✅ |

## Documentation

- [OAuth2 Concepts](OAUTH2_CONCEPTS.md) - Detailed explanation of OAuth2 flows
- [Testing Guide](TESTING_GUIDE.md) - Complete testing instructions
- [Implementation Details](IMPLEMENTATION_DETAILS.md) - Technical implementation details
- [Security Best Practices](SECURITY_BEST_PRACTICES.md) - Security considerations
