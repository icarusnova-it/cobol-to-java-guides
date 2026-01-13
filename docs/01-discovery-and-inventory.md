# Phase 1: Discovery and Inventory

> **Icarus Nova** | Complete technical inventory and classification for COBOL migration.

## Overview

The Discovery and Inventory phase is the foundation of a successful migration. This phase ensures we know **what exists, what runs, who uses it, and what breaks if we touch it** before making any migration decisions.

## Objectives

1. **Complete Technical Inventory**: Catalog all COBOL components
2. **Operational Understanding**: Understand how systems run in production
3. **Dependency Mapping**: Identify dependencies and relationships
4. **Classification**: Classify components by criticality, complexity, and risk
5. **Prioritization**: Create migration waves based on risk and dependencies

## Step 1.1: Technical Inventory

### Inventory Scope

**COBOL Programs:**
- Source code files (.cbl, .cob)
- Copybooks (.cpy)
- JCL procedures
- Scripts (shell, REXX, etc.)
- Macros and utilities

**Batch Jobs:**
- Job definitions (JCL)
- Scheduling (Control-M, OPC, JCL scheduling)
- Job dependencies
- Execution windows
- SLAs

**Dependencies:**
- Libraries and runtime (LE, CICS, etc.)
- Utilities (SORT, IDCAMS, etc.)
- External systems
- Databases (DB2, IMS, VSAM, etc.)

**Interfaces:**
- CICS transactions (online)
- Batch file interfaces
- Message queues
- Database interfaces
- API endpoints

**Environments:**
- Development
- UAT/Testing
- Production
- Batch windows
- SLAs

### Inventory Template

**Application Inventory Structure:**

| Field | Description | Example |
|-------|-------------|---------|
| Application ID | Unique identifier | APP-001 |
| Application Name | Business name | Customer Management |
| Program Count | Number of COBOL programs | 45 |
| Copybook Count | Number of copybooks | 23 |
| Batch Job Count | Number of batch jobs | 12 |
| Online Transaction Count | CICS transactions | 8 |
| Criticality | Tier 0/1/2 | Tier 1 |
| Owner | Business owner | Credit Department |
| Last Modified | Last change date | 2023-06-15 |

**Program Inventory Structure:**

| Field | Description | Example |
|-------|-------------|---------|
| Program Name | COBOL program name | CUST001 |
| Program Type | Batch/Online/Utility | Batch |
| Lines of Code | Approximate LOC | 2,450 |
| Copybooks Used | List of copybooks | CUSTCOPY, DATECOPY |
| Files Accessed | Files used | CUSTOMER-FILE, ORDER-FILE |
| SQL Statements | Database access | SELECT, UPDATE |
| CALL Statements | External calls | CALL 'UTIL001' |
| Complexity | Low/Medium/High | Medium |
| Last Modified | Last change date | 2023-05-20 |

### Deliverables

**1. Application Inventory.xlsx (or Markdown)**

**Structure:**
- Applications list with metadata
- Programs per application
- Dependencies per application
- Interfaces per application

**2. Batch Job Catalog**

**Structure:**
```
Job Name: JOB001
  Step 1: STEP001 → Program: PROG001
  Step 2: STEP002 → Program: PROG002
  Step 3: STEP003 → Utility: SORT
Dependencies: JOB002 must complete first
Window: 02:00 - 04:00
SLA: Complete by 04:00
```

**3. Interface Catalog**

**Structure:**
- Interface ID
- Interface Type (File/Queue/API/DB)
- Source System
- Target System
- Format (Fixed/Variable/XML/JSON)
- Frequency
- Owner
- Volume

## Step 1.2: Classification and Prioritization

### Classification Criteria

**Criticality:**
- **Tier 0**: Mission-critical, cannot fail
- **Tier 1**: High importance, significant impact if fails
- **Tier 2**: Important, moderate impact if fails
- **Tier 3**: Low importance, minimal impact if fails

**Type:**
- **Batch**: Scheduled batch jobs
- **Online**: CICS transactions, real-time
- **Utility**: Supporting programs, shared code
- **Interface**: Integration points

**Complexity:**
- **Low**: Simple programs, few dependencies
- **Medium**: Moderate complexity, some dependencies
- **High**: Complex logic, many dependencies, intricate flows

**Risk:**
- **Low**: Well-understood, stable, low change frequency
- **Medium**: Some unknowns, moderate change frequency
- **High**: Poorly documented, high change frequency, critical

**Data Impact:**
- **Low**: No data modification, read-only
- **Medium**: Data modification with controls
- **High**: Critical data modification, financial impact

### Prioritization Matrix

**Migration Waves:**

**Wave 1: Low-Risk, High-Value**
- Low complexity
- Low risk
- High business value
- Few dependencies
- Good documentation

**Wave 2: Medium-Risk, Medium-Value**
- Medium complexity
- Medium risk
- Medium business value
- Some dependencies
- Moderate documentation

**Wave 3: High-Risk, Critical**
- High complexity
- High risk
- Critical business value
- Many dependencies
- Poor documentation

**Wave 4: Utilities and Shared Code**
- Utility programs
- Shared copybooks
- Common libraries
- Reusable components

### Deliverables

**1. Prioritization Matrix**

**Structure:**
| Component | Criticality | Complexity | Risk | Data Impact | Wave | Priority |
|-----------|-------------|------------|------|-------------|------|----------|
| PROG001 | Tier 1 | Medium | Low | Medium | Wave 1 | High |
| PROG002 | Tier 0 | High | High | High | Wave 3 | Critical |

**2. Migration Waves Plan**

**Structure:**
- Wave definition
- Components in wave
- Dependencies
- Exit criteria
- Timeline

## Tools and Techniques

### Inventory Tools

**Static Analysis:**
- COBOL parsers
- Dependency analyzers
- Code metrics tools

**Operational Analysis:**
- Job scheduler analysis
- Execution logs
- Performance data
- Error logs

**Documentation:**
- Existing documentation review
- Interviews with operators
- Business user interviews
- Technical team interviews

## Best Practices

### Inventory Best Practices

1. **Be Thorough**: Don't skip components
2. **Version Control**: Track all inventory artifacts
3. **Validate**: Verify inventory with operations team
4. **Document Assumptions**: Document any assumptions made
5. **Regular Updates**: Keep inventory current

### Classification Best Practices

1. **Business Input**: Get business input on criticality
2. **Technical Assessment**: Technical team assesses complexity
3. **Risk Assessment**: Combined business and technical risk
4. **Review**: Regular review of classifications
5. **Documentation**: Document classification rationale

## Common Challenges

### Challenge: Incomplete Documentation

**Solution:**
- Interview operations team
- Analyze execution logs
- Review job schedules
- Examine error logs

### Challenge: Hidden Dependencies

**Solution:**
- Call graph analysis
- File dependency analysis
- Database dependency analysis
- Runtime analysis

### Challenge: Unclear Ownership

**Solution:**
- Business stakeholder interviews
- Organizational chart review
- Change request analysis
- Support ticket analysis

## Related Documents

- [Business Process Mapping](./02-business-process-mapping.md)
- [COBOL Application Analysis](./03-cobol-application-analysis.md)
- [Migration Strategies](./06-migration-strategies.md)

---

**Last Updated:** 2024  
**Maintained by:** Icarus Nova Architecture Team  
**Version:** 1.0
