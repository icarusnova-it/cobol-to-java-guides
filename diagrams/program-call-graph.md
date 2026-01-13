# Program Call Graph

> **Icarus Nova** | Call graph diagram showing COBOL program relationships.

## Overview

This diagram illustrates the call relationships between COBOL programs, showing entry points, subprograms, and shared utilities.

## Call Graph Diagram

```mermaid
graph TD
    subgraph "Entry Points"
        JOB1[Batch Job: JOB001]
        CICS1[CICS Transaction: CICS001]
        JOB2[Batch Job: JOB002]
    end
    
    subgraph "Main Programs"
        MAIN1[MAIN001<br/>Customer Processing]
        MAIN2[MAIN002<br/>Order Processing]
        MAIN3[MAIN003<br/>Report Generation]
    end
    
    subgraph "Subprograms"
        SUB1[SUB001<br/>Validation]
        SUB2[SUB002<br/>Calculation]
        SUB3[SUB003<br/>Data Access]
    end
    
    subgraph "Shared Utilities"
        UTIL1[UTIL001<br/>Date Utilities]
        UTIL2[UTIL002<br/>Format Utilities]
        UTIL3[UTIL003<br/>Error Handling]
    end
    
    JOB1 --> MAIN1
    CICS1 --> MAIN1
    JOB2 --> MAIN2
    
    MAIN1 --> SUB1
    MAIN1 --> SUB2
    MAIN1 --> UTIL1
    MAIN1 --> UTIL3
    
    MAIN2 --> SUB2
    MAIN2 --> SUB3
    MAIN2 --> UTIL2
    MAIN2 --> UTIL3
    
    MAIN3 --> SUB3
    MAIN3 --> UTIL1
    
    SUB1 --> UTIL3
    SUB2 --> UTIL1
    SUB3 --> UTIL2
    
    style MAIN1 fill:#90EE90
    style MAIN2 fill:#90EE90
    style MAIN3 fill:#90EE90
    style UTIL1 fill:#FFE4B5
    style UTIL2 fill:#FFE4B5
    style UTIL3 fill:#FFE4B5
```

## Call Graph Analysis

### Entry Points

**Batch Jobs:**
- JOB001: Customer processing batch job
- JOB002: Order processing batch job

**Online Transactions:**
- CICS001: Customer inquiry transaction

### Main Programs

**MAIN001: Customer Processing**
- Called by: JOB001, CICS001
- Calls: SUB001, SUB002, UTIL001, UTIL003
- Purpose: Main customer processing logic

**MAIN002: Order Processing**
- Called by: JOB002
- Calls: SUB002, SUB003, UTIL002, UTIL003
- Purpose: Main order processing logic

**MAIN003: Report Generation**
- Called by: JOB003
- Calls: SUB003, UTIL001
- Purpose: Report generation

### Subprograms

**SUB001: Validation**
- Called by: MAIN001
- Calls: UTIL003
- Purpose: Data validation

**SUB002: Calculation**
- Called by: MAIN001, MAIN002
- Calls: UTIL001
- Purpose: Business calculations

**SUB003: Data Access**
- Called by: MAIN002, MAIN003
- Calls: UTIL002
- Purpose: Database access

### Shared Utilities

**UTIL001: Date Utilities**
- Called by: MAIN001, MAIN003, SUB002
- Purpose: Date manipulation

**UTIL002: Format Utilities**
- Called by: MAIN002, SUB003
- Purpose: Data formatting

**UTIL003: Error Handling**
- Called by: MAIN001, MAIN002, SUB001
- Purpose: Error handling

## Migration Implications

### Migration Order

**Phase 1: Utilities**
- Migrate shared utilities first
- Foundation for other programs
- Low risk, high reuse

**Phase 2: Subprograms**
- Migrate subprograms
- Used by main programs
- Medium risk

**Phase 3: Main Programs**
- Migrate main programs
- Depend on utilities and subprograms
- Higher risk

### Dependency Management

**Critical Dependencies:**
- UTIL003 (Error Handling) used by many programs
- Must migrate early
- High impact if changes

**Shared Components:**
- Utilities shared across programs
- Changes affect multiple programs
- Careful versioning needed

## Related Documents

- [COBOL Application Analysis](../docs/03-cobol-application-analysis.md)
- [Migration Strategies](../docs/06-migration-strategies.md)

---

**Last Updated:** 2024  
**Maintained by:** Icarus Nova Architecture Team  
**Version:** 1.0
