# Phase 9: Parallel Run and Cutover

> **Icarus Nova** | Execute parallel runs and manage cutover with evidence, not faith.

## Overview

Parallel Run and Cutover is the moment of truth. This phase executes both COBOL and Java systems in parallel, compares outputs, reconciles data, and manages the cutover with comprehensive evidence and rollback capability.

## Objectives

1. **Parallel Execution**: Run both systems in parallel
2. **Output Comparison**: Compare outputs systematically
3. **Reconciliation**: Reconcile data differences
4. **Gradual Cutover**: Cutover in controlled phases
5. **Rollback Capability**: Maintain rollback readiness

## Step 9.1: Parallel Run

### Parallel Run Strategy

**Approach:**
- Run COBOL and Java systems simultaneously
- Process same inputs
- Compare outputs
- Reconcile differences
- Document results

### Parallel Run Types

**Batch Parallel Run:**
- Run batch jobs in parallel
- Compare output files
- Reconcile totals
- Validate completeness

**Online Parallel Run:**
- Route requests to both systems
- Compare responses
- Validate functionality
- Monitor differences

### Parallel Run Process

**Execution Flow:**
```
1. Prepare Input Data
2. Execute COBOL System
3. Execute Java System (in parallel)
4. Capture Outputs
5. Compare Outputs
6. Reconcile Differences
7. Document Results
8. Decision: Continue or Investigate
```

### Output Comparison

**Comparison Strategy:**
- **Exact Match**: For deterministic outputs
- **Tolerance**: For numeric differences (rounding)
- **Ignore**: For non-critical differences (timestamps)
- **Document**: All differences

**Comparison Template:**

| Field | COBOL Value | Java Value | Difference | Status | Action |
|-------|-------------|------------|------------|--------|--------|
| Record Count | 10,000 | 10,000 | 0 | ✅ Match | None |
| Total Amount | 1,234,567.89 | 1,234,567.90 | 0.01 | ⚠️ Tolerance | Accept |
| Customer ID | 12345 | 12345 | 0 | ✅ Match | None |
| Timestamp | 2024-01-15 10:30:00 | 2024-01-15 10:30:01 | 1s | ℹ️ Ignore | None |

### Drift Handling

**Drift Definition:**
- Differences between COBOL and Java outputs
- Expected vs. unexpected differences
- Acceptable vs. unacceptable differences

**Drift Categories:**
- **Expected Drift**: Known, acceptable differences
- **Investigation Needed**: Differences requiring analysis
- **Critical Drift**: Unacceptable differences

**Drift Resolution:**
1. Identify drift
2. Categorize drift
3. Investigate if needed
4. Fix if critical
5. Document resolution

## Step 9.2: Cutover Strategy

### Cutover Approaches

**Big-Bang Cutover:**
- ❌ Not recommended
- High risk
- All-or-nothing
- Difficult rollback

**Gradual Cutover (Recommended):**
- ✅ Recommended
- Low risk
- Phased approach
- Easy rollback

**Feature Flag Cutover:**
- Route by feature flag
- Gradual traffic shift
- Easy rollback
- A/B testing capability

### Cutover Phases

**Phase 1: Read-Only (0% Traffic)**
- Java system ready
- No traffic yet
- Monitoring active
- Validation complete

**Phase 2: Shadow Mode (0% Traffic)**
- Java processes in shadow
- No impact on production
- Compare outputs
- Validate behavior

**Phase 3: Canary (5-10% Traffic)**
- Small percentage to Java
- Monitor closely
- Quick rollback if issues
- Validate at scale

**Phase 4: Gradual Ramp (25%, 50%, 75%)**
- Gradually increase traffic
- Monitor at each step
- Validate performance
- Ensure stability

**Phase 5: Full Cutover (100%)**
- All traffic to Java
- COBOL in standby
- Monitor closely
- Ready for rollback

**Phase 6: Decommission (After Validation)**
- COBOL system decommissioned
- After successful validation period
- Archive COBOL system
- Complete migration

### Cutover Checklist

**Pre-Cutover:**
- [ ] All tests pass
- [ ] Parallel run successful
- [ ] Reconciliation complete
- [ ] Performance validated
- [ ] Monitoring configured
- [ ] Rollback plan ready
- [ ] Team trained
- [ ] Documentation complete
- [ ] Support procedures ready
- [ ] Business approval obtained

**During Cutover:**
- [ ] Execute cutover plan
- [ ] Monitor closely
- [ ] Validate functionality
- [ ] Check performance
- [ ] Verify data integrity
- [ ] Handle issues promptly

**Post-Cutover:**
- [ ] Validate system stability
- [ ] Monitor for issues
- [ ] Collect metrics
- [ ] Document lessons learned
- [ ] Plan next phase

## Rollback Planning

### Rollback Triggers

**Automatic Rollback:**
- Critical errors
- Data corruption
- Performance degradation
- System instability

**Manual Rollback:**
- Business decision
- User complaints
- Operational issues
- Risk assessment

### Rollback Process

**Rollback Steps:**
1. Detect issue
2. Assess severity
3. Decision: Rollback or fix
4. Execute rollback
5. Restore COBOL system
6. Validate restoration
7. Document rollback
8. Post-mortem analysis

### Rollback Checklist

**Rollback Readiness:**
- [ ] Rollback plan documented
- [ ] Rollback procedures tested
- [ ] COBOL system maintained
- [ ] Data synchronization ready
- [ ] Team trained on rollback
- [ ] Rollback decision criteria defined
- [ ] Communication plan ready

## Runbook

### Runbook Structure

**Sections:**
- Overview
- Prerequisites
- Execution steps
- Monitoring
- Troubleshooting
- Rollback procedures
- Contact information

### Runbook Template

```markdown
# Cutover Runbook: Wave 1

## Overview
Cutover of Customer Management module from COBOL to Java.

## Prerequisites
- All tests pass
- Parallel run successful for 2 weeks
- Business approval obtained

## Execution Steps
1. Enable feature flag: 5% traffic
2. Monitor for 1 hour
3. If stable, increase to 25%
4. Monitor for 2 hours
5. Continue gradual ramp

## Monitoring
- Error rate: < 0.1%
- Response time: < 2x legacy
- Data reconciliation: 100% match

## Troubleshooting
[Issue resolution procedures]

## Rollback
[Rollback procedures]
```

## Deliverables

### 1. Runbook

**Content:**
- Cutover procedures
- Monitoring procedures
- Troubleshooting guide
- Rollback procedures

### 2. Rollback Checklist

**Content:**
- Rollback triggers
- Rollback steps
- Validation procedures
- Communication plan

### 3. Contingency Plan

**Content:**
- Risk scenarios
- Response procedures
- Escalation paths
- Communication plan

### 4. Cutover Plan

**Content:**
- Cutover phases
- Timeline
- Success criteria
- Risk mitigation

## Best Practices

### Parallel Run Best Practices

1. **Run Long Enough**: Minimum 2-4 weeks
2. **Compare Systematically**: Automated comparison
3. **Document Everything**: All differences documented
4. **Investigate Differences**: Understand all differences
5. **Validate Fixes**: Re-run after fixes

### Cutover Best Practices

1. **Gradual Approach**: Never big-bang
2. **Monitor Closely**: Watch everything
3. **Be Ready to Rollback**: Always ready
4. **Communicate**: Keep stakeholders informed
5. **Document**: Document everything

## Related Documents

- [Testing Strategy](./08-testing-strategy.md)
- [Risk and Governance](./10-risk-and-governance.md)
- [ADR-0003: Parallel Run Required](../adr/0003-parallel-run-required.md)

---

**Last Updated:** 2024  
**Maintained by:** Icarus Nova Architecture Team  
**Version:** 1.0
