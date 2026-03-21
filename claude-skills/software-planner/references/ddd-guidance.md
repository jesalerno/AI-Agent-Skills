# Domain-Driven Design (DDD) Guidance

Apply DDD when the system has a rich domain — multiple distinct business capabilities, complex
business rules owned by different teams, or a long-lived codebase that will evolve with the
business. Don't apply it to simple CRUD apps or infrastructure tools; the overhead isn't worth it
for thin domains.

---

## When to apply DDD

Apply DDD if any of the following are true:
- The system spans multiple distinct business capabilities (e.g., Ordering, Inventory, Billing)
- Different parts of the domain are owned by different teams or subject-matter experts
- Complex business rules govern state transitions (e.g., order fulfilment workflows)
- The system is expected to grow and evolve over years, not months
- The client uses rich domain language that differs from generic technical terms

Skip DDD (or apply lightly) if:
- The system is primarily CRUD with minimal business logic
- It's a short-lived tool or internal utility
- The team is small and the domain is well understood by everyone

---

## Strategic design

### Bounded contexts

A bounded context is a domain area with a clear boundary inside which a particular domain model
applies. Each bounded context has its own language — the word "Account" might mean something
different in a Banking bounded context than in a Customer Management bounded context.

**In the spec:** Each major bounded context becomes an Epic. Name the Epic after the bounded
context. In `architecture_notes`, define what "Account", "User", "Order" etc. mean *within*
each context — don't assume words mean the same thing across contexts.

### Ubiquitous language

The ubiquitous language is the shared vocabulary between developers and domain experts within
a bounded context. It must be used consistently in:
- All `source_text` fields in the requirements register
- All Story `description` and `title` fields
- All Task `title` fields
- The `architecture_notes` glossary
- (Eventually) variable names, class names, database schema

**In the spec:** Include a short glossary in `architecture_notes` defining the core terms for
each bounded context. Example:
- **Order**: A confirmed intention to purchase. An Order has an OrderId, belongs to one Customer,
  and contains one or more OrderLines.
- **OrderLine**: A single product and quantity within an Order.
- **Customer**: A registered user who can place Orders.

### Context map

A context map shows how bounded contexts relate to each other. Key relationships:

| Relationship | Meaning | When to use |
|---|---|---|
| Shared Kernel | Two contexts share a small common model | When two teams share code they both maintain |
| Customer / Supplier | Upstream context defines the interface; downstream consumes it | When one team's output feeds another |
| Anti-Corruption Layer (ACL) | Downstream context translates the upstream model into its own language | When integrating with a legacy or external system |
| Open Host Service | Upstream publishes a stable published language (API/events) for all consumers | Microservices, public APIs |
| Conformist | Downstream adopts the upstream model as-is | When cost of translation exceeds benefit |

**In the spec:** Document the context map in `architecture_notes`. Call out ACLs explicitly —
they become infra Tasks in the decomposition (wrapping the external API in an adapter).

---

## Tactical design

### Aggregates

An aggregate is a cluster of domain objects treated as a unit for data changes. One object is
the aggregate root — the only entry point for external interactions.

**In the spec:** Identify aggregates in `architecture_notes`. Each aggregate root typically
becomes an entity in your data model and a resource in your API. Stories should operate on
aggregates ("As a customer, I want to cancel my Order" operates on the Order aggregate).

**Aggregate design rules to capture:**
- Aggregates reference other aggregates by ID only (no direct object references across boundaries)
- All invariants (business rules) within an aggregate are enforced by the aggregate root
- Keep aggregates small — if an aggregate has more than 5–7 entities, consider splitting

### Domain events

A domain event is a significant state change in the domain that other parts of the system (or
other systems) may need to react to.

**In the spec:** List domain events in `architecture_notes`. For each event, note:
- The trigger: what action in which bounded context produces it
- The consumers: which other contexts or systems need to react
- The payload: what data the event carries

**Common domain events to look for:**
- `{Entity}Created`, `{Entity}Updated`, `{Entity}Deleted`
- State transitions: `OrderPlaced`, `OrderShipped`, `OrderCancelled`
- Business milestones: `PaymentConfirmed`, `InventoryDepleted`, `CustomerVerified`
- Cross-context triggers: `InvoiceGenerated` (triggers Billing context)

**In the decomposition:** Each significant domain event that crosses a context boundary becomes
at least one dev Task (publish) and one dev Task (subscribe/handle) plus one test Task.

### Entities vs value objects

| Concept | Identity | Mutability | Example |
|---|---|---|---|
| Entity | Has a unique ID; two entities with the same data are still different | Mutable | Customer (CustomerID matters), Order |
| Value Object | No identity; two value objects with the same data are the same | Immutable | Address, Money (amount + currency), DateRange |

**In the spec:** Calling out whether something is an entity or a value object helps prevent
a common mistake where a value object (e.g., an address) gets its own table with a surrogate
key when it should be embedded in the parent entity.

---

## DDD checklist for the spec

Before delivering the Phase 2 JSON, verify:

- [ ] `architecture_notes` contains a bounded context list
- [ ] `architecture_notes` contains a ubiquitous language glossary for each bounded context
- [ ] `architecture_notes` contains a context map (even if just two contexts)
- [ ] Epics are named after bounded contexts (where DDD applies)
- [ ] Story and Task titles use domain vocabulary from the glossary
- [ ] Domain events are listed and their cross-context flows are noted
- [ ] ACL Tasks are present for every external system integration
- [ ] Aggregates are identified and aggregate roots named

---

## Sources

- Evans, Eric. *Domain-Driven Design: Tackling Complexity in the Heart of Software*. Addison-Wesley, 2003.
- Vernon, Vaughn. *Implementing Domain-Driven Design*. Addison-Wesley, 2013.
- [martinfowler.com/tags/domain driven design.html](https://martinfowler.com/tags/domain%20driven%20design.html)
- [ddd-crew/ddd-starter-modelling-process](https://github.com/ddd-crew/ddd-starter-modelling-process)
