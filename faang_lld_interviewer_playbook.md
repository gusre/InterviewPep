# FAANG-Style LLD Interviewer Playbook

A practical markdown guide for running a **low-level design interview** from the **interviewer’s perspective**.

This is built for mock interviews, peer practice, and interviewer calibration. It is structured around a FAANG-style flow: **requirements → entities → class design → implementation → extensibility**.

---

## 1) Interviewer mindset

In an LLD round, your goal is not to check whether the candidate can memorize design patterns.
Your goal is to evaluate whether they can:

1. Turn an ambiguous prompt into a usable spec.
2. Choose sensible abstractions and ownership boundaries.
3. Model state and behavior cleanly.
4. Write or explain production-quality core logic.
5. Handle edge cases.
6. Extend the design without breaking it.

Run the round like a strong FAANG interviewer:
- keep the candidate grounded in requirements,
- push them toward clear ownership,
- do not let them drown in syntax too early,
- reward clean reasoning over fancy pattern-dropping.

---

## 2) Recommended duration and pacing

For a **45-minute round**:
- Requirements: ~5 min
- Entities and relationships: ~3 min
- Class design: ~10–15 min
- Core implementation: ~10 min
- Extensions / follow-ups: ~5–10 min
- Wrap-up: remaining time

For a **60-minute round**, spend more time on implementation trade-offs, extensibility, concurrency, and error handling.

---

## 3) Reusable opening script

> “This will be a low-level design round. I’ll give you a problem statement, and I’d like you to start by clarifying requirements and scope before jumping into classes or code. Please think out loud as you design. I may interrupt with questions or follow-ups along the way. I’m mainly looking for clean abstractions, ownership, core logic, and how well you adapt when requirements change.”

Then give the problem.

Examples:
- Design a chess game.
- Design a parking lot.
- Design an elevator system.
- Design a coffee machine.
- Design Amazon Locker.

---

## 4) Copy-paste interviewer prompt

```text
Act as a FAANG-style low-level design interviewer.

Your job is to run a realistic LLD interview from the interviewer’s perspective.

Rules:
1. Start with a brief opening script.
2. Present one LLD problem.
3. Let the candidate drive, but interrupt naturally with probing questions.
4. Do not dump the full solution unless the candidate is completely blocked.
5. Evaluate clarity of requirements, abstractions, ownership, API design, implementation thinking, edge cases, and extensibility.
6. If the candidate codes too early without structure, steer them back.
7. If the candidate invents too much scope, force prioritization.
8. Ask follow-ups around invalid states, encapsulation, tell-don’t-ask, extensibility, concurrency, and trade-offs.
9. At the end, provide feedback in these sections:
   - What went well
   - What was missing
   - Red flags
   - Hiring recommendation

Interview structure to enforce:
- Requirements
- Entities and relationships
- Class design
- Important methods / implementation
- Extensions / follow-ups

Be collaborative but rigorous. Ask concise questions. Reward clean ownership and practical design.
```

---

## 5) Evaluation rubric

Assess the candidate on these dimensions:

### A. Problem framing
- Did they clarify requirements and scope?
- Did they ask about invalid operations and business rules?
- Did they identify what is explicitly out of scope?

### B. Modeling
- Did they identify the right entities?
- Did they assign clear ownership of state and behavior?
- Did they avoid an anemic or over-fragmented design?

### C. API / class design
- Are class responsibilities clear?
- Are methods cohesive and well placed?
- Does the design follow encapsulation and clean boundaries?

### D. Implementation thinking
- Can they walk through major methods?
- Do they handle happy path and edge cases?
- Do they write logic that is understandable and testable?

### E. Extensibility
- Can the design handle a reasonable new requirement?
- Do they know when to introduce interfaces, strategies, or composition?
- Can they discuss trade-offs without overengineering?

---

## 6) Interview flow

## Step 1 — Requirements (~5 minutes)

### What you want from the candidate
They should clarify:
- core capabilities,
- rules and lifecycle,
- invalid actions / error handling,
- what is in scope vs out of scope.

### Good prompts
- “What operations must this system support?”
- “What defines success, failure, or completion?”
- “What should happen for invalid actions?”
- “What is explicitly out of scope?”
- “Do we care about persistence, networking, concurrency, or UI in this round?”

### Strong signals
- Asks focused questions.
- Produces a concise spec.
- Separates core business logic from extras.
- Writes down assumptions before designing.

### Red flags
- Starts coding immediately.
- Never asks about invalid behavior.
- Expands scope endlessly.
- Treats unclear requirements as obvious.

### What a strong interviewer does
If the candidate gets lost in tiny details, narrow the round:

> “Let’s freeze scope to the core logic first. We can add extensions later.”

---

## Step 2 — Entities and relationships (~3 minutes)

### What you want
The candidate should identify the core objects and how they relate.

### Good prompts
- “What are the main entities in this system?”
- “Which class is the orchestrator?”
- “Which objects own durable state?”
- “Which things are real entities versus just fields?”

### Strong signals
- Finds the important nouns quickly.
- Keeps the model small and meaningful.
- Identifies has-a / uses / contains relationships cleanly.
- Places rules near the state they govern.

### Red flags
- Creates too many tiny objects with no value.
- Puts all logic in one god class.
- Splits behavior away from state unnaturally.
- Cannot explain why an entity exists.

---

## Step 3 — Class design (~10–15 minutes)

### What you want
A clear class-level design with state and behavior derived from the requirements.

### Good prompts
- “Walk me through each major class.”
- “What state does this class need to track?”
- “What behaviors belong here?”
- “Why does this logic belong in this class instead of another?”
- “What does the public API of this class look like?”

### Strong signals
- Works top-down from the orchestrator.
- Derives fields and methods from requirements.
- Keeps classes cohesive.
- Uses enums/value objects where useful.
- Applies encapsulation and tell-don’t-ask.

### Red flags
- Class APIs are just getters/setters with no ownership.
- Logic placement is arbitrary.
- Uses inheritance where composition would be cleaner.
- Introduces patterns by name without need.

### What to listen for
Strong candidates naturally answer:
- What must this class remember?
- What must this class do?
- Who is allowed to call this?
- What state transitions are valid?

---

## Step 4 — Core implementation (~10 minutes)

### What you want
The candidate should implement or pseudo-code the important methods that define the behavior of the system.

### Good prompts
- “Which method is the core of the system?”
- “Let’s implement the happy path first.”
- “Now what are the main failure cases?”
- “How would this method update state?”
- “What would you unit test here?”

### Strong signals
- Starts with the key workflow, not trivial helpers.
- Explains happy path before edge cases.
- Handles invalid state transitions.
- Writes logic that matches the design.
- Can reason about correctness.

### Red flags
- Focuses only on syntax.
- Implements helpers without the core workflow.
- Ignores edge cases.
- Writes logic inconsistent with the class design.

### Interviewer note
Unless the company expects full compilable code, pseudo-code is often enough. The goal is to understand the candidate’s design and control flow, not their typing speed.

---

## Step 5 — Extensions / follow-ups (~5–10 minutes)

### What you want
Test whether the design bends without breaking.

### Good prompts
- “Now suppose we support an additional rule / variant. What changes?”
- “How would you add undo/redo?”
- “How would you support different pricing strategies?”
- “How would you add persistence?”
- “How would you make this thread-safe?”
- “How would you support multiple game modes?”

### Strong signals
- Makes localized changes.
- Introduces abstractions only when the extension justifies them.
- Explains trade-offs in complexity vs flexibility.
- Understands where to use interfaces/strategies and where not to.

### Red flags
- Entire design must be rewritten for one new requirement.
- Uses abstraction as decoration.
- Cannot reason about concurrency or invalid states when asked.
- Adds extensibility hooks everywhere “just in case.”

---

## 7) Probe bank by topic

## Ownership and encapsulation
- “Which class owns this rule?”
- “Why should this method live here?”
- “Are callers making decisions that the object should make itself?”
- “Can we reduce the number of getters?”

## State transitions
- “What are the valid states?”
- “What transitions are illegal?”
- “How do you prevent partial updates?”
- “Where is game/system status tracked?”

## Errors and invalid input
- “What happens if this operation is called at the wrong time?”
- “Would you return a boolean, throw an exception, or return a result object?”
- “How do you distinguish business-rule failure from programmer error?”

## Extensibility
- “If requirements change here, what breaks?”
- “Do you need an interface yet, or is one implementation enough?”
- “Would composition be cleaner than inheritance?”
- “How would you support multiple variants?”

## Patterns
Use pattern questions carefully.
Instead of asking “which pattern would you use?”, ask:
- “How would you avoid long if-else chains here?”
- “How would you plug in different behaviors?”
- “How would you isolate construction or policy logic?”

## Concurrency / thread safety
Ask only if relevant.
- “What shared state would be unsafe under concurrency?”
- “What would need locking or synchronization?”
- “Would immutable objects help here?”
- “Could race conditions violate correctness?”

## Testing
- “What are the first 3 unit tests you would write?”
- “What edge case is easiest to miss?”
- “How would you test invalid transitions?”

---

## 8) Seniority calibration

## Mid-level candidate
Expect:
- clean requirements,
- reasonable object model,
- good class ownership,
- working core methods,
- some interviewer help on extensibility or concurrency.

Do not expect:
- framework-level architecture,
- perfect pattern vocabulary,
- advanced concurrency design unless the role demands it.

## Senior candidate
Expect:
- stronger ownership boundaries,
- better trade-offs around abstraction,
- cleaner evolution under requirement changes,
- more awareness of invariants, error modeling, and testing,
- sharper reasoning about concurrency and maintainability.

Push harder on:
- extensibility without overengineering,
- correctness under invalid usage,
- API cleanliness,
- operational concerns if persistence/networking enters scope.

---

## 9) Common interviewer mistakes

Avoid these:
- Forcing UML formality over clarity.
- Rewarding pattern names more than clean design.
- Letting the candidate spend all their time on setup.
- Ignoring implementation completely.
- Turning the round into a language trivia test.
- Penalizing candidates for not matching your exact class structure.

A good interviewer evaluates principles and reasoning, not whether the candidate guessed the same boxes you would draw.

---

## 10) End-of-round feedback template

Use this after the interview.

### What went well
- Candidate clarified requirements and scope well.
- Candidate identified strong ownership boundaries.
- Candidate designed cohesive classes.
- Candidate implemented the core workflow cleanly.
- Candidate handled follow-ups thoughtfully.

### What was missing
- Needed clearer invalid-state handling.
- Class responsibilities were sometimes blurry.
- Too much reliance on getters/setters.
- Implementation did not fully match the design.
- Extensibility discussion was shallow.

### Red flags
- Started coding without a model.
- Could not explain why classes existed.
- Built a god object or anemic model.
- Ignored edge cases.
- Overused patterns without need.
- Struggled to adapt when requirements changed.

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
- Decide 2–3 extension prompts.

### During the round
- Did they clarify the core requirements?
- Did they define invalid behavior?
- Did they mark out-of-scope items?
- Did they identify the right entities?
- Did they define relationships clearly?
- Did they derive fields/methods from requirements?
- Did they assign ownership well?
- Did they implement the core logic?
- Did they handle edge cases?
- Did they adapt to extensions cleanly?

### After the round
- What was strongest?
- What was weakest?
- Was the design complete enough for the level?
- What would change your recommendation up or down?

---

## 12) Short version interviewer command

Use this when you want something compact.

```text
Run a FAANG-style LLD interview.
Evaluate the candidate on requirement clarification, object modeling, ownership, class/API design, core implementation, edge cases, extensibility, and communication.
Keep the flow:
1. Requirements
2. Entities and relationships
3. Class design
4. Core implementation
5. Extensions / follow-ups
Do not give away the answer early. Probe naturally. End with structured feedback and a hiring recommendation.
```
