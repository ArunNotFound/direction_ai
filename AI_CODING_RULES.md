# CanonFlow Architectural Principles

This document captures the strict architectural philosophy and F# engineering practices developed during the CanonFlow project. These high standards should be applied to all future work and provided as context to AI coding assistants.

## Rule: `architectural_stewardship`

**Classification:** Rule (Universal Behavioral Guardrail)
**Rationale:** To prevent AI "feature bloat" (suggesting runtime services, graph databases, or SaaS platforms) and maintain strict adherence to build-time correctness and mathematical boundaries.

### Instructions for AI Assistants:
# Architectural Stewardship & "Take the Insight, Refuse the Platform"
When proposing or evaluating system architectures, especially those involving AI enhancements or data graphs:
1. **Build-Time over Runtime (ADR-009):** Always prefer generating static artifacts, typed code (`.fs`, `.ts`, `.kt`), and configuration files at build-time. Reject runtime sidecars, active services, or graph databases unless absolutely required.
2. **Take the Insight, Refuse the Platform:** If an AI model suggests a complex runtime feature (e.g., "Semantic Graph as a Service"), extract the underlying value (e.g., "AI agents need schema context") and fulfill it via static code generation (e.g., emitting an `agent-context.json` file).
3. **The Noun vs. Verb Boundary:** 
   - **Nouns (Structural Truth):** Must be enforced at the lowest physical layer (Database `CHECK` constraints, composite keys, enums).
   - **Verbs (Business Behavior/State Transitions):** Must be enforced in the Application layer (Domain-Driven Design).
4. **F# Code Generation over Type Providers:** In F#, prefer explicit, text-based code generation (transpilation) over opaque Type Providers to ensure transparency, portability, and diffability.
5. **The Semantic Compiler Model:** Treat all code-generation tasks as a strict compiler pipeline: `Parse -> Normalize -> Optimize -> Verify -> Emit`. Parse into an Abstract Syntax Tree (Lattice/Refined types), verify mathematically, and emit proven contracts.
