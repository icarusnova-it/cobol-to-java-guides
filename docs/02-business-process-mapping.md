# Phase 2: Business Process Mapping

> **Icarus Nova** | Map business processes and rules before writing Java code.

## Overview

Business Process Mapping ensures we understand **what the business does** before focusing on **how the code works**. This phase maps end-to-end business processes, documents business rules, and creates a business glossary that bridges COBOL terminology with business language.

## Objectives

1. **Process Understanding**: Map end-to-end business processes
2. **Rule Documentation**: Document business rules and logic
3. **Terminology Bridge**: Create glossary (COBOL → Business)
4. **Control Identification**: Identify controls and reconciliations
5. **Business Validation**: Validate understanding with business stakeholders

## Step 2.1: Map End-to-End Processes

### Process Mapping Approach

**For Each Business Domain:**

**Example Domains:**
- Credit/Lending
- Payments
- Portfolio Management
- Customer Management
- Reporting

**Process Elements:**

**Inputs:**
- Events that trigger the process
- Files received
- User interactions (screens)
- External system inputs
- Scheduled triggers

**Process Steps:**
- Main process flow
- Decision points
- Parallel activities
- Exception handling
- Validation steps

**Business Rules:**
- Validation rules
- Calculation rules
- State transition rules
- Authorization rules
- Business constraints

**Outputs:**
- Reports generated
- Files produced
- Database updates
- External system notifications
- User notifications

**Controls:**
- Control totals
- Reconciliation points
- Audit trails
- Exception handling
- Error reporting

### Process Documentation Format

**Process Narrative Template:**

```
Process: Customer Credit Approval

Trigger: Credit application submitted

Inputs:
  - Credit application form
  - Customer credit history
  - Income verification

Process Steps:
  1. Validate application data
  2. Check customer credit score
  3. Calculate debt-to-income ratio
  4. Apply credit policy rules
  5. Generate approval/rejection decision
  6. Update customer record
  7. Notify customer

Business Rules:
  - Credit score must be >= 650
  - Debt-to-income ratio must be <= 40%
  - Maximum credit limit based on income
  - Approval requires manager sign-off if > $50,000

Outputs:
  - Approval/rejection notification
  - Updated customer record
  - Credit report entry

Controls:
  - Total applications processed
  - Total approvals/rejections
  - Reconciliation with credit bureau
  - Audit log of all decisions
```

### BPMN Representation

**Simple BPMN Elements:**
- Start Event
- Tasks (Activities)
- Gateways (Decisions)
- End Events
- Sub-processes

**Example Flow:**
```
[Start] → [Validate] → [Decision: Valid?]
  Yes → [Process] → [Update] → [Notify] → [End]
  No → [Reject] → [Notify] → [End]
```

## Step 2.2: Business Rules Catalog

### Rule Categories

**Validation Rules:**
- Data format validation
- Range validation
- Business rule validation
- Cross-field validation

**Calculation Rules:**
- Interest calculations
- Fee calculations
- Commission calculations
- Tax calculations

**State Transition Rules:**
- Status changes
- Workflow transitions
- Approval flows
- State validations

**Authorization Rules:**
- Access control
- Permission checks
- Role-based rules
- Approval requirements

### Rule Documentation Template

**Rule Structure:**

| Field | Description | Example |
|-------|-------------|---------|
| Rule ID | Unique identifier | BR-001 |
| Rule Name | Descriptive name | Credit Score Validation |
| Business Domain | Domain area | Credit |
| Rule Type | Validation/Calculation/etc. | Validation |
| Description | Rule description | Credit score must be >= 650 |
| Source | Where rule is implemented | Program: CREDIT01, Paragraph: VALIDATE-SCORE |
| Impact | What breaks if rule changes | All credit approvals |
| Dependencies | Related rules | BR-002, BR-003 |
| Exceptions | Known exceptions | Manager override allowed |
| Last Verified | Last validation date | 2024-01-15 |

### Business Rules Catalog

**Deliverable Structure:**

```markdown
# Business Rules Catalog

## Domain: Credit

### BR-001: Credit Score Validation
- **Rule**: Credit score must be >= 650
- **Source**: Program CREDIT01, Paragraph VALIDATE-SCORE
- **Impact**: All credit applications
- **Exceptions**: Manager override for scores 600-649

### BR-002: Debt-to-Income Ratio
- **Rule**: Debt-to-income ratio must be <= 40%
- **Source**: Program CREDIT01, Paragraph CALCULATE-DTI
- **Impact**: Credit limit calculation
- **Dependencies**: BR-001
```

## Step 2.3: Business Glossary

### Glossary Purpose

**Bridge COBOL and Business:**
- Translate COBOL field names to business terms
- Document business meaning of technical terms
- Create common vocabulary
- Enable business-IT communication

### Glossary Template

**Structure:**

| COBOL Term | Business Term | Description | Example |
|------------|---------------|-------------|---------|
| WS-CUST-ID | Customer ID | Unique customer identifier | 12345 |
| WS-CREDIT-SCORE | Credit Score | FICO credit score | 720 |
| WS-DTI-RATIO | Debt-to-Income | Monthly debt / monthly income | 0.35 |
| FD-CUSTOMER-FILE | Customer Master | Master customer data file | CUSTOMER.MASTER |

## Step 2.4: Control Documentation

### Control Types

**Control Totals:**
- Record counts
- Amount totals
- Hash totals
- Balance checks

**Reconciliation:**
- Input/output reconciliation
- Cross-system reconciliation
- Balance reconciliation
- Exception reconciliation

**Audit Trails:**
- Change logs
- Transaction logs
- Approval logs
- Error logs

### Control Documentation Template

**Structure:**

```
Control: Daily Transaction Reconciliation

Purpose: Ensure all transactions processed correctly

Process:
  1. Count input transactions
  2. Process transactions
  3. Count output transactions
  4. Calculate totals
  5. Compare input vs output
  6. Report discrepancies

Tolerance: +/- 0.01 (rounding)

Exception Handling:
  - Discrepancies > tolerance → Alert operations
  - Missing transactions → Investigate
  - Duplicate transactions → Reject duplicates
```

## Deliverables

### 1. Process Narratives

**Format:**
- Text narratives
- BPMN diagrams (where applicable)
- Process flow charts
- Swimlane diagrams

**Content:**
- Process name and description
- Trigger events
- Inputs and outputs
- Process steps
- Business rules
- Controls

### 2. Business Rules Catalog

**Format:**
- Structured catalog (Markdown/Excel)
- Rule ID, name, description
- Source location
- Impact analysis
- Dependencies

### 3. Business Glossary

**Format:**
- Glossary document
- COBOL → Business mapping
- Business → COBOL mapping
- Domain-specific glossaries

### 4. Control Documentation

**Format:**
- Control catalog
- Control descriptions
- Reconciliation procedures
- Exception handling

## Validation

### Business Validation

**Activities:**
- Review process narratives with business
- Validate business rules
- Confirm glossary accuracy
- Verify control understanding

**Participants:**
- Business stakeholders
- Subject matter experts
- Operations team
- Technical team

## Best Practices

### Process Mapping Best Practices

1. **Business-First**: Start with business, not code
2. **Stakeholder Engagement**: Involve business stakeholders
3. **Visual Representation**: Use diagrams where helpful
4. **Iterative Refinement**: Refine based on feedback
5. **Documentation**: Document everything

### Rule Documentation Best Practices

1. **Traceability**: Link rules to code
2. **Completeness**: Document all rules
3. **Clarity**: Use business language
4. **Validation**: Validate with business
5. **Maintenance**: Keep rules current

## Related Documents

- [Discovery and Inventory](./01-discovery-and-inventory.md)
- [COBOL Application Analysis](./03-cobol-application-analysis.md)
- [Data and Copybook Mapping](./04-data-and-copybook-mapping.md)

---

**Last Updated:** 2024  
**Maintained by:** Icarus Nova Architecture Team  
**Version:** 1.0
