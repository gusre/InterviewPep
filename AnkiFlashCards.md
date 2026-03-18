Task:
Your task is to analyze the source text and convert it into high-quality Anki flashcards focused on software engineering concepts, especially:

- HLD (High-Level Design): architecture, components, services, APIs, scalability, reliability, trade-offs, storage choices, caching, queues, load balancing, availability, consistency, security.
- LLD (Low-Level Design): classes, interfaces, objects, design patterns, method responsibilities, relationships, data flow, validation, error handling, modularity, extensibility.

Create flashcards in a hybrid format:
- Use **Cloze** cards for short facts, responsibilities, definitions, direct trade-offs, and compact design statements.
- Use **Question–Answer** cards for explanations, reasoning, complexity analysis, trade-offs that need context, and “why” questions.

Your goal is to produce concise, interview-friendly flashcards that are clear, standalone, and easy to review.

Instructions:
1. Rewrite the source text in clear and concise language while preserving the original meaning.
2. Split the rewritten content into small sections, where each section covers exactly one important engineering idea.
3. For each section, choose the better card type:
   - **Cloze** if the idea can be tested as a short standalone statement.
   - **Q/A** if the idea needs explanation or reasoning.
4. Prefer cards that test:
   - what a component does
   - why a design choice was made
   - when to use a pattern/component
   - trade-offs between alternatives
   - responsibilities of modules/classes/interfaces
   - scaling, reliability, and maintainability decisions
   - time complexity, space complexity, and performance reasoning when present
5. Avoid vague, overly broad, or duplicate cards.
6. If the text contains examples, extract the underlying engineering principle, not just the example details.
7. Keep the cards concise and exam/interview-friendly.
8. Do not invent facts not present in the source text.

Card Selection Rules:
- Use **Cloze** when the answer is a short phrase or keyword and the statement remains meaningful on its own.
- Use **Q/A** when the answer needs a sentence, reasoning, comparison, or complexity explanation.
- Prefer **Q/A** for:
  - time complexity
  - space complexity
  - “why” design decisions
  - trade-off discussions
  - multi-step reasoning
- Prefer **Cloze** for:
  - component responsibilities
  - design pattern purpose
  - API/module purpose
  - one-line architecture facts
  - direct LLD/HLD terminology

Formatting Criteria:
- Construct a table with 2 columns: "Front", "Back/Notes".

Rules for Cloze cards:
- Put exactly one Anki cloze statement in the "Front" column.
- Put a plain-language explanation in the "Back/Notes" column.
- Restrict each statement to 1–2 cloze deletions.
- Use only `c1` and `c2`.
- Keep each cloze statement under 40 words.
- Keep each cloze deletion to one or two key words only.
- Every statement must stand alone and include its subject.

Rules for Q/A cards:
- Put the question in the "Front" column.
- Put the answer in the "Back/Notes" column.
- Each question must test exactly one concept.
- Each answer must directly answer the question in plain language.
- Default to concise answers, but allow 40+ words when needed for reasoning or complexity.

Good card rules:
- Test one engineering idea at a time.
- Prefer precise software engineering and system design language.
- Make trade-offs explicit where relevant.
- Include reasoning when useful, especially for scalability, reliability, maintainability, or complexity.
- Use terms like service, cache, queue, replica, partition, interface, class, dependency, consistency, latency, throughput, retry, idempotency, sharding, indexing, and abstraction only when supported by the source text.

Output Format:
| Front | Back/Notes |
| --- | --- |
| A {{c1::load balancer}} distributes traffic across {{c2::servers}}. | It improves availability and prevents one server from becoming overloaded. |
| Why is a queue used between services? | A queue decouples producers and consumers, smooths traffic spikes, and supports retries or asynchronous processing. |

