---
name: designing-api-contracts
description: RESTful API design following best practices with endpoint mapping, schema definition, and error handling. Use when mapping endpoints, defining schemas, or when you see "API", "endpoint", "REST", "OpenAPI", "schema", "contract", or "HTTP".
---

# Designing API Contracts

## Purpose

Design RESTful API contracts that map user actions to endpoints with complete schema definitions and comprehensive error handling. This skill covers endpoint design, request/response schemas, and OpenAPI specification.

## Endpoint Mapping

### User Action → Endpoint Mapping

| User Action | HTTP Method | Endpoint Pattern |
|-------------|-------------|------------------|
| Create resource | POST | `/resources` |
| List resources | GET | `/resources` |
| Get single resource | GET | `/resources/{id}` |
| Update resource | PUT/PATCH | `/resources/{id}` |
| Delete resource | DELETE | `/resources/{id}` |
| Perform action | POST | `/resources/{id}/{action}` |
| Get nested resource | GET | `/resources/{id}/children` |

### Method Selection

| Scenario | Method | Idempotent? |
|----------|--------|-------------|
| Create new resource | POST | No |
| Full replacement | PUT | Yes |
| Partial update | PATCH | No |
| Read resource | GET | Yes |
| Remove resource | DELETE | Yes |
| Trigger action | POST | Usually No |

### Resource Naming

```markdown
## Naming Conventions

- Use plural nouns: `/users`, not `/user`
- Use kebab-case for multi-word: `/user-profiles`
- Use path params for IDs: `/users/{userId}`
- Use query params for filtering: `/users?role=admin`
- Use nested paths for relationships: `/users/{userId}/tasks`
```

## Endpoint Format

### Standard Endpoint Documentation

```markdown
## POST /api/auth/login

**Description**: Authenticate user with email and password

**Source Requirements**: FR-001, US#1

### Request

```json
{
  "email": "user@example.com",
  "password": "securePassword123"
}
```

### Response (200 OK)

```json
{
  "user": {
    "id": "uuid",
    "email": "user@example.com",
    "name": "John Doe"
  },
  "accessToken": "eyJhbGc...",
  "refreshToken": "eyJhbGc...",
  "expiresIn": 900
}
```

### Error Responses

| Status | Code | Description |
|--------|------|-------------|
| 400 | INVALID_INPUT | Missing or malformed fields |
| 401 | INVALID_CREDENTIALS | Wrong email or password |
| 403 | ACCOUNT_DISABLED | Account is disabled |
| 429 | RATE_LIMITED | Too many attempts |
```

## Schema Definition

### Request Schema Format

```yaml
LoginRequest:
  type: object
  required:
    - email
    - password
  properties:
    email:
      type: string
      format: email
      description: User's email address
      example: user@example.com
    password:
      type: string
      minLength: 8
      description: User's password
      example: securePassword123
```

### Response Schema Format

```yaml
UserResponse:
  type: object
  required:
    - id
    - email
  properties:
    id:
      type: string
      format: uuid
      description: Unique identifier
    email:
      type: string
      format: email
      description: User's email
    name:
      type: string
      nullable: true
      description: Display name
    createdAt:
      type: string
      format: date-time
      description: Creation timestamp
```

### Schema from Data Model

Translate data model entities to schemas:

| Data Model Type | OpenAPI Type | Format |
|-----------------|--------------|--------|
| UUID | string | uuid |
| Text | string | - |
| Email | string | email |
| URL | string | uri |
| Integer | integer | int32/int64 |
| Decimal | number | float/double |
| Boolean | boolean | - |
| Timestamp | string | date-time |
| Date | string | date |
| Enum[a,b,c] | string | enum: [a,b,c] |

## Error Response Design

### Standard Error Format

```yaml
ErrorResponse:
  type: object
  required:
    - code
    - message
  properties:
    code:
      type: string
      description: Machine-readable error code
      example: INVALID_CREDENTIALS
    message:
      type: string
      description: Human-readable message
      example: Invalid email or password
    details:
      type: object
      additionalProperties: true
      description: Additional error context
```

### HTTP Status Codes

| Status | When to Use | Example Codes |
|--------|-------------|---------------|
| 400 Bad Request | Invalid input format | INVALID_INPUT, VALIDATION_ERROR |
| 401 Unauthorized | Missing/invalid auth | UNAUTHORIZED, TOKEN_EXPIRED |
| 403 Forbidden | No permission | FORBIDDEN, ACCESS_DENIED |
| 404 Not Found | Resource missing | NOT_FOUND, USER_NOT_FOUND |
| 409 Conflict | State conflict | CONFLICT, ALREADY_EXISTS |
| 422 Unprocessable | Business rule violation | UNPROCESSABLE, RULE_VIOLATION |
| 429 Too Many | Rate limit | RATE_LIMITED |
| 500 Server Error | Unexpected error | INTERNAL_ERROR |

### Endpoint-Specific Errors

```markdown
## Error Responses: POST /api/users

| Status | Code | Condition |
|--------|------|-----------|
| 400 | INVALID_EMAIL | Email format invalid |
| 400 | INVALID_PASSWORD | Password too weak |
| 409 | EMAIL_EXISTS | Email already registered |
| 422 | TERMS_NOT_ACCEPTED | Terms acceptance required |
| 429 | RATE_LIMITED | Too many registration attempts |
```

## Brownfield Considerations

### Matching Existing Patterns

When existing API patterns are detected:

```markdown
## Brownfield API Alignment

### Detected Patterns

| Aspect | Existing Pattern | Apply? |
|--------|------------------|--------|
| Base path | `/api/v1` | Yes |
| Auth | Bearer token | Yes |
| Error format | `{error: {code, message}}` | Yes |
| Pagination | `?page=1&limit=20` | Yes |

### Endpoint Collision Handling

| Proposed | Existing | Action |
|----------|----------|--------|
| GET /api/users | GET /api/users | REUSE existing |
| POST /api/auth/login | POST /api/login | RENAME to match |
| POST /api/sessions | (none) | NEW endpoint |
```

## OpenAPI Structure

```yaml
openapi: 3.0.3
info:
  title: {Feature Name} API
  version: 1.0.0
  description: API contracts for {feature_id}

servers:
  - url: /api
    description: API base path

paths:
  /auth/login:
    post:
      summary: Authenticate user
      operationId: login
      tags:
        - Authentication
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/LoginRequest'
      responses:
        '200':
          description: Successful login
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/LoginResponse'
        '401':
          description: Invalid credentials
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'

components:
  schemas:
    LoginRequest:
      # ... schema definition
    LoginResponse:
      # ... schema definition
    ErrorResponse:
      # ... error schema

  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

security:
  - bearerAuth: []
```

## Traceability

Track endpoint to requirement mapping:

```markdown
## Endpoint Traceability

| Endpoint | Method | FR | US | Description |
|----------|--------|-----|-----|-------------|
| /auth/login | POST | FR-001 | US#1 | User login |
| /auth/logout | POST | FR-002 | US#2 | User logout |
| /auth/refresh | POST | FR-003 | US#3 | Token refresh |
| /users/me | GET | FR-004 | US#4 | Get current user |
```

## Validation Script

Validate OpenAPI specifications for syntax, REST conventions, and completeness:

```bash
python scripts/validate-openapi.py path/to/openapi.yaml
```

**Requirements:** PyYAML (`pip install pyyaml`) for YAML files, or use JSON format.

**Output:**
```json
{
  "file": "contracts/api.yaml",
  "valid_openapi": true,
  "openapi_version": "3.0.3",
  "paths_count": 5,
  "schemas_count": 8,
  "checks": [...],
  "summary": {"total": 10, "passed": 9, "failed": 1}
}
```

The script checks:
- **openapi_version**: Valid OpenAPI 3.x syntax
- **info_section**: Required title and version present
- **plural_nouns**: REST convention for resource names
- **kebab_case**: No camelCase or snake_case in paths
- **error_responses**: 4xx/5xx responses defined
- **request_bodies**: POST/PUT/PATCH have request bodies
- **operation_ids**: Unique operationId for each endpoint
- **security_schemes**: securitySchemes defined if security used
- **schema_examples**: Example values in schemas
- **descriptions**: Summary/description for operations

**Usage pattern:**
1. Design API contracts using this skill
2. Generate OpenAPI spec (YAML or JSON)
3. Run validation script to check quality
4. Fix any issues before presenting to user

## Quality Checklist

Before finalizing API contracts, verify:

- [ ] Every user action has an endpoint
- [ ] All endpoints have request schema (if applicable)
- [ ] All endpoints have success response schema
- [ ] All endpoints have error responses defined
- [ ] Naming follows REST conventions
- [ ] Authentication requirements documented
- [ ] Brownfield patterns matched (if applicable)
- [ ] OpenAPI spec is valid
- [ ] Traceability to requirements complete

## Anti-Patterns to Avoid

- **Verb in URL**: Use `/users` not `/getUsers`
- **Action without POST**: Use `POST /users/{id}/archive` not `GET /users/{id}/archive`
- **Missing errors**: Every endpoint needs error responses
- **Inconsistent naming**: Don't mix `/userProfiles` and `/user-settings`
- **Undocumented auth**: Every protected endpoint needs security defined
- **Generic errors**: Use specific error codes, not just 400/500
- **No examples**: Include realistic example values
