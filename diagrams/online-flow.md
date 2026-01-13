# Online Flow Diagram

> **Icarus Nova** | Online/real-time processing flow from CICS to Java REST APIs.

## Overview

This diagram illustrates how COBOL CICS transactions are replaced with Java REST APIs for online/real-time processing.

## Online Flow Diagram

```mermaid
sequenceDiagram
    participant User
    participant CICS
    participant COBOL
    participant Database
    
    Note over User,Database: Legacy Online Flow (Before)
    User->>CICS: CICS Transaction
    CICS->>COBOL: Execute Program
    COBOL->>Database: Query Data
    Database-->>COBOL: Data
    COBOL->>COBOL: Process
    COBOL->>Database: Update
    COBOL-->>CICS: Response
    CICS-->>User: Display Screen
    
    Note over User,Database: Modern Online Flow (After)
    participant API
    participant Service
    participant NewDB
    
    User->>API: REST API Request
    API->>Service: Process Request
    Service->>NewDB: Query Data
    NewDB-->>Service: Data
    Service->>Service: Business Logic
    Service->>NewDB: Update
    Service-->>API: Response
    API-->>User: JSON Response
```

## Online Architecture

### Legacy Online (CICS/COBOL)

**Components:**
- CICS transaction monitor
- COBOL programs
- 3270 screens
- Database

**Flow:**
1. User enters transaction
2. CICS routes to COBOL program
3. COBOL processes
4. Updates database
5. Returns screen data
6. CICS displays screen

### Modern Online (Java REST)

**Components:**
- API Gateway
- REST APIs
- Java services
- Database
- Frontend (web/mobile)

**Flow:**
1. User sends API request
2. API Gateway routes
3. Service processes
4. Updates database
5. Returns JSON response
6. Frontend displays

## Transaction Mapping

### CICS Transaction → REST API

**Mapping Examples:**

| CICS Transaction | REST API | Method |
|------------------|----------|--------|
| CUST | GET /api/customers/{id} | GET |
| CUPD | PUT /api/customers/{id} | PUT |
| CADD | POST /api/customers | POST |
| CDEL | DELETE /api/customers/{id} | DELETE |

## State Management

### Legacy State Management

**CICS State:**
- Conversational transactions
- Screen state
- Transaction state
- Session state

### Modern State Management

**Stateless APIs:**
- No server-side state
- Client manages state
- Token-based authentication
- Session in token

## Related Documents

- [Target Architecture Java](../docs/05-target-architecture-java.md)
- [Implementation Guidelines](../docs/07-implementation-guidelines.md)

---

**Last Updated:** 2024  
**Maintained by:** Icarus Nova Architecture Team  
**Version:** 1.0
