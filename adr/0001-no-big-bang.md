# ADR-0001: No Big-Bang Migration

## Status

**Accepted** - This is a fundamental architectural decision that cannot be changed without significant impact.

## Context

COBOL to Java migrations face a critical decision: whether to migrate everything at once (big-bang) or incrementally. Big-bang migrations are tempting because they seem faster, but they carry enormous risk.

Big-bang migrations have:
- High risk of failure
- Difficult rollback
- Business disruption potential
- Limited validation
- All-or-nothing approach

We need to decide on the migration approach.

## Decision

**We will NOT perform big-bang migrations. All migrations will be incremental using the Strangler Fig pattern or similar incremental approaches.**

The migration will:
1. **Incremental Migration**: Migrate component by component
2. **Parallel Runs**: Run legacy and new in parallel
3. **Gradual Cutover**: Cutover gradually, not all at once
4. **Rollback Capability**: Always maintain rollback capability
5. **Evidence-Based**: Migrate based on evidence, not faith

## Consequences

### Positive

✅ **Risk Reduction**: Lower risk through incremental approach  
✅ **Business Continuity**: Business continues operating  
✅ **Validation**: Validate each component before next  
✅ **Rollback**: Easy rollback if issues  
✅ **Learning**: Learn and improve with each component  
✅ **Confidence**: Build confidence gradually  

### Negative

❌ **Longer Timeline**: Takes longer than big-bang  
❌ **Dual Maintenance**: Maintain both systems during transition  
❌ **Complexity**: More complex than big-bang  
❌ **Coordination**: Requires coordination between systems  

### Mitigation

- Plan migration waves carefully
- Minimize dual maintenance period
- Use automation for coordination
- Clear exit criteria for each wave

## Implementation

### Incremental Approach

**Migration Waves:**
- Wave 1: Low-risk components
- Wave 2: Medium-risk components
- Wave 3: High-risk components
- Wave 4: Utilities and shared code

**Wave Criteria:**
- Clear boundaries
- Independent migration
- Validated before next wave
- Rollback capability

### Strangler Fig Pattern

**Pattern:**
- Legacy and new coexist
- Routing layer directs traffic
- Gradual migration
- Legacy decommissioned when complete

## Related Decisions

- [ADR-0003: Parallel Run Required](./0003-parallel-run-required.md)
- [Migration Strategies](../docs/06-migration-strategies.md)

## References

- [Overview Methodology](../docs/00-overview-methodology.md)
- [Parallel Run and Cutover](../docs/09-parallel-run-and-cutover.md)

---

**Date**: 2026  
**Deciders**: Icarus Nova Architecture Team  
**Status**: Accepted
