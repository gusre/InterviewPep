# FAANG-Style HLD Interviewer Playbook

A practical markdown guide for running a **high-level design interview** from the **interviewer’s perspective**.

This is built for mock interviews, peer practice, and interviewer calibration. It is structured around a FAANG-style flow: **requirements → entities → interface → architecture → deep dives**.

---

## 1) Interviewer mindset

Your job is not to trap the candidate with trivia.
Your job is to evaluate whether they can:

1. Clarify ambiguity before designing.
2. Prioritize the most important product and system goals.
3. Build a sane baseline before adding complexity.
4. Explain trade-offs clearly.
5. Adapt when new constraints appear.
6. Collaborate under pressure.

Run the round like a strong FAANG interviewer:
- keep the candidate moving,
- do not let them disappear into details too early,
- probe for reasoning, not memorization,
- reward a complete working design over a flashy but unfinished one.

---

## 2) Recommended duration and pacing

For a **45-minute round**:
- Requirements: ~5 min
- Core entities: ~2 min
- API / system interface: ~5 min
- Optional data flow: ~3–5 min
- High-level design: ~10–15 min
- Deep dives: ~10 min
- Wrap-up: remaining time

For a **60-minute round**, keep the same order and spend the extra time on deeper trade-offs, failure cases, and scaling.

---

## 3) Reusable opening script

> “This will be a design round. I’ll give you a system design problem, and I’d like you to begin by clarifying requirements and scope before jumping into architecture. Please think out loud so I can follow your reasoning. I may interrupt with follow-up questions along the way, just like in a real interview. I’m mainly looking for structure, trade-offs, and how you adapt as we go.”

Then give the problem.

Examples:
- Design a delivery platform like DoorDash / Uber Eats.
- Design a rate limiter.
- Design a Twitter-like feed system.
- Design a ride matching platform.

---

## 4) Copy-paste interviewer prompt

```text
Act as a FAANG-style high-level design interviewer.

Your job is to run a realistic HLD interview from the interviewer’s perspective.

Rules:
1. Start with a brief opening script.
2. Present one HLD problem.
3. Let the candidate drive, but interrupt naturally with probing questions.
4. Do not give away the solution unless the candidate is completely blocked.
5. Push for structure: requirements, core entities, APIs, architecture, bottlenecks, and trade-offs.
6. If the candidate goes too deep too early, steer them back.
7. If the candidate skips requirements or assumptions, challenge them.
8. Test for scalability, latency, consistency, durability, failure handling, security, and observability when relevant.
9. At the end, provide feedback in these sections:
   - What went well
   - What was missing
   - Red flags
   - Hiring recommendation

Interview structure to enforce:
- Requirements
- Core entities
- API / system interface
- Optional data flow
- High-level design
- Deep dives

Be collaborative but rigorous. Ask concise questions. Reward clear trade-offs.
```

---

## 5) Evaluation rubric

Assess the candidate on these dimensions:

### A. Problem framing
- Did they clarify the right requirements?
- Did they identify in-scope vs out-of-scope?
- Did they prioritize instead of expanding endlessly?

### B. System thinking
- Did they choose reasonable components?
- Did they explain data flow and state ownership?
- Did they connect the design back to requirements?

### C. Trade-offs
- Did they explain why one option beats another?
- Did they discuss consistency, latency, scale, cost, and simplicity?
- Did they know where to stop optimizing?

### D. Communication
- Did they think out loud?
- Did they structure the conversation clearly?
- Did they collaborate when challenged?

### E. Depth
- Could they identify bottlenecks?
- Could they improve the design under new constraints?
- Could they reason about failure modes and degraded behavior?

---

## 6) Interview flow

## Step 1 — Requirements (~5 minutes)

### What you want from the candidate
They should identify:
- top 2–4 functional requirements,
- the key non-functional requirements,
- assumptions about scale,
- explicit out-of-scope items.

### Good prompts
- “What are the top 3 things this system must do?”
- “What scale are you assuming?”
- “What matters more here: latency, availability, consistency, durability, or cost?”
- “What’s out of scope for this round?”
- “Which user journeys are on the critical path?”

### Strong signals
- Asks targeted product questions.
- Prioritizes instead of listing everything.
- Quantifies where useful.
- Connects NFRs to the domain.

### Red flags
- Starts drawing services immediately.
- Lists many features with no prioritization.
- Says only generic things like “it should scale.”
- Never asks about users, load, or trade-offs.

### What a strong interviewer does
If the candidate gives too many requirements, cut them down:

> “Let’s lock the top 3 so we have enough time to design them well.”

If the candidate gets stuck on scale math too early:

> “You can state your assumptions and do exact math only where it changes the design.”

---

## Step 2 — Core entities (~2 minutes)

### What you want
The candidate should define the main nouns/resources the system centers around.

### Good prompts
- “What are the core entities in this system?”
- “What are the main resources being created, read, updated, or served?”
- “What data objects drive your API and persistence model?”

### Strong signals
- Crisp naming.
- Distinguishes core entities from incidental details.
- Produces a small first-pass data model that can evolve later.

### Red flags
- No data model at all.
- Uses fuzzy names inconsistently.
- Confuses actions with entities.

---

## Step 3 — API / system interface (~5 minutes)

### What you want
The candidate should define the contract between clients and the system before diving into internals.

### Good prompts
- “What are the key APIs or interfaces?”
- “Would you use REST, gRPC, GraphQL, or async events here?”
- “What does the write path request look like?”
- “What does the read path response look like?”
- “Where does authentication context come from?”

### Strong signals
- Chooses a default protocol without overcomplicating it.
- Maps interfaces back to requirements.
- Uses consistent naming.
- Handles auth and identity cleanly.

### Red flags
- Skips interfaces entirely.
- Designs internal services before defining the system boundary.
- Relies on client-supplied identity when auth should provide it.

### Interviewer note
This step is especially important for product-style questions because it anchors the rest of the discussion.

---

## Step 4 — Optional data flow (~3–5 minutes)

Use this only when it helps.

### Best-fit systems
- search/indexing pipelines,
- feeds and ranking,
- crawlers,
- event-driven systems,
- asynchronous workflows,
- ingestion and processing systems.

### Good prompts
- “Walk me through the write path.”
- “Walk me through the read path.”
- “Which steps are synchronous and which are asynchronous?”
- “What gets persisted, queued, cached, or recomputed?”

### Strong signals
- Explains end-to-end flow clearly.
- Identifies state changes.
- Separates user-facing latency from background processing.

### Red flags
- Cannot explain how a request actually flows.
- Treats queues and caches as magic boxes.
- Adds async processing with no reason.

---

## Step 5 — High-level design (~10–15 minutes)

### What you want
A clean baseline architecture that satisfies the agreed requirements.

### Good prompts
- “Show me the core components and how they interact.”
- “Where does each major piece of state live?”
- “What is the simplest design that works?”
- “Where would you use cache, queue, blob store, search index, SQL, or NoSQL?”
- “Why are those the right choices?”

### Strong signals
- Starts with a simple baseline.
- Explains request flow, data flow, and state ownership.
- Keeps the design aligned to requirements.
- Notes future optimizations without derailing the core design.

### Red flags
- Adds caching, sharding, queues, and replicas before proving the base system works.
- Mentions technologies by name without justification.
- Ignores write path, read path, or persistence.
- Never explains failure domains.

### Good interruption points
Interrupt if the candidate:
- jumps into micro-optimizations too early,
- handwaves the hardest part,
- speaks for too long without checking assumptions,
- avoids committing to storage or interface choices.

Use prompts like:
- “Let’s pause and make sure the baseline works end-to-end.”
- “Where exactly is this state stored?”
- “What happens on a cache miss?”
- “How does this request complete?”

---

## Step 6 — Deep dives (~10 minutes)

### What you want
This is where you test engineering judgment.
Push on the candidate’s weakest part or the system’s most likely bottleneck.

### Common deep-dive themes
- read scaling vs write scaling
- hot partitions / hot keys
- caching and invalidation
- partitioning / sharding
- indexing and query patterns
- queue semantics and retries
- idempotency
- consistency model
- failure handling and degraded mode
- regional replication
- abuse prevention / rate limiting
- security and data isolation
- observability and alerting

### Great prompts
- “What breaks first at 100x traffic?”
- “How would you remove the biggest bottleneck?”
- “Suppose this dependency is down. What is the fallback?”
- “How do you prevent duplicate work?”
- “How would you partition this data?”
- “What metrics would you monitor?”
- “What consistency guarantees does this feature need?”

### Strong signals
- Identifies the bottleneck before optimizing blindly.
- Explains trade-offs instead of insisting on one perfect answer.
- Can evolve the design under pressure.
- Knows when eventual consistency is acceptable and when it is not.

### Red flags
- Treats scaling as just “add Redis” or “use Kafka.”
- Ignores failure scenarios.
- Cannot explain correctness under retries or duplication.
- Talks over the interviewer instead of collaborating.

---

## 7) Probe bank by topic

## Scalability
- “Is this read-heavy or write-heavy?”
- “How would you scale the hottest path?”
- “Where would partitioning be needed?”
- “What happens with skewed traffic?”

## Consistency and correctness
- “What needs strong consistency here?”
- “Where is eventual consistency acceptable?”
- “How do you avoid duplicate writes?”
- “What invariants must always hold?”

## Storage
- “Why SQL here?”
- “Why NoSQL here?”
- “What are your access patterns?”
- “Which fields need indexes?”

## Caching
- “Why cache here and not elsewhere?”
- “What is your invalidation strategy?”
- “What happens on cache stampede?”
- “How stale can data be?”

## Queues and async processing
- “Why async?”
- “What delivery semantics do you need?”
- “How do you handle retries and poison messages?”
- “How do you ensure idempotency?”

## Failure handling
- “What happens if the DB is slow?”
- “What happens if the cache is unavailable?”
- “What features can degrade gracefully?”
- “What is the blast radius of a failure?”

## Security and abuse
- “How do you authenticate callers?”
- “How do you authorize access?”
- “What abuse or fraud vectors matter?”
- “What data needs stronger protection?”

## Observability
- “What would you log?”
- “What are your top SLIs/SLOs?”
- “What dashboards and alerts would you set up?”
- “How would you debug a latency spike?”

---

## 8) Seniority calibration

## Mid-level candidate
Expect:
- a clear baseline design,
- reasonable trade-offs,
- solid communication,
- some interviewer help on deeper bottlenecks.

Do not expect:
- flawless multi-region strategy,
- exhaustive resilience design,
- perfect cost modeling.

## Senior candidate
Expect:
- better prioritization,
- more proactive deep dives,
- stronger reasoning about failure handling,
- clearer boundaries between services and ownership,
- better treatment of operations and observability.

Push harder on:
- correctness,
- reliability,
- migration strategy,
- operability,
- cost vs complexity.

---

## 9) Common interviewer mistakes

Avoid these:
- Turning the round into trivia about specific technologies.
- Forcing exact numbers when they do not affect the design.
- Letting the candidate spend 20 minutes on requirements.
- Driving the whole design yourself.
- Penalizing a candidate for not choosing your personal favorite tech stack.
- Interrupting so often that the candidate cannot build momentum.

A good interviewer challenges the design without hijacking it.

---

## 10) End-of-round feedback template

Use this after the interview.

### What went well
- Candidate clarified requirements before designing.
- Candidate built a workable baseline before optimization.
- Candidate explained trade-offs clearly.
- Candidate responded well to probing.

### What was missing
- Needed stronger prioritization.
- API boundary was underdeveloped.
- Failure handling was too shallow.
- Storage/indexing choices lacked justification.
- Deep dives did not connect clearly to bottlenecks.

### Red flags
- Jumped into architecture without understanding the problem.
- Overengineered too early.
- Could not explain request flow end-to-end.
- Treated buzzwords as design.
- Struggled to collaborate under interruption.

### Hiring recommendation
- Strong Hire
- Hire
- Lean Hire
- Lean No Hire
- No Hire

Add 2–4 lines of rationale tied to observable behavior.

---

## 11) One-page interviewer checklist

Use this during the round.

### Before the round
- Pick the problem.
- Decide candidate level.
- Decide 2–3 deep-dive themes.

### During the round
- Did they clarify functional requirements?
- Did they clarify non-functional requirements?
- Did they prioritize?
- Did they define core entities?
- Did they define APIs / interfaces?
- Did they present a working baseline architecture?
- Did they explain read path and write path?
- Did they identify bottlenecks?
- Did they handle trade-offs well?
- Did they collaborate?

### After the round
- What was strongest?
- What was weakest?
- Was the candidate complete enough for the level?
- What would change your recommendation up or down?

---

## 12) Short version interviewer command

Use this when you want something compact.

```text
Run a FAANG-style HLD interview.
Evaluate the candidate on requirement clarification, prioritization, architecture quality, trade-offs, bottleneck handling, and communication.
Keep the flow:
1. Requirements
2. Core entities
3. API / interface
4. Optional data flow
5. High-level design
6. Deep dives
Do not give away the answer early. Probe naturally. End with structured feedback and a hiring recommendation.
```
