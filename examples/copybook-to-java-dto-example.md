# Copybook to Java DTO Example

> **Icarus Nova** | Example mapping from COBOL copybook to Java DTO (conceptual, NOT production code).

## Overview

This document shows how to map a COBOL copybook to a Java DTO, including data type mapping, validation, and transformation logic. This is a **conceptual example** for educational purposes.

## COBOL Copybook

### Original COBOL Copybook

```cobol
       COPY CUSTOMER-COPYBOOK.

       *> CUSTOMER-COPYBOOK.cpy
       01 CUSTOMER-RECORD.
           05 CUSTOMER-ID            PIC X(10).
           05 CUSTOMER-NAME          PIC X(50).
           05 CUSTOMER-ADDRESS.
               10 STREET             PIC X(50).
               10 CITY               PIC X(30).
               10 STATE              PIC X(2).
               10 ZIP-CODE          PIC X(10).
           05 CUSTOMER-AGE           PIC 9(3).
           05 CUSTOMER-BALANCE       PIC S9(10)V9(2) COMP-3.
           05 CUSTOMER-STATUS        PIC X(1).
               88 ACTIVE             VALUE 'A'.
               88 INACTIVE           VALUE 'I'.
               88 SUSPENDED          VALUE 'S'.
           05 CUSTOMER-CREDIT-SCORE   PIC 9(3).
           05 CUSTOMER-OPEN-DATE     PIC 9(8). *> YYYYMMDD format
           05 FILLER                 PIC X(20). *> Reserved for future use
```

## Java DTO Mapping

### Java DTO

```java
package com.company.customer.dto;

import java.math.BigDecimal;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;

/**
 * Customer DTO mapped from COBOL copybook CUSTOMER-RECORD
 * 
 * Source: CUSTOMER-COPYBOOK.cpy
 * Last Updated: 2024-01-15
 */
public class CustomerDto {
    
    // PIC X(10) → String (fixed length 10)
    private String customerId;
    
    // PIC X(50) → String (fixed length 50)
    private String customerName;
    
    // Nested structure → Nested object
    private CustomerAddress address;
    
    // PIC 9(3) → Integer (0-999)
    private Integer customerAge;
    
    // PIC S9(10)V9(2) COMP-3 → BigDecimal (packed decimal)
    private BigDecimal customerBalance;
    
    // PIC X(1) → String with enum-like methods
    private String customerStatus; // 'A', 'I', or 'S'
    
    // PIC 9(3) → Integer (0-999)
    private Integer customerCreditScore;
    
    // PIC 9(8) YYYYMMDD → LocalDate
    private LocalDate customerOpenDate;
    
    // Getters and Setters
    public String getCustomerId() {
        return customerId;
    }
    
    public void setCustomerId(String customerId) {
        // Validation: must be exactly 10 characters
        if (customerId != null && customerId.length() != 10) {
            throw new IllegalArgumentException("Customer ID must be exactly 10 characters");
        }
        this.customerId = customerId;
    }
    
    // Status helper methods (replacing COBOL 88 levels)
    public boolean isActive() {
        return "A".equals(customerStatus);
    }
    
    public boolean isInactive() {
        return "I".equals(customerStatus);
    }
    
    public boolean isSuspended() {
        return "S".equals(customerStatus);
    }
    
    // Date conversion helper
    public void setCustomerOpenDateFromString(String dateStr) {
        // Convert YYYYMMDD to LocalDate
        if (dateStr != null && dateStr.length() == 8) {
            DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyyMMdd");
            this.customerOpenDate = LocalDate.parse(dateStr, formatter);
        }
    }
    
    public String getCustomerOpenDateAsString() {
        // Convert LocalDate to YYYYMMDD
        if (customerOpenDate != null) {
            DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyyMMdd");
            return customerOpenDate.format(formatter);
        }
        return null;
    }
}

// Nested address structure
class CustomerAddress {
    private String street;      // PIC X(50)
    private String city;         // PIC X(30)
    private String state;        // PIC X(2)
    private String zipCode;      // PIC X(10)
    
    // Getters and setters
    // ...
}
```

## Transformation Logic

### COBOL to Java Transformation

**Transformation Service:**

```java
@Service
public class CustomerTransformationService {
    
    /**
     * Transform COBOL record to Java DTO
     * Handles: EBCDIC encoding, packed decimal, date formats
     */
    public CustomerDto transformFromCobol(byte[] cobolRecord) {
        CustomerDto dto = new CustomerDto();
        
        // Extract fields from COBOL record
        // Handle EBCDIC to UTF-8 conversion
        String customerId = convertEbcdicToString(cobolRecord, 0, 10);
        dto.setCustomerId(customerId);
        
        String customerName = convertEbcdicToString(cobolRecord, 10, 50);
        dto.setCustomerName(customerName);
        
        // Handle packed decimal (COMP-3)
        BigDecimal balance = convertPackedDecimal(cobolRecord, 100, 6); // 6 bytes for S9(10)V9(2)
        dto.setCustomerBalance(balance);
        
        // Handle date conversion (YYYYMMDD)
        String dateStr = convertEbcdicToString(cobolRecord, 106, 8);
        dto.setCustomerOpenDateFromString(dateStr);
        
        return dto;
    }
    
    /**
     * Transform Java DTO to COBOL record
     * Handles: UTF-8 to EBCDIC, decimal to packed, date formats
     */
    public byte[] transformToCobol(CustomerDto dto) {
        byte[] record = new byte[200]; // Total record length
        
        // Convert and write fields
        writeStringToEbcdic(record, 0, dto.getCustomerId(), 10);
        writeStringToEbcdic(record, 10, dto.getCustomerName(), 50);
        
        // Convert BigDecimal to packed decimal
        writePackedDecimal(record, 100, dto.getCustomerBalance(), 6);
        
        // Convert LocalDate to YYYYMMDD
        String dateStr = dto.getCustomerOpenDateAsString();
        writeStringToEbcdic(record, 106, dateStr, 8);
        
        return record;
    }
    
    private String convertEbcdicToString(byte[] data, int offset, int length) {
        // EBCDIC to UTF-8 conversion logic
        // Implementation depends on EBCDIC library used
        return "..."; // Simplified
    }
    
    private BigDecimal convertPackedDecimal(byte[] data, int offset, int length) {
        // COMP-3 (packed decimal) to BigDecimal conversion
        // Implementation depends on packed decimal library
        return BigDecimal.ZERO; // Simplified
    }
}
```

## Validation

### Validation Rules

**Java Validation:**

```java
public class CustomerValidationService {
    
    public ValidationResult validate(CustomerDto dto) {
        ValidationResult result = new ValidationResult();
        
        // Customer ID validation (PIC X(10))
        if (dto.getCustomerId() == null || dto.getCustomerId().length() != 10) {
            result.addError("Customer ID must be exactly 10 characters");
        }
        
        // Age validation (PIC 9(3))
        if (dto.getCustomerAge() != null && (dto.getCustomerAge() < 0 || dto.getCustomerAge() > 999)) {
            result.addError("Customer age must be between 0 and 999");
        }
        
        // Balance validation (PIC S9(10)V9(2))
        if (dto.getCustomerBalance() != null) {
            BigDecimal maxBalance = new BigDecimal("9999999999.99");
            BigDecimal minBalance = new BigDecimal("-9999999999.99");
            if (dto.getCustomerBalance().compareTo(maxBalance) > 0 ||
                dto.getCustomerBalance().compareTo(minBalance) < 0) {
                result.addError("Customer balance out of range");
            }
        }
        
        // Status validation (PIC X(1) with 88 levels)
        if (dto.getCustomerStatus() != null && 
            !"A".equals(dto.getCustomerStatus()) &&
            !"I".equals(dto.getCustomerStatus()) &&
            !"S".equals(dto.getCustomerStatus())) {
            result.addError("Customer status must be A, I, or S");
        }
        
        return result;
    }
}
```

## Key Mapping Rules

### Data Type Mapping

| COBOL Type | Java Type | Conversion Notes |
|------------|-----------|------------------|
| PIC X(n) | String | Fixed length, pad/trim, EBCDIC→UTF-8 |
| PIC 9(n) | Integer/BigInteger | Based on size, unsigned |
| PIC S9(n)V9(m) | BigDecimal | Signed decimal, precision |
| COMP-3 | BigDecimal | Packed decimal conversion |
| PIC 9(8) YYYYMMDD | LocalDate | Date format conversion |
| 88 levels | boolean methods | Enum-like behavior |

### Special Considerations

**EBCDIC Encoding:**
- COBOL on mainframe uses EBCDIC
- Java uses UTF-8
- Conversion required for text fields

**Packed Decimal (COMP-3):**
- COBOL packed decimal format
- Java BigDecimal
- Special conversion library needed

**Date Formats:**
- COBOL: Various formats (YYYYMMDD, Julian, etc.)
- Java: LocalDate
- Conversion logic required

**Fixed-Length Fields:**
- COBOL: Fixed length with padding
- Java: Variable length strings
- Padding/trimming required

## Related Documents

- [Data and Copybook Mapping](../docs/04-data-and-copybook-mapping.md)
- [ADR-0002: Canonical Data Model](../adr/0002-canonical-data-model.md)

---

**Note:** This is a conceptual example for educational purposes. Production implementations should include proper error handling, security, compliance, and performance optimizations specific to your environment.

**Last Updated:** 2024  
**Maintained by:** Icarus Nova Architecture Team
