# Kutcheri — Requirements Specification

**Product:** Season console for a Carnatic sabha's booking desk
**Version:** 0.1 (draft) · **Audience:** sabha administrator, box-office staff, treasurer
**One-line job:** run a Margazhi season — bookings, houses, and artist settlements —
from a single desk, offline-tolerant, with every money and capacity rule enforced.

---

## 1. Context & scope

A *sabha* stages a season of *kutcheris* (concerts) across a few weeks. Each
kutcheri has an artist (or ensemble), a venue, a date/slot, a ticketed house,
and a financial settlement with the artist. The desk today runs on spreadsheets
and WhatsApp; this console replaces that with one system of record.

**In scope (v0.1):** season calendar, artist & venue registry, per-kutcheri
bookings and house tracking, ticket-tier pricing, settlement calculation,
season-level financial roll-up.
**Out of scope (v0.1):** public ticket-buying site (this is the *desk*, not the
patron), seat-map selection, payment gateway, GST filing, multi-season history.
Deferred with `post-v1` labels — not "no", just "not first".

**Non-goals:** not a CRM, not an accounting package, not a streaming platform.
The console does one thing: help the desk run *this* season without dropping a
booking or miscounting a settlement.

---

## 2. Actors & roles

| Role | Can do | Cannot do |
|---|---|---|
| **Administrator** | everything; configure venues, tiers, season dates | — |
| **Box-office** | record bookings, adjust house counts, change kutcheri status | edit artist fees, delete kutcheris |
| **Treasurer** | view all financials, mark settlements paid, export | change bookings or house counts |

Role separation matters because money and house-count authority should not sit
in the same hands — a real internal-control requirement, not decoration.

---

## 3. Core entities (the domain)

- **Artist** — name, form (Vocal/Violin/Veena/Mridangam/Flute/Ensemble),
  contact, standard fee band.
- **Venue** — name, seating capacity, address, house tiers available.
- **Kutcheri** — the central entity: artist × venue × datetime, with status,
  ticket tiers, house counts, and the artist fee agreed for *this* engagement
  (which may differ from the artist's standard band).
- **TicketTier** — name (e.g. Patron/Regular/Student), price, allocation.
- **Booking** — a sale against a tier: quantity, channel, timestamp.
- **Settlement** — derived: gate receipts − artist fee − venue cost, per kutcheri.

---

## 4. Functional requirements

### 4.1 Season calendar
- FR-1 Display all kutcheris of the active season as a list and a date view.
- FR-2 Filter by status (all / on-sale / confirmed / sold-out / on-hold) and by venue.
- FR-3 Selecting a kutcheri opens its detail: house, tiers, fee, settlement.

### 4.2 Bookings & house
- FR-4 Record a booking against a ticket tier; house count updates immediately.
- FR-5 The system must never let total bookings exceed venue capacity (see C-1).
- FR-6 A tier's sales must never exceed its allocation (see C-2).
- FR-7 House-fill % is shown per kutcheri and rolled up per season.
- FR-8 Status transitions follow a defined machine (see C-6); illegal
  transitions are refused with a message naming the allowed next states.

### 4.3 Scheduling integrity
- FR-9 A venue cannot host two kutcheris in overlapping time windows (see C-3).
- FR-10 An artist cannot be double-booked in overlapping windows across venues.
- FR-11 A kutcheri's datetime must fall within the season's date range.

### 4.4 Financials & settlement
- FR-12 Per kutcheri, compute gate receipts = Σ(tier price × tier sold).
- FR-13 Settlement = gate receipts − artist fee − venue cost; shown with sign.
- FR-14 Season roll-up: total box office, total artist outlay, net to sabha.
- FR-15 Treasurer can mark a settlement paid; paid settlements are immutable (see C-5).
- FR-16 Export the season's financials as CSV.

### 4.5 Registry
- FR-17 CRUD for artists and venues (Administrator only).
- FR-18 Creating a kutcheri references an existing artist and venue (see C-4).

---

## 5. Constraints (the rules the system must enforce)

These are written as constraints deliberately — they are where correctness
lives, and where a future CanonFlow harness would derive proofs. Each is
tagged by the layer that should enforce it.

| # | Constraint | Layer | Kind |
|---|---|---|---|
| C-1 | `bookings_total ≤ venue.capacity` | DB CHECK + app | numeric bound (liftable) |
| C-2 | `tier_sold ≤ tier_allocation`; `Σ allocations ≤ capacity` | DB + app | bound + aggregate |
| C-3 | no two kutcheris share a venue in overlapping `[start,end)` | app / exclusion constraint | temporal / interval |
| C-4 | kutcheri.artist_id, venue_id are valid FKs | DB FK | referential |
| C-5 | a settlement with `paid_at NOT NULL` is immutable | app + DB trigger | state / temporal |
| C-6 | status ∈ {hold, onsale, confirmed, soldout, cancelled}; transitions: hold→onsale→{confirmed,soldout}; any→cancelled | app state machine | vocabulary + transition |
| C-7 | prices ≥ 0; fees ≥ 0; capacity > 0 | DB CHECK | numeric bound (liftable) |
| C-8 | kutcheri.datetime within season window | DB CHECK (cross-field) | temporal |
| C-9 | soldout ⟺ house_fill = 100% (status must match reality) | app invariant | cross-field consistency |

Note for the future harness: C-1, C-2, C-7 lift cleanly to `Refined` proofs;
C-3, C-5, C-6, C-8, C-9 are the honest Opaque / app-layer set — exactly the
three-layer classification the Mudra dogfood tests. Kutcheri is a natural
fourth dogfood whenever the harness is revisited.

---

## 6. Non-functional requirements

- **NFR-1 Offline tolerance:** the desk loses wifi mid-season; bookings must be
  recordable offline and reconcile on reconnect (last-write-wins is *not*
  acceptable for house counts — needs a conflict rule, see open question Q-2).
- **NFR-2 Auditability:** every booking, status change, and settlement edit is
  logged with actor, timestamp, and before/after. The treasurer must be able to
  answer "who changed this house count and when".
- **NFR-3 Latency:** any desk action (record booking, change status) reflects in
  under 200 ms locally; this is a point-of-sale-grade interaction.
- **NFR-4 Accessibility:** keyboard-operable, visible focus, reduced-motion
  respected, readable in a dim concert-hall foyer (dark theme is functional,
  not just aesthetic).
- **NFR-5 Money correctness:** all currency in integer paise or `decimal`,
  never float. Rounding rule stated once and applied everywhere.
- **NFR-6 Data volume:** one season ≈ 30 kutcheris × ~1600 seats; trivial scale,
  so simplicity beats cleverness. No premature optimization.

---

## 7. Success criteria (falsifiable)

- A box-office user records 50 bookings across a season and the house counts,
  fills, and season roll-up are correct to the rupee.
- Attempting to oversell a venue (C-1) or double-book (C-3) is refused with a
  message that names the conflict, not a generic error.
- A treasurer exports the season and the CSV totals reconcile with the on-screen
  roll-up exactly.
- The console is usable one-handed on a phone in a dim foyer.
- A new administrator sets up a venue, a tier, and a kutcheri in under 10
  minutes without documentation.

---

## 8. Open questions

- **Q-1** Does the sabha need multi-season history in v1, or is one active
  season enough? (Assumed: one season. Confirm.)
- **Q-2** Offline conflict rule for house counts: sum-of-deltas vs
  last-write-wins vs manual reconcile? (Recommend sum-of-deltas — bookings are
  additive events, not state overwrites.)
- **Q-3** Venue cost model: flat rental, % of gate, or per-seat? Affects C-3
  settlement math.
- **Q-4** Are complimentary/press tickets tracked against house (reducing
  saleable inventory) or separately? Affects C-1.
- **Q-5** Single sabha or multi-sabha tenant? Changes the whole data model if
  the latter — decide before building.

---

*The tala keeps time; this spec keeps the count. Answer Q-1..Q-5 and the
data model + first vertical slice (season list → booking → settlement) is the
buildable v0.1.*
