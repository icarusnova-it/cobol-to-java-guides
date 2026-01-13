# ADR-0002: Canonical Data Model

## Status

**Accepted** - This decision establishes the canonical data model approach.

## Context

COBOL data structures (copybooks) need to be mapped to Java. We need to decide whether to:
- Map directly from COBOL to Java
- Create a canonical model in between
- Use multiple target formats

Direct mapping creates tight coupling and makes it hard to support multiple formats or evolve the model.

## Decision

**We will use a canonical data model as an intermediate representation between COBOL and Java formats.**

The approach will:
1. **Canonical Model**: Create canonical Java model
2. **Transformation Layer**: Transform COBOL → Canonical → Java
3. **Format Independence**: Canonical model independent of formats
4. **Versioning**: Version canonical model
5. **Multiple Targets**: Support multiple target formats from canonical

## Consequences

### Positive

✅ **Format Independence**: Not tied to COBOL format  
✅ **Multiple Targets**: Support multiple output formats  
✅ **Evolution**: Easier to evolve data model  
✅ **Testing**: Easier to test transformations  
✅ **Reusability**: Canonical model reusable  

### Negative

❌ **Additional Layer**: Extra transformation layer  
❌ **Complexity**: More complex than direct mapping  
❌ **Performance**: Additional transformation overhead  

### Mitigation

- Efficient transformation
- Cache transformations where possible
- Optimize transformation logic
- Use efficient libraries

## Implementation

### Canonical Model Structure

**Approach:**
- Java domain models
- Independent of COBOL structure
- Business-focused
- Versioned

### Transformation Layer

**Transformations:**
- COBOL → Canonical
- Canonical → Java DTOs
- Canonical → API formats
- Canonical → Database entities

## Related Decisions

- [Data and Copybook Mapping](../docs/04-data-and-copybook-mapping.md)
- [ADR-0004: Contract-First Integration](./0004-contract-first-integration.md)

## References

- [Copybook to Java DTO Example](../examples/copybook-to-java-dto-example.md)

---

**Date**: 2026  
**Deciders**: Icarus Nova Architecture Team  
**Status**: Accepted
