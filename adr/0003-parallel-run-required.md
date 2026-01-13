# ADR-0003: Parallel Run Required

## Status

**Accepted** - This decision makes parallel runs mandatory for critical components.

## Context

For critical COBOL systems, especially in banking and financial services, we need to validate Java implementation before cutover. Options:
- Cutover without validation (high risk)
- Parallel run for validation (lower risk)
- Extended testing only (moderate risk)

We need to decide on validation approach.

## Decision

**Parallel runs are REQUIRED for all critical components (Tier 0 and Tier 1). Parallel runs are strongly recommended for Tier 2 components.**

Parallel runs will:
1. **Run Both Systems**: Execute COBOL and Java in parallel
2. **Compare Outputs**: Systematically compare outputs
3. **Reconcile Data**: Reconcile data differences
4. **Validate Equivalence**: Prove functional equivalence
5. **Evidence-Based**: Provide evidence for cutover decision

## Consequences

### Positive

✅ **Risk Mitigation**: Significant risk reduction  
✅ **Evidence-Based**: Evidence of correctness  
✅ **Confidence**: Build confidence before cutover  
✅ **Validation**: Validate before production  
✅ **Compliance**: Meets regulatory requirements  

### Negative

❌ **Cost**: Dual execution cost  
❌ **Complexity**: More complex execution  
❌ **Time**: Takes time to validate  
❌ **Coordination**: Requires coordination  

### Mitigation

- Automated comparison
- Efficient reconciliation
- Clear success criteria
- Time-boxed parallel runs

## Implementation

### Parallel Run Process

**Process:**
1. Prepare input data
2. Execute COBOL system
3. Execute Java system (parallel)
4. Capture outputs
5. Compare outputs
6. Reconcile differences
7. Document results

### Success Criteria

**Criteria:**
- Output match (within tolerance)
- Data reconciliation successful
- Performance acceptable
- Error rate acceptable
- Duration: Minimum 2-4 weeks

## Related Decisions

- [ADR-0001: No Big-Bang](./0001-no-big-bang.md)
- [Parallel Run and Cutover](../docs/09-parallel-run-and-cutover.md)

## References

- [Testing Strategy](../docs/08-testing-strategy.md)

---

**Date**: 2026  
**Deciders**: Icarus Nova Architecture Team  
**Status**: Accepted
