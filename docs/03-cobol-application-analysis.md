# Phase 3: COBOL Application Analysis

> **Icarus Nova** | Deep analysis of COBOL code to create an understandable model.

## Overview

COBOL Application Analysis converts legacy code into an understandable model. This phase focuses on understanding program structure, call relationships, data flows, and business rules embedded in COBOL code.

## Objectives

1. **Call Graph Analysis**: Understand program relationships
2. **Program Structure**: Document program organization
3. **Business Rule Extraction**: Extract business rules from code
4. **Data Flow Analysis**: Understand data movement
5. **Complexity Assessment**: Assess migration complexity

## Step 3.1: Call Graph Analysis

### Call Graph Purpose

**Understand Program Relationships:**
- Which programs call which programs
- Entry points vs. subprograms
- Shared utility programs
- Dependency chains

### Call Graph Elements

**Program Types:**
- **Entry Point Programs**: Called by external systems or batch jobs
- **Subprograms**: Called by other COBOL programs
- **Utility Programs**: Shared across multiple systems
- **Library Programs**: Reusable components

**Call Mechanisms:**
- **CALL Statement**: External program call
- **PERFORM**: Internal procedure call
- **GO TO**: Control flow (legacy, avoid in new code)
- **COPY**: Copybook inclusion

### Call Graph Documentation

**Deliverable: [`/diagrams/program-call-graph.md`](../diagrams/program-call-graph.md)**

**Structure:**
```
Program: MAIN001
  Calls:
    - SUB001 (subprogram)
    - UTIL001 (utility)
    - SUB002 (subprogram)
  Called By:
    - JOB001 (batch job)
    - CICS transaction CICS001
```

### Call Graph Analysis Process

**Steps:**
1. Identify all programs
2. Extract CALL statements
3. Extract PERFORM statements (for internal structure)
4. Build call graph
5. Identify entry points
6. Identify shared utilities
7. Document dependencies

## Step 3.2: Program Structure Analysis

### COBOL Program Structure

**Standard Sections:**
- **IDENTIFICATION DIVISION**: Program identification
- **ENVIRONMENT DIVISION**: Environment configuration
- **DATA DIVISION**: Data definitions
- **PROCEDURE DIVISION**: Program logic

### Program Analysis Template

**Program Analysis Sheet:**

| Field | Description | Example |
|-------|-------------|---------|
| Program Name | COBOL program name | CUST001 |
| Program Type | Batch/Online/Utility | Batch |
| Lines of Code | Approximate LOC | 2,450 |
| Sections | Number of sections | 5 |
| Paragraphs | Number of paragraphs | 12 |
| Copybooks | Copybooks used | CUSTCOPY, DATECOPY |
| Files | Files accessed | CUSTOMER-FILE, ORDER-FILE |
| SQL | SQL statements | SELECT, UPDATE, INSERT |
| CALLs | External calls | CALL 'UTIL001' |
| Complexity | Low/Medium/High | Medium |

### Copybook Analysis

**Copybook Usage:**
- Data structures
- Common definitions
- Constants
- File definitions

**Copybook Dependencies:**
- Which copybooks are used
- Copybook hierarchy
- Shared copybooks
- Copybook versions

### File Analysis

**File Types:**
- **Sequential Files**: Sequential access
- **VSAM Files**: Indexed/relative/sequential
- **Database**: DB2, IMS
- **Temporary Files**: Work files

**File Operations:**
- OPEN/CLOSE
- READ/WRITE
- REWRITE/DELETE
- START/READ NEXT (VSAM)

### SQL Analysis

**SQL Statements:**
- SELECT (queries)
- INSERT (additions)
- UPDATE (modifications)
- DELETE (deletions)
- Cursors (row processing)

**SQL Patterns:**
- Embedded SQL
- Dynamic SQL
- Stored procedures
- Transaction management

## Step 3.3: Business Rule Extraction

### Rule Extraction Process

**Identify Rule Patterns:**

**Validation Rules:**
- IF statements with conditions
- Range checks
- Format validations
- Cross-field validations

**Calculation Rules:**
- COMPUTE statements
- Arithmetic operations
- Formula implementations
- Rate calculations

**State Transitions:**
- Status field updates
- State machine logic
- Workflow transitions
- Approval flows

**Error Handling:**
- Error detection
- Retry logic
- Abort conditions
- Recovery procedures

### Rule Documentation

**Rule Extraction Template:**

```
Rule ID: BR-001
Source: Program CUST001, Paragraph VALIDATE-CUSTOMER
Rule Type: Validation
Description: Customer age must be >= 18

COBOL Code:
  IF WS-CUSTOMER-AGE < 18
    MOVE 'INVALID' TO WS-STATUS
    PERFORM ERROR-HANDLING
  END-IF

Business Rule: Customer must be at least 18 years old
Impact: All customer registrations
Exceptions: None
```

### Business Rules Catalog

**Deliverable Structure:**

```markdown
# Business Rules Catalog

## Program: CUST001

### BR-001: Customer Age Validation
- **Location**: Paragraph VALIDATE-CUSTOMER
- **Rule**: Customer age must be >= 18
- **Type**: Validation
- **Impact**: Customer registration

### BR-002: Credit Limit Calculation
- **Location**: Paragraph CALCULATE-CREDIT-LIMIT
- **Rule**: Credit limit = Income * 0.3
- **Type**: Calculation
- **Impact**: Credit approval
```

## Data Flow Analysis

### Data Flow Elements

**Data Sources:**
- Input files
- Database tables
- User input (screens)
- External systems

**Data Transformations:**
- Data movement
- Data calculations
- Data validations
- Data aggregations

**Data Destinations:**
- Output files
- Database updates
- Reports
- External systems

### Data Flow Documentation

**Deliverable: [`/diagrams/data-flow.md`](../diagrams/data-flow.md)**

**Structure:**
```
Input: Customer File
  → Validate Customer Data
  → Calculate Credit Limit
  → Update Customer Record
  → Generate Report
Output: Updated Customer File, Credit Report
```

## Complexity Assessment

### Complexity Factors

**Code Complexity:**
- Lines of code
- Cyclomatic complexity
- Nesting depth
- Number of branches

**Dependency Complexity:**
- Number of dependencies
- Shared components
- External system dependencies
- Database dependencies

**Business Complexity:**
- Number of business rules
- Rule complexity
- State transitions
- Exception handling

### Complexity Scoring

**Scoring Model:**
- **Low**: Simple, well-documented, few dependencies
- **Medium**: Moderate complexity, some dependencies
- **High**: Complex logic, many dependencies, poor documentation

**Factors:**
- Code metrics
- Dependency count
- Business rule count
- Documentation quality

## Deliverables

### 1. Call Graph Diagram

**Format:** Mermaid diagram or graph visualization

**Content:**
- Program relationships
- Entry points
- Shared utilities
- Dependency chains

### 2. Program Analysis Sheets

**Format:** Spreadsheet or Markdown tables

**Content:**
- Program metadata
- Structure analysis
- Dependencies
- Complexity assessment

### 3. Business Rules Catalog

**Format:** Structured catalog

**Content:**
- Extracted business rules
- Rule locations
- Rule types
- Impact analysis

### 4. Data Flow Diagrams

**Format:** Mermaid diagrams

**Content:**
- Data sources
- Data transformations
- Data destinations
- Data flow paths

## Tools and Techniques

### Static Analysis Tools

**COBOL Parsers:**
- Extract program structure
- Identify CALL statements
- Analyze data definitions
- Extract SQL statements

**Dependency Analyzers:**
- Build call graphs
- Identify dependencies
- Map file usage
- Track copybook usage

### Manual Analysis

**Code Review:**
- Read and understand code
- Document business logic
- Identify patterns
- Extract rules

**Interviews:**
- Technical team
- Business analysts
- Operations team
- Support team

## Best Practices

### Analysis Best Practices

1. **Systematic Approach**: Follow structured process
2. **Document Everything**: Document all findings
3. **Validate Understanding**: Confirm with stakeholders
4. **Iterative Refinement**: Refine analysis based on feedback
5. **Version Control**: Track analysis artifacts

### Rule Extraction Best Practices

1. **Don't Guess**: Extract from code, don't infer
2. **Document Source**: Always document rule location
3. **Validate with Business**: Confirm rules with business
4. **Handle Exceptions**: Document known exceptions
5. **Maintain Catalog**: Keep rules catalog current

## Related Documents

- [Program Call Graph](../diagrams/program-call-graph.md)
- [Data Flow Diagram](../diagrams/data-flow.md)
- [Data and Copybook Mapping](./04-data-and-copybook-mapping.md)

---

**Last Updated:** 2024  
**Maintained by:** Icarus Nova Architecture Team  
**Version:** 1.0
