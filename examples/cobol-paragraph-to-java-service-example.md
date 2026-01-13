# COBOL Paragraph to Java Service Example

> **Icarus Nova** | Example mapping from COBOL paragraph to Java service method (conceptual, NOT production code).

## Overview

This document shows how to map COBOL paragraphs and sections to Java service methods, demonstrating the transformation from procedural COBOL to object-oriented Java. This is a **conceptual example** for educational purposes.

## COBOL Program

### Original COBOL Code

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. CUST001.
       *> Customer Processing Program
       
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-CUSTOMER-ID        PIC X(10).
       01 WS-CUSTOMER-NAME      PIC X(50).
       01 WS-CUSTOMER-AGE       PIC 9(3).
       01 WS-CUSTOMER-BALANCE   PIC S9(10)V9(2) COMP-3.
       01 WS-STATUS             PIC X(1).
       01 WS-ERROR-MESSAGE       PIC X(100).
       
       PROCEDURE DIVISION.
       MAIN-PROCESSING.
           PERFORM INITIALIZE-PROGRAM
           PERFORM READ-CUSTOMER-DATA
           PERFORM VALIDATE-CUSTOMER
           IF WS-STATUS = 'V'
               PERFORM CALCULATE-CREDIT-LIMIT
               PERFORM UPDATE-CUSTOMER
               PERFORM WRITE-RESULT
           ELSE
               PERFORM ERROR-HANDLING
           END-IF
           PERFORM FINALIZE-PROGRAM
           STOP RUN.
       
       INITIALIZE-PROGRAM.
           MOVE SPACES TO WS-CUSTOMER-ID
           MOVE SPACES TO WS-CUSTOMER-NAME
           MOVE ZERO TO WS-CUSTOMER-AGE
           MOVE ZERO TO WS-CUSTOMER-BALANCE
           MOVE 'V' TO WS-STATUS.
       
       READ-CUSTOMER-DATA.
           READ CUSTOMER-FILE
               AT END
                   MOVE 'E' TO WS-STATUS
                   MOVE 'Customer not found' TO WS-ERROR-MESSAGE
               NOT AT END
                   MOVE CUSTOMER-ID TO WS-CUSTOMER-ID
                   MOVE CUSTOMER-NAME TO WS-CUSTOMER-NAME
                   MOVE CUSTOMER-AGE TO WS-CUSTOMER-AGE
                   MOVE CUSTOMER-BALANCE TO WS-CUSTOMER-BALANCE
           END-READ.
       
       VALIDATE-CUSTOMER.
           IF WS-CUSTOMER-AGE < 18
               MOVE 'E' TO WS-STATUS
               MOVE 'Customer must be 18 or older' TO WS-ERROR-MESSAGE
           END-IF
           
           IF WS-CUSTOMER-BALANCE < 0
               MOVE 'E' TO WS-STATUS
               MOVE 'Customer balance cannot be negative' TO WS-ERROR-MESSAGE
           END-IF.
       
       CALCULATE-CREDIT-LIMIT.
           COMPUTE WS-CREDIT-LIMIT = WS-CUSTOMER-BALANCE * 1.5
           IF WS-CREDIT-LIMIT > 100000
               MOVE 100000 TO WS-CREDIT-LIMIT
           END-IF.
       
       UPDATE-CUSTOMER.
           EXEC SQL
               UPDATE CUSTOMER
               SET CREDIT-LIMIT = :WS-CREDIT-LIMIT
               WHERE CUSTOMER-ID = :WS-CUSTOMER-ID
           END-EXEC
           
           IF SQLCODE NOT = 0
               MOVE 'E' TO WS-STATUS
               MOVE 'Database update failed' TO WS-ERROR-MESSAGE
           END-IF.
       
       WRITE-RESULT.
           WRITE OUTPUT-RECORD FROM WS-CUSTOMER-ID
           WRITE OUTPUT-RECORD FROM WS-CREDIT-LIMIT.
       
       ERROR-HANDLING.
           DISPLAY 'Error: ' WS-ERROR-MESSAGE
           PERFORM FINALIZE-PROGRAM.
       
       FINALIZE-PROGRAM.
           CLOSE CUSTOMER-FILE
           CLOSE OUTPUT-FILE.
```

## Java Service Mapping

### Java Service Implementation

```java
package com.company.customer.service;

import com.company.customer.dto.CustomerDto;
import com.company.customer.repository.CustomerRepository;
import com.company.customer.exception.CustomerValidationException;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.math.BigDecimal;

/**
 * Customer Service - Mapped from COBOL program CUST001
 * 
 * Source: CUST001.cbl
 * Last Updated: 2024-01-15
 */
@Service
public class CustomerService {
    
    private final CustomerRepository customerRepository;
    
    public CustomerService(CustomerRepository customerRepository) {
        this.customerRepository = customerRepository;
    }
    
    /**
     * Main processing method - maps to MAIN-PROCESSING paragraph
     */
    @Transactional
    public CustomerProcessingResult processCustomer(String customerId) {
        try {
            // Initialize (maps to INITIALIZE-PROCESSING)
            CustomerDto customer = initializeCustomer();
            
            // Read customer data (maps to READ-CUSTOMER-DATA)
            customer = readCustomerData(customerId);
            if (customer == null) {
                return CustomerProcessingResult.error("Customer not found");
            }
            
            // Validate customer (maps to VALIDATE-CUSTOMER)
            ValidationResult validation = validateCustomer(customer);
            if (!validation.isValid()) {
                return CustomerProcessingResult.error(validation.getErrorMessage());
            }
            
            // Calculate credit limit (maps to CALCULATE-CREDIT-LIMIT)
            BigDecimal creditLimit = calculateCreditLimit(customer);
            
            // Update customer (maps to UPDATE-CUSTOMER)
            customer.setCreditLimit(creditLimit);
            customerRepository.updateCustomer(customer);
            
            // Write result (maps to WRITE-RESULT)
            return CustomerProcessingResult.success(customer, creditLimit);
            
        } catch (Exception e) {
            // Error handling (maps to ERROR-HANDLING)
            return handleError(e);
        }
    }
    
    /**
     * Initialize customer - maps to INITIALIZE-PROGRAM paragraph
     */
    private CustomerDto initializeCustomer() {
        CustomerDto customer = new CustomerDto();
        customer.setStatus("V"); // Valid status
        return customer;
    }
    
    /**
     * Read customer data - maps to READ-CUSTOMER-DATA paragraph
     */
    private CustomerDto readCustomerData(String customerId) {
        return customerRepository.findByCustomerId(customerId)
            .orElse(null);
    }
    
    /**
     * Validate customer - maps to VALIDATE-CUSTOMER paragraph
     */
    private ValidationResult validateCustomer(CustomerDto customer) {
        ValidationResult result = new ValidationResult();
        
        // Age validation (maps to IF WS-CUSTOMER-AGE < 18)
        if (customer.getCustomerAge() < 18) {
            result.addError("Customer must be 18 or older");
        }
        
        // Balance validation (maps to IF WS-CUSTOMER-BALANCE < 0)
        if (customer.getCustomerBalance().compareTo(BigDecimal.ZERO) < 0) {
            result.addError("Customer balance cannot be negative");
        }
        
        return result;
    }
    
    /**
     * Calculate credit limit - maps to CALCULATE-CREDIT-LIMIT paragraph
     */
    private BigDecimal calculateCreditLimit(CustomerDto customer) {
        // COMPUTE WS-CREDIT-LIMIT = WS-CUSTOMER-BALANCE * 1.5
        BigDecimal creditLimit = customer.getCustomerBalance()
            .multiply(new BigDecimal("1.5"));
        
        // IF WS-CREDIT-LIMIT > 100000
        BigDecimal maxCreditLimit = new BigDecimal("100000");
        if (creditLimit.compareTo(maxCreditLimit) > 0) {
            creditLimit = maxCreditLimit;
        }
        
        return creditLimit;
    }
    
    /**
     * Error handling - maps to ERROR-HANDLING paragraph
     */
    private CustomerProcessingResult handleError(Exception e) {
        String errorMessage = "Error: " + e.getMessage();
        // Log error
        return CustomerProcessingResult.error(errorMessage);
    }
}

// Result class
class CustomerProcessingResult {
    private boolean success;
    private CustomerDto customer;
    private BigDecimal creditLimit;
    private String errorMessage;
    
    public static CustomerProcessingResult success(CustomerDto customer, BigDecimal creditLimit) {
        CustomerProcessingResult result = new CustomerProcessingResult();
        result.success = true;
        result.customer = customer;
        result.creditLimit = creditLimit;
        return result;
    }
    
    public static CustomerProcessingResult error(String errorMessage) {
        CustomerProcessingResult result = new CustomerProcessingResult();
        result.success = false;
        result.errorMessage = errorMessage;
        return result;
    }
    
    // Getters
    // ...
}

// Validation result class
class ValidationResult {
    private boolean valid = true;
    private String errorMessage;
    
    public void addError(String error) {
        this.valid = false;
        this.errorMessage = error;
    }
    
    public boolean isValid() {
        return valid;
    }
    
    public String getErrorMessage() {
        return errorMessage;
    }
}
```

## Mapping Patterns

### Paragraph → Method Mapping

**Pattern:**
- COBOL paragraph → Java method
- COBOL section → Java service class
- COBOL data → Java DTO/Entity
- COBOL file operations → Java repository

### Control Flow Mapping

**COBOL IF → Java if:**
```cobol
IF WS-STATUS = 'V'
    PERFORM CALCULATE-CREDIT-LIMIT
ELSE
    PERFORM ERROR-HANDLING
END-IF
```

```java
if ("V".equals(status)) {
    calculateCreditLimit(customer);
} else {
    handleError();
}
```

**COBOL PERFORM → Java method call:**
```cobol
PERFORM VALIDATE-CUSTOMER
```

```java
validateCustomer(customer);
```

### Data Access Mapping

**COBOL File Read → Java Repository:**
```cobol
READ CUSTOMER-FILE
    AT END
        MOVE 'E' TO WS-STATUS
    NOT AT END
        MOVE CUSTOMER-ID TO WS-CUSTOMER-ID
END-READ
```

```java
Optional<CustomerDto> customer = customerRepository.findByCustomerId(customerId);
if (customer.isEmpty()) {
    return CustomerProcessingResult.error("Customer not found");
}
```

**COBOL SQL → Java Repository:**
```cobol
EXEC SQL
    UPDATE CUSTOMER
    SET CREDIT-LIMIT = :WS-CREDIT-LIMIT
    WHERE CUSTOMER-ID = :WS-CUSTOMER-ID
END-EXEC
```

```java
@Transactional
public void updateCustomer(CustomerDto customer) {
    customerRepository.updateCreditLimit(
        customer.getCustomerId(), 
        customer.getCreditLimit()
    );
}
```

## Key Transformation Principles

### 1. Procedural → Object-Oriented

- COBOL paragraphs → Java methods
- COBOL data → Java objects
- COBOL files → Java repositories

### 2. Error Handling

- COBOL status codes → Java exceptions
- COBOL error messages → Java exception messages
- COBOL error handling → Java try-catch

### 3. Transaction Management

- COBOL SQL COMMIT/ROLLBACK → Java @Transactional
- COBOL file locking → Java transaction boundaries

## Related Documents

- [Implementation Guidelines](../docs/07-implementation-guidelines.md)
- [COBOL Application Analysis](../docs/03-cobol-application-analysis.md)

---

**Note:** This is a conceptual example for educational purposes. Production implementations should include proper error handling, security, compliance, and performance optimizations specific to your environment.

**Last Updated:** 2024  
**Maintained by:** Icarus Nova Architecture Team
