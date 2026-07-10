# Crossroads: Why GSTFlow Should Come First

## Decision

**Recommendation:** Build **GSTFlow first**, while treating **CanonFlow
as a stable-but-evolving foundation**.

This is **not** because CanonFlow is finished.

It is because CanonFlow is **mature enough to support one serious domain
product**, and that product will provide the real-world pressure needed
to evolve CanonFlow intelligently.

------------------------------------------------------------------------

## The Two Choices

### Option A --- Continue polishing CanonFlow

**Pros** - Cleaner architecture - Better IR - Better emitters - Lower
technical debt

**Cons** - No real users - Requirements remain hypothetical - Risk of
polishing infrastructure forever

------------------------------------------------------------------------

### Option B --- Build GSTFlow now

**Pros** - Real users - Real invoices - Real bug reports - Real feature
requests - Validates CanonFlow through actual usage

**Cons** - CanonFlow will continue evolving - Some refactoring will be
necessary

------------------------------------------------------------------------

## Why GSTFlow First?

CanonFlow has reached an important stage.

It already has:

-   Semantic IR direction
-   Introspection
-   Multi-target philosophy
-   Constraint model
-   Verification direction
-   AI-aware architecture

It does **not** need to become perfect before it is used.

Infrastructure becomes better by supporting real products.

------------------------------------------------------------------------

## GSTFlow Becomes CanonFlow's First Customer

Instead of imagining requirements:

    Idea
        ↓
    CanonFlow

Use:

    GSTFlow
        ↓
    Real requirement
        ↓
    CanonFlow improvement

Every enhancement should answer one question:

> Is this problem **general** or **GST-specific**?

If it is general:

→ Improve CanonFlow.

If it is GST-specific:

→ Keep it inside GSTFlow.

------------------------------------------------------------------------

## Freeze the Philosophy, Not the Code

Freeze:

-   Canon IR concepts
-   Fidelity model
-   Routing principles
-   Verification philosophy

Do **not** freeze:

-   Bugs
-   Missing capabilities
-   Performance
-   Usability

CanonFlow should continue evolving---but only because GSTFlow proves a
genuine need.

------------------------------------------------------------------------

## Development Allocation

Recommended effort:

-   **80%** --- GSTFlow
-   **20%** --- CanonFlow

CanonFlow work should primarily consist of:

-   Fixing defects exposed by GSTFlow
-   Generalizing reusable abstractions
-   Improving verification and fidelity
-   Avoiding speculative features

------------------------------------------------------------------------

## Success Criteria

GSTFlow should answer:

-   Does this solve a real problem?
-   Do users trust it?
-   Which CanonFlow abstractions were actually useful?
-   Which abstractions were unnecessary?
-   Which semantic capabilities are missing?

------------------------------------------------------------------------

## Long-Term Vision

    CanonFlow
            ↓
    GSTFlow (first product)
            ↓
    Validated improvements
            ↓
    CanonFlow vNext
            ↓
    Additional domain packs

Rather than building a perfect platform first, build **one excellent
product** that continuously strengthens the platform.

------------------------------------------------------------------------

## Final Principle

> CanonFlow should evolve because **real products demand it**, not
> because more abstractions can be imagined.

GSTFlow is the shortest path to discovering what CanonFlow truly needs
to become.
