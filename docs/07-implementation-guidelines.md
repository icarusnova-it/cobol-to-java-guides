# Phase 7: Implementation Guidelines

> **Icarus Nova** | Guidelines for implementing COBOL logic in maintainable Java.

## Overview

Implementation Guidelines provide standards and patterns for converting COBOL business logic into maintainable Java code. This phase focuses on contract-first development, component-based rewrite, and transaction management.

## Objectives

1. **Contract-First Development**: Define contracts before implementation
2. **Component-Based Rewrite**: Rewrite by components, not files
3. **Transaction Management**: Proper transaction handling
4. **Coding Standards**: Consistent code quality
5. **Testing Standards**: Comprehensive testing approach

## Step 7.1: Contract-First Development

### Contract-First Principle

**Approach:**
- Define APIs and contracts first
- Implement contracts
- Version contracts
- Test against contracts

**Benefits:**
- Clear interfaces
- Independent development
- Version management
- Integration testing

### Contract Definition

**API Contracts:**
- OpenAPI specification
- Request/response schemas
- Error responses
- Versioning strategy

**Service Contracts:**
- Service interfaces
- Method signatures
- Exception definitions
- Return types

### Contract Versioning

**Versioning Strategy:**
- Semantic versioning
- Backward compatibility
- Deprecation process
- Migration path

## Step 7.2: Component-Based Rewrite

### Rewrite Approach

**COBOL Structure:**
- Paragraphs and sections
- Procedural logic
- File-based organization

**Java Structure:**
- Use cases / services
- Domain-driven organization
- Object-oriented design

### Mapping Strategy

**COBOL → Java Mapping:**

**COBOL Paragraph → Java Method:**
```
COBOL:
  VALIDATE-CUSTOMER.
    IF WS-CUSTOMER-AGE < 18
      MOVE 'INVALID' TO WS-STATUS
    END-IF.

Java:
  public ValidationResult validateCustomer(Customer customer) {
      if (customer.getAge() < 18) {
          return ValidationResult.invalid("Customer must be 18+");
      }
      return ValidationResult.valid();
  }
```

**COBOL Section → Java Service:**
```
COBOL:
  PROCESS-CUSTOMER SECTION.
    PERFORM VALIDATE-CUSTOMER
    PERFORM CALCULATE-CREDIT
    PERFORM UPDATE-CUSTOMER

Java:
  @Service
  public class CustomerService {
      public Customer processCustomer(Customer customer) {
          validateCustomer(customer);
          calculateCredit(customer);
          return updateCustomer(customer);
      }
  }
```

### Service Design

**Service Responsibilities:**
- Business logic
- Orchestration
- Validation
- Transformation

**Service Structure:**
```
Service Interface
    ↓
Service Implementation
    ↓
Repository (Data Access)
    ↓
Domain Model
```

## Step 7.3: Transaction Management

### Transaction Patterns

**COBOL Transaction Handling:**
- SQL COMMIT/ROLLBACK
- File locking
- VSAM record locking
- CICS transaction boundaries

**Java Transaction Handling:**
- @Transactional annotations
- Programmatic transactions
- Distributed transactions
- Saga pattern (if needed)

### Transaction Mapping

**COBOL → Java:**

**COBOL:**
```cobol
EXEC SQL
    UPDATE CUSTOMER
    SET BALANCE = :WS-BALANCE
    WHERE CUSTOMER-ID = :WS-CUSTOMER-ID
END-EXEC

IF SQLCODE NOT = 0
    EXEC SQL ROLLBACK END-EXEC
ELSE
    EXEC SQL COMMIT END-EXEC
END-IF
```

**Java:**
```java
@Transactional
public void updateCustomerBalance(String customerId, BigDecimal balance) {
    try {
        customerRepository.updateBalance(customerId, balance);
        // Transaction commits automatically
    } catch (Exception e) {
        // Transaction rolls back automatically
        throw new CustomerUpdateException("Failed to update balance", e);
    }
}
```

### Idempotency

**Idempotent Operations:**
- Safe to retry
- Idempotency keys
- Duplicate detection
- Consistent results

## Coding Standards

### Java Standards

**Code Style:**
- Follow Java conventions
- Use meaningful names
- Keep methods small
- Single responsibility

**Design Patterns:**
- Repository pattern
- Service pattern
- DTO pattern
- Builder pattern (where appropriate)

**Error Handling:**
- Custom exceptions
- Proper exception hierarchy
- Error messages
- Logging

### Code Organization

**Package Structure:**
```
com.company.domain
  ├── model          (Domain models)
  ├── service        (Business services)
  ├── repository     (Data access)
  ├── dto            (Data transfer objects)
  └── exception      (Custom exceptions)
```

## Deliverables

### 1. Coding Standards Document

**Content:**
- Code style guidelines
- Naming conventions
- Design patterns
- Error handling
- Documentation standards

### 2. Service Templates

**Templates:**
- Service interface template
- Service implementation template
- Repository template
- DTO template
- Test template

### 3. Repository Patterns

**Patterns:**
- Repository interface
- JPA implementation
- Custom queries
- Transaction management

### 4. Test Templates

**Templates:**
- Unit test template
- Integration test template
- Test data builders
- Mock configurations

## Best Practices

### Implementation Best Practices

1. **Contract-First**: Define contracts before implementation
2. **Component-Based**: Organize by domain, not by file
3. **Test-Driven**: Write tests as you develop
4. **Code Reviews**: All code reviewed
5. **Documentation**: Document complex logic

### Transaction Best Practices

1. **Declarative Transactions**: Use @Transactional
2. **Transaction Boundaries**: Clear boundaries
3. **Error Handling**: Proper rollback
4. **Idempotency**: Safe retries
5. **Performance**: Optimize transaction scope

## Related Documents

- [COBOL Paragraph to Java Service Example](../examples/cobol-paragraph-to-java-service-example.md)
- [Testing Strategy](./08-testing-strategy.md)
- [ADR-0004: Contract-First Integration](../adr/0004-contract-first-integration.md)

---

**Last Updated:** 2024  
**Maintained by:** Icarus Nova Architecture Team  
**Version:** 1.0
