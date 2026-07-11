Recap of both, on the F#-compatibility axis — and the honest headline: **DuckDB is excellent for F#, Avro is workable but the weakest of the serialization options for your specific type system.**

**DuckDB — the strong one:**
- **Native DU support** — the standout finding from earlier: DuckDB's `UNION` type is a genuine tagged union (the docs call it equivalent to functional-language sum types), with `union_tag()` recovering the active case. **No other mainstream database has this.** F# `Payment = Cash of decimal | Card of string * decimal` maps structurally, and the round-trip preserves *which case* — not just "a string with a CHECK."
- **F# access:** via the ADO.NET provider (`DuckDB.NET`) — standard `IDbConnection` patterns, so it works with any F# data-access approach you already use. Nothing exotic.
- **Where it fits your stack:** the DuckDbEmitter dormant (EMIT-FINAL B7) — the faithful target for payload-carrying DU *state*. Wake condition: a real domain with payload-DU state needing persistence. Your hospital-core's 7 payload cases turned out to be *commands/events* → facts store, not DuckDB — so nothing has actually woken it yet.
- **Caveats:** UNION implicit casts are deliberately restricted (to avoid silent information loss), and there have been struct-size mismatch issues in file I/O. Faithful ≠ frictionless.

**Apache Avro — the honest assessment:**
- **What it is:** a compact binary serialization format with a **schema** carried alongside the data, built for schema *evolution* (add/remove fields with defined compatibility rules). It's the Kafka ecosystem's default.
- **Sum-type story:** Avro has **unions** (`["null", "string", "int"]`) — but they're *type* unions, not *tagged* unions with named cases carrying payloads. F#'s `Card of last4: string * amt: decimal` doesn't map cleanly; you'd encode it as a record-per-case wrapped in a union, which works but loses the natural shape. Better than Parquet (which can't do sums at all), worse than DuckDB (which does them natively).
- **Decimals:** Avro has a `decimal` logical type (bytes + precision/scale) — so money survives, but it's a logical-type convention, not a first-class primitive. Handle with care in any cross-runtime agreement test.
- **F# support:** libraries exist (Chr.Avro, Apache.Avro for .NET) but the F#-idiomatic story is thinner than for JSON (Thoth) — you'd likely be writing mapping code rather than getting DU support for free.
- **Where it *would* fit:** if you ever needed a compact, schema-evolving wire format for event streams — not currently anything you have.

**The routing recap, since this is really an emit-target question:**

| Target | Sum types (DU) | F# ergonomics | Your use |
|---|---|---|---|
| **Postgres** | ✗ (encode + lose) | Excellent | **The one powered path** (Postgres-A) |
| **DuckDB** | ✓ **native** | Good (ADO.NET) | Dormant — for payload-DU *state* |
| **Avro** | partial (untagged unions) | Fair | No current use — file if event streams appear |
| **Parquet** | ✗ **structurally impossible** (Dremel has no "OR" level) | Good | Dormant — nested *products* for analytics |
| **JSON (Thoth)** | ✓ (you control the encoding) | **Excellent, Fable-compatible** | **In use** — canonical envelope |

**The practical takeaway for right now:** you don't need either. Your live serialization is JSON via Thoth (which works in *both* .NET and Fable — the One-Engine Law depends on that dual-target property, which neither DuckDB nor Avro has). DuckDB is a strong *someday* emit target with a real technical advantage; Avro has no current wake condition in any of your plans. Neither is a decision you need to make before EDIFlow's seed or GSTFlow's first user.

**The one thing worth remembering:** if a domain ever appears with genuine payload-carrying DU *state* (not commands/events), DuckDB is the only faithful home in the SQL world — that's a real, rare asset. Avro is a tool for a problem (schema-evolving binary event streams) you don't currently have. Keep the first in the dormant drawer; leave the second off the board entirely until a Kafka-shaped need shows up. 🗄️
