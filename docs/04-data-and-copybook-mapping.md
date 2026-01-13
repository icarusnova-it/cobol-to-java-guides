# Phase 4: Data and Copybook Mapping

> **Icarus Nova** | Translate COBOL data structures to Java without losing semantics.

## Overview

Data and Copybook Mapping is **critical for migration success**. This phase translates COBOL data structures (copybooks, file definitions) into Java equivalents while preserving semantics, handling data types correctly, and planning data migration strategies.

## Objectives

1. **Copybook Analysis**: Understand COBOL data structures
2. **Data Type Mapping**: Map COBOL types to Java types
3. **Canonical Model**: Create canonical data model
4. **Persistence Strategy**: Plan data storage approach
5. **Migration Strategy**: Plan data migration

## Step 4.1: Copybook to Canonical Model

### COBOL Data Types

**PIC Clauses:**
- **PIC X(n)**: Alphanumeric (string)
- **PIC 9(n)**: Numeric (integer)
- **PIC 9(n)V9(m)**: Decimal (fixed-point)
- **PIC S9(n)**: Signed numeric
- **PIC S9(n)V9(m)**: Signed decimal
- **PIC 9(n) COMP-3**: Packed decimal
- **PIC 9(n) COMP**: Binary

**Special Types:**
- **REDEFINES**: Multiple views of same storage
- **OCCURS**: Arrays/tables
- **OCCURS DEPENDING ON**: Variable-length arrays
- **GROUP ITEMS**: Nested structures

### Data Type Mapping

**COBOL → Java Mapping:**

| COBOL Type | Java Type | Notes |
|------------|-----------|-------|
| PIC X(n) | String | Fixed length, pad/trim |
| PIC 9(n) | BigInteger/Long | Based on size |
| PIC 9(n)V9(m) | BigDecimal | Fixed decimal precision |
| PIC S9(n) | BigInteger/Long | Signed |
| PIC S9(n)V9(m) | BigDecimal | Signed decimal |
| COMP-3 | BigDecimal | Packed decimal conversion |
| COMP | Long/Integer | Binary, endianness matters |
| REDEFINES | Union type | Multiple views |
| OCCURS | Array/List | Fixed/variable length |

### Copybook Analysis

**Copybook Structure:**

```cobol
01 CUSTOMER-RECORD.
   05 CUSTOMER-ID        PIC X(10).
   05 CUSTOMER-NAME      PIC X(50).
   05 CUSTOMER-AGE       PIC 9(3).
   05 CUSTOMER-BALANCE   PIC S9(10)V9(2) COMP-3.
   05 CUSTOMER-STATUS    PIC X(1).
      88 ACTIVE          VALUE 'A'.
      88 INACTIVE        VALUE 'I'.
```

**Java Equivalent:**

```java
public class CustomerRecord {
    private String customerId;        // PIC X(10)
    private String customerName;      // PIC X(50)
    private Integer customerAge;      // PIC 9(3)
    private BigDecimal customerBalance; // PIC S9(10)V9(2) COMP-3
    private String customerStatus;    // PIC X(1)
    
    public boolean isActive() {
        return "A".equals(customerStatus);
    }
    
    public boolean isInactive() {
        return "I".equals(customerStatus);
    }
}
```

### Special Considerations

**Endianness:**
- COBOL on mainframe: Big-endian
- Java: Platform-dependent (usually big-endian)
- Binary data: May need conversion

**Encoding:**
- COBOL: EBCDIC (mainframe)
- Java: UTF-8 (typically)
- Conversion required for text data

**Dates:**
- COBOL: Various formats (YYYYMMDD, Julian, etc.)
- Java: LocalDate, LocalDateTime
- Conversion logic required

**Packed Decimal (COMP-3):**
- COBOL: Packed decimal format
- Java: BigDecimal
- Conversion library required

**REDEFINES:**
- COBOL: Multiple views of same storage
- Java: Union types or separate fields
- Careful mapping required

### Canonical Data Model

**Purpose:**
- Common data model across systems
- Independent of source/target format
- Enables transformation
- Supports multiple formats

**Structure:**
```
Canonical Model (Java)
    ↓
Transformation Layer
    ↓
COBOL Format / Java Format / API Format
```

## Step 4.2: Persistence Strategy

### Data Migration Approaches

**Big Bang Migration:**
- Migrate all data at once
- High risk
- Not recommended

**Incremental Migration:**
- Migrate data in phases
- Lower risk
- Recommended

**Dual-Write:**
- Write to both systems
- Maintain consistency
- Gradual migration

**Replication:**
- Replicate data to new system
- Read from new, write to old
- Gradual cutover

### Data Consistency

**Consistency Strategies:**
- **Event-Driven**: CDC (Change Data Capture)
- **Batch Sync**: Periodic synchronization
- **Dual-Write**: Write to both systems
- **Read-Through**: Read from old, write to new

### Data Retention

**Retention Strategy:**
- Keep legacy data accessible
- Archive old data
- Maintain audit trail
- Support rollback

## Deliverables

### 1. Data Dictionary

**Structure:**

| Field Name | COBOL Type | Java Type | Description | Validation | Transformation |
|------------|------------|-----------|-------------|------------|----------------|
| CUSTOMER-ID | PIC X(10) | String | Customer identifier | Not null, 10 chars | Trim, pad |
| CUSTOMER-NAME | PIC X(50) | String | Customer name | Not null, max 50 | Trim |
| CUSTOMER-AGE | PIC 9(3) | Integer | Customer age | 0-999 | None |
| CUSTOMER-BALANCE | COMP-3 | BigDecimal | Account balance | Not null | Packed decimal conversion |

### 2. Copybook Mapping

**Structure:**
- Copybook name
- COBOL structure
- Java equivalent
- Mapping rules
- Validation rules
- Transformation logic

### 3. Data Model Overview

**Content:**
- Entity relationships
- Data flow diagrams
- Migration strategy
- Consistency approach

### 4. Migration Strategy

**Content:**
- Migration approach
- Phases
- Data validation
- Rollback plan

## Examples

See [`/examples/copybook-to-java-dto-example.md`](../examples/copybook-to-java-dto-example.md) for detailed examples.

## Best Practices

### Mapping Best Practices

1. **Preserve Semantics**: Don't lose business meaning
2. **Handle Edge Cases**: Consider all data scenarios
3. **Validate Mappings**: Test mappings thoroughly
4. **Document Transformations**: Document all conversions
5. **Version Control**: Track mapping changes

### Data Migration Best Practices

1. **Incremental Approach**: Migrate in phases
2. **Validate Data**: Validate before and after migration
3. **Maintain Consistency**: Ensure data consistency
4. **Test Thoroughly**: Test migration process
5. **Plan Rollback**: Have rollback plan ready

## Related Documents

- [Copybook to Java DTO Example](../examples/copybook-to-java-dto-example.md)
- [Data Flow Diagram](../diagrams/data-flow.md)
- [Target Architecture Java](./05-target-architecture-java.md)

---

**Last Updated:** 2024  
**Maintained by:** Icarus Nova Architecture Team  
**Version:** 1.0
