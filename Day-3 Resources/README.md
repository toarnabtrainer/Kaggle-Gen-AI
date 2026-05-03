# Day 3 Documentation — Context Engineering: Sessions & Memory

> A GitHub-ready study document for **Day 3** of Kaggle’s **5-Day AI Agents Intensive Course with Google**.

> **Scope note:** This document follows the official Day 3 course flow — the Day 3 guide, the *Context Engineering: Sessions & Memory* whitepaper, the companion podcast, and the two codelabs **Day 3a — Agent Sessions** and **Day 3b — Agent Memory**.  
> Where useful, it also maps the course ideas to the current ADK and Vertex AI docs so the concepts stay practical even if some SDK details evolve.

---

## Table of Contents

- [1. Day 3 Goal](#1-day-3-goal)
- [2. What Day 3 Includes](#2-what-day-3-includes)
- [3. Why Day 3 Matters](#3-why-day-3-matters)
- [4. Context Engineering in Plain English](#4-context-engineering-in-plain-english)
- [5. The Core Day 3 Vocabulary](#5-the-core-day-3-vocabulary)
- [6. Day 3a — Agent Sessions](#6-day-3a--agent-sessions)
- [7. What Day 3a Actually Teaches You](#7-what-day-3a-actually-teaches-you)
- [8. How Sessions Work During a Turn](#8-how-sessions-work-during-a-turn)
- [9. In-Memory vs Persistent Sessions](#9-in-memory-vs-persistent-sessions)
- [10. Session State Patterns You Should Learn](#10-session-state-patterns-you-should-learn)
- [11. Safe State Updates and a Critical Warning](#11-safe-state-updates-and-a-critical-warning)
- [12. Day 3b — Agent Memory](#12-day-3b--agent-memory)
- [13. What Day 3b Actually Teaches You](#13-what-day-3b-actually-teaches-you)
- [14. The Manual Memory Flow](#14-the-manual-memory-flow)
- [15. Reactive vs Proactive Memory Retrieval](#15-reactive-vs-proactive-memory-retrieval)
- [16. Automatic Memory Writing with Callbacks](#16-automatic-memory-writing-with-callbacks)
- [17. Memory Consolidation and Vertex AI Memory Bank](#17-memory-consolidation-and-vertex-ai-memory-bank)
- [18. Sessions vs Memory vs RAG](#18-sessions-vs-memory-vs-rag)
- [19. Context Growth: Compression and Caching](#19-context-growth-compression-and-caching)
- [20. Production Lessons Hidden Inside Day 3](#20-production-lessons-hidden-inside-day-3)
- [21. Common Mistakes to Avoid](#21-common-mistakes-to-avoid)
- [22. Day 3 Revision Checklist](#22-day-3-revision-checklist)
- [23. Short Glossary](#23-short-glossary)
- [24. Resources Reviewed](#24-resources-reviewed)

---

## 1. Day 3 Goal

Day 3 is about one big transition:

**from agents that answer within one turn**  
to  
**agents that maintain context across turns and across time**

This is the day where agent systems stop behaving like forgetful chatbots and start behaving like stateful applications.

By the end of Day 3, you should be able to explain:

1. what a **session** is
2. what **events** and **state** are inside a session
3. how to persist sessions and resume prior conversations
4. what **memory** is and why it is different from session history
5. how an agent can **retrieve memory reactively or proactively**
6. how to move from a prototype memory demo to a more production-ready architecture

---

## 2. What Day 3 Includes

Kaggle structures Day 3 around a standard study pattern:

### Concept resources
- the **Day 3 course guide**
- the **“Context Engineering: Sessions & Memory” whitepaper**
- the **summary podcast for Unit 3**

### Hands-on resources
- **Day 3a — Agent Sessions**
- **Day 3b — Agent Memory**

### Reference resources
- ADK documentation for:
  - **Sessions**
  - **State**
  - **Memory**
  - **Runtime Event Loop**
  - **Context Compression**
  - **Context Caching**
- Vertex AI Agent Engine documentation for:
  - **Sessions**
  - **Memory Bank**

### Best study order
1. read the Day 3 overview
2. listen to the summary podcast
3. read the whitepaper
4. run **Day 3a**
5. run **Day 3b**
6. revisit ADK docs and map each notebook idea to the framework components

---

## 3. Why Day 3 Matters

Language models are naturally **stateless**.

If you do nothing beyond sending the current prompt, the model does not truly “remember” prior work. It only sees what you include in the current context window. That leads to three practical problems:

### 3.1 Continuity problem
The agent forgets previous decisions, preferences, or partially completed work.

### 3.2 Cost problem
If you keep pasting the full history back into the prompt, token usage grows, latency grows, and relevance drops.

### 3.3 Product problem
A useful assistant should remember enough to continue a task, personalize its behavior, and avoid asking the same questions again.

Day 3 exists to solve these three problems through **context engineering**, **sessions**, and **memory**.

---

## 4. Context Engineering in Plain English

A simple way to understand Day 3 is:

> **Context engineering is the job of deciding what the model should see at each turn, and what should stay outside the prompt until needed.**

That means the system must decide how to combine:

- the user’s current request
- recent interaction history
- session state
- retrieved memories
- retrieved external knowledge
- tool outputs
- system instructions
- task-specific grounding

The important lesson is that **good agents do not just hold more information**.  
They hold the **right information at the right moment**.

So Day 3 is not only about “adding memory.”  
It is about designing the **information flow** around the model.

---

## 5. The Core Day 3 Vocabulary

Day 3 becomes much easier once these terms are cleanly separated.

### 5.1 Session
A **session** is a single conversation thread or work thread between a user and the agent system.

It represents one ongoing interaction.

### 5.2 Event
An **event** is one recorded interaction inside the session.

Examples:
- a user message
- an agent response
- a tool call
- a tool result
- a state update

### 5.3 State
`session.state` is the session’s **scratchpad**.

It stores dynamic, structured values that help the agent continue the current task.

Examples:
- current booking step
- selected plan
- temporary status flags
- per-user values you want to track inside the ongoing workflow

### 5.4 Memory
**Memory** is searchable information that can be used **across sessions**.

It is not just the raw conversation transcript.  
It is the part of prior interactions that remains useful later.

Examples:
- user preferences
- stable facts
- recurring habits
- important project details
- historical decisions worth recalling later

### 5.5 SessionService
A **SessionService** manages conversation sessions.

It is responsible for:
- creating new sessions
- retrieving existing sessions
- appending events
- updating state
- listing sessions
- deleting sessions

### 5.6 MemoryService
A **MemoryService** manages long-term searchable knowledge.

It is responsible for:
- ingesting sessions into memory
- searching prior memories
- returning relevant memory snippets to the agent

### 5.7 A compact mental model

| Layer | Lifetime | What it stores | Main purpose |
|---|---|---|---|
| `session.events` | current conversation | chronological interaction history | continuity inside a thread |
| `session.state` | current conversation or scoped state | structured task data | keep work in progress |
| `memory` | across conversations | distilled useful knowledge | personalization and recall |

---

## 6. Day 3a — Agent Sessions

Day 3a is the notebook that teaches the **mechanics of conversational continuity**.

At a practical level, this notebook introduces the idea that an agent becomes “stateful” only when you stop treating each request as a brand-new chat and instead attach every turn to a managed session.

The notebook starts simply:
- create a session service
- create a runner
- assign user/session identifiers
- send multiple turns through the same session
- inspect how continuity is preserved

Then it upgrades the example from **temporary in-memory sessions** to **persistent sessions**.

That is the main arc of Day 3a.

---

## 7. What Day 3a Actually Teaches You

The Day 3a notebook teaches four practical ideas.

### 7.1 Sessions are the unit of continuity
The same `session_id` means the same conversation thread.

If you reuse it, the agent can continue prior work.  
If you create a new one, you start a fresh thread.

### 7.2 Session history is not the same as memory
A session stores the detailed thread of interaction.  
It is the raw ongoing work surface, not necessarily the long-term knowledge store.

### 7.3 Session persistence changes product behavior
If sessions are only kept in RAM, continuity disappears when the process resets.  
If sessions are stored in a persistent backend, the conversation can survive restarts and be resumed later.

### 7.4 Session state is a structured scratchpad
The notebook is not only about chat history.  
It is also about **structured, mutable state** that helps the agent keep track of progress.

That is why Day 3a matters even if you are not building a chatbot.  
Any multi-step workflow benefits from session state.

---

## 8. How Sessions Work During a Turn

The ADK runtime makes Day 3 easier to understand if you picture one interaction turn as a pipeline.

### Step 1 — Start or resume a session
Your application creates a new session or fetches an existing one through the `SessionService`.

### Step 2 — Hand the session to the runner
The `Runner` uses the session to give the agent access to:
- the event history
- the current state
- the user identity
- the session identity

### Step 3 — Append the new user input
The incoming message becomes a new event in the session history.

### Step 4 — Agent processes the turn
The agent reasons using:
- the new request
- prior events
- current state
- any tools or callbacks that are configured

### Step 5 — Agent emits events
The agent may emit:
- partial responses
- tool calls
- final responses
- state updates

### Step 6 — Runner commits the updates
The `Runner` appends the resulting events through the `SessionService`.

This matters because state changes become durable only when they are committed through the event flow.

### Step 7 — Future turns reuse the same session
If the next user message uses the same `session_id`, the agent continues where it left off.

### Minimal Day 3a-style setup

```python
from google.adk.sessions import InMemorySessionService
from google.adk.runners import Runner

session_service = InMemorySessionService()

runner = Runner(
    agent=root_agent,
    app_name=APP_NAME,
    session_service=session_service,
)
```

That small pattern is the foundation of the whole notebook.

---

## 9. In-Memory vs Persistent Sessions

One of the best parts of Day 3a is that it shows a progression from local prototype to persistent storage.

### 9.1 InMemorySessionService

Use this when you want:
- fast local experiments
- simple notebook demos
- zero infrastructure setup

Tradeoff:
- all session data is lost when the process stops

This is perfect for learning, but not enough for real users.

### 9.2 DatabaseSessionService

The Day 3a notebook upgrades to a **SQLite-backed `DatabaseSessionService`** to demonstrate persistence without requiring a separate database server.

That teaches an important architectural lesson:

> you do not need cloud-scale infrastructure to understand persistence

You just need a persistent storage layer and stable identifiers.

A typical pattern looks like:

```python
from google.adk.sessions import DatabaseSessionService

db_url = "sqlite+aiosqlite:///./my_agent_data.db"
session_service = DatabaseSessionService(db_url=db_url)
```

### Important practical note

The notebook presents SQLite as the easy persistence upgrade.  
In current ADK Python docs, the database session service expects an **async driver**, so `sqlite+aiosqlite` is the right form in modern usage.

### 9.3 VertexAiSessionService

This is the managed cloud option.

Use it when you want:
- persistent sessions
- scalable cloud-managed storage
- tighter integration with Vertex AI Agent Engine

This is the production-oriented path, while SQLite is the simplest persistence demo path.

### 9.4 How to think about the three options

| Session service | Best use case | Persistence | Complexity |
|---|---|---|---|
| `InMemorySessionService` | demos, notebooks, local testing | none | very low |
| `DatabaseSessionService` | self-managed durable storage | yes | medium |
| `VertexAiSessionService` | managed cloud production | yes | higher, but operationally cleaner at scale |

---

## 10. Session State Patterns You Should Learn

Day 3a becomes much more useful when you understand what belongs in `session.state`.

### 10.1 What state is good for

State is ideal for information like:

- current workflow step
- selected options
- flags that affect the next action
- summaries of current progress
- values that should be reused later in the same task

Examples:
- `current_booking_step = "confirm_payment"`
- `selected_currency = "EUR"`
- `needs_human_review = True`
- `user_goal = "plan 4-day trip"`

### 10.2 What state is **not** for

State is not the place for:
- huge transcripts
- arbitrary unstructured documents
- non-serializable Python objects
- long-lived knowledge that should outlive the session
- everything the user has ever said

If the information should survive beyond the current work thread, it may belong in **memory**, not only in session state.

### 10.3 State prefixes in ADK

Current ADK docs add an extremely useful nuance: key prefixes define scope.

#### No prefix
Session-local state.

Example:
```python
session.state["current_intent"] = "book_flight"
```

Use for:
- task progress
- local interaction flags
- current turn decisions

#### `user:`
Shared across all sessions for the same user in the same app.

Example:
```python
session.state["user:preferred_language"] = "fr"
```

Use for:
- per-user preferences
- stable user-level values

#### `app:`
Shared across the whole application.

Example:
```python
session.state["app:global_discount_code"] = "SAVE10"
```

Use for:
- app-wide values
- shared configuration-like state

#### `temp:`
Only for the current invocation.

Example:
```python
session.state["temp:raw_api_response"] = {...}
```

Use for:
- intermediate values inside a single run
- data passed between tool calls in one invocation

Do **not** use `temp:` for anything that must survive to the next turn.

### 10.4 State injection into instructions

A useful Day 3 idea from ADK is that state can be injected into instructions with `{key}` templating.

Example:

```python
story_agent = LlmAgent(
    name="StoryGenerator",
    model="gemini-flash-latest",
    instruction="Write a short story about a cat, focusing on the theme: {topic}.",
)
```

If `session.state["topic"] = "friendship"`, the instruction becomes more specific automatically.

That is a clean way to make instructions dynamic without turning everything into loose natural-language prompting.

### 10.5 `output_key`

If an agent’s final text output should be saved into state, `output_key` is a very convenient pattern.

Example idea:
```python
greeting_agent = LlmAgent(
    name="Greeter",
    model="gemini-flash-latest",
    instruction="Generate a short greeting.",
    output_key="last_greeting",
)
```

This is especially useful in multi-step workflows where one step’s result becomes the next step’s input.

---

## 11. Safe State Updates and a Critical Warning

This is one of the most important Day 3 details.

### The right way
Update state through:
- `output_key`
- callback context
- tool context
- event-driven session updates handled by the `Runner`

### The wrong way
Do **not** fetch a session object from the session service and then directly mutate its `state` outside the managed invocation flow.

Bad pattern:

```python
retrieved_session = await session_service.get_session(...)
retrieved_session.state["key"] = value   # avoid this
```

Why this is dangerous:
- the change may bypass event history
- persistence can fail
- timestamps may not update
- thread safety can break
- you lose an auditable state transition

### The practical rule
Treat `SessionService` as the source of truth, and treat the event loop as the official way changes become durable.

If you are inside a callback or tool, modify state through the provided context.  
If you want an agent response saved automatically, use `output_key`.

That is the reliable path.

---

## 12. Day 3b — Agent Memory

If Day 3a teaches the workbench, Day 3b teaches the filing cabinet.

Day 3b moves from:
- preserving an ongoing conversation

to:
- extracting durable knowledge from conversations and reusing it later

The core shift is:

> **session history preserves a thread**  
> **memory preserves what is still worth knowing after the thread ends**

This is where agent systems stop being “stateful chat logs” and start becoming personalized systems.

---

## 13. What Day 3b Actually Teaches You

The Day 3b notebook teaches five practical ideas.

### 13.1 Initialize and attach a memory service
A memory-enabled agent is not just an LLM with chat history.  
It needs a `MemoryService`.

### 13.2 Move session information into long-term memory
The notebook first shows a **manual** pattern:
- run a session
- call `add_session_to_memory`
- search that stored information later

This is a good teaching move because it exposes the mechanism.

### 13.3 Search memory later
A future interaction can use memory search to recall relevant facts.

### 13.4 Compare reactive and proactive retrieval
The notebook distinguishes:
- **LoadMemory**: retrieve when the agent decides it needs memory
- **PreloadMemory**: retrieve at the start of every turn

This is a major design choice.

### 13.5 Automate memory writing
The notebook makes an explicit production point:

manual calls to `add_session_to_memory()` are educational, but real systems need memory writing to happen automatically.

That is where callbacks enter the picture.

---

## 14. The Manual Memory Flow

The manual Day 3b flow is worth understanding clearly before moving to automation.

### Step 1 — Create a memory service

```python
from google.adk.memory import InMemoryMemoryService

memory_service = InMemoryMemoryService()
```

### Step 2 — Use a session service too
Sessions and memory work together, not separately.

```python
from google.adk.sessions import InMemorySessionService

session_service = InMemorySessionService()
```

### Step 3 — Run a first session and capture information
For example, the user says something like:
- favorite project
- food preference
- travel style
- recurring task detail

### Step 4 — Add that session to memory

```python
await memory_service.add_session_to_memory(session)
```

This is the explicit ingestion step.

### Step 5 — Start another session for the same user
Now the user asks a question that depends on prior context.

Example:
- “What is my favorite project?”
- “What kind of trip do I prefer?”
- “What language do I usually ask for?”

### Step 6 — Let the agent retrieve memory
If the agent has a memory-retrieval tool, it can search prior memory and answer with context.

### Why this demo matters

The manual flow teaches you the exact architecture:

1. conversation produces events
2. events are ingested into memory
3. memory becomes searchable
4. later turns retrieve the relevant pieces
5. the agent uses them to answer better

This is the core of long-term personalization.

### A crucial Day 3b implementation detail

The examples share the same services across runners:

- same `SessionService`
- same `MemoryService`

That matters because otherwise the second runner would not see the same session or memory state.

---

## 15. Reactive vs Proactive Memory Retrieval

This is one of the most useful conceptual distinctions in the whole course.

## 15.1 Reactive retrieval — `LoadMemory`

Reactive retrieval means:

> the agent decides when memory is needed

Pattern:

```python
from google.adk.tools import load_memory

agent = LlmAgent(
    ...,
    tools=[load_memory],
)
```

### Strengths
- cheaper in many cases
- more selective
- good when only some turns require recall
- keeps context smaller when memory is not relevant

### Risks
- the agent might fail to call the tool
- important memory may be missed
- behavior depends on the model’s decision process

### Best for
- optional recall
- targeted lookup
- agents that only occasionally need past context

---

## 15.2 Proactive retrieval — `PreloadMemory`

Proactive retrieval means:

> memory is loaded at the start of every turn

Pattern:

```python
from google.adk.tools.preload_memory_tool import PreloadMemoryTool

agent = Agent(
    ...,
    tools=[PreloadMemoryTool()],
)
```

### Strengths
- more reliable when memory is consistently important
- easier to reason about
- good for strong personalization or mandatory context

### Tradeoffs
- can add latency
- can add unnecessary context on turns where memory is not relevant
- can increase token usage if not designed carefully

### Best for
- always-important user preferences
- safety-critical remembered facts
- applications where recall should not depend on the model choosing a tool

---

## 15.3 A simple design rule

Use **LoadMemory** when:
- recall is conditional
- you want a more selective, tool-like interaction
- cost and compactness matter

Use **PreloadMemory** when:
- recall should happen every turn
- missing a memory is worse than paying some extra context cost
- personalization is central to the product

---

## 16. Automatic Memory Writing with Callbacks

The course does something important here: it moves beyond the manual demo.

A real agent system should not depend on a developer remembering to call `add_session_to_memory()` by hand after each useful interaction.

Instead, the notebook introduces a callback pattern.

### Example idea

```python
from google import adk

async def auto_save_session_to_memory_callback(callback_context):
    await callback_context._invocation_context.memory_service.add_session_to_memory(
        callback_context._invocation_context.session
    )
```

Then attach it to the agent:

```python
agent = Agent(
    model=MODEL,
    name="Generic_QA_Agent",
    instruction="Answer the user's questions",
    tools=[adk.tools.preload_memory_tool.PreloadMemoryTool()],
    after_agent_callback=auto_save_session_to_memory_callback,
)
```

### What this pattern gives you

- **automatic write-back** after an interaction
- **automatic recall** at the start of the next interaction
- a reusable memory loop rather than a one-off demo

### Why this matters

This pattern is close to the real architecture you want in production:

1. user interacts
2. agent answers
3. session is ingested into memory automatically
4. future turns recall memory automatically or when needed

That is the beginning of a stable memory pipeline.

---

## 17. Memory Consolidation and Vertex AI Memory Bank

This is where Day 3b becomes more than “search old chats.”

### 17.1 In-memory memory is for prototyping
`InMemoryMemoryService` is useful because it requires no setup.

But it has real limits:
- no persistence across restarts
- basic keyword matching
- less realistic retrieval behavior
- not a production memory layer

### 17.2 Vertex AI Memory Bank is the production-oriented model
`VertexAiMemoryBankService` is the managed option.

Its strengths are:
- persistence
- semantic retrieval
- identity-scoped memory
- intelligent extraction from sessions
- consolidation of memory across time

### 17.3 Why “consolidation” matters

The Day 3b notebook explicitly teaches a memory consolidation idea:

**raw session events are not the final memory format**.

Instead, a memory pipeline should:
1. inspect the conversation
2. identify meaningful facts
3. discard noise
4. store concise memories
5. merge or consolidate with existing memory for the same scope

This is a much better design than storing every utterance forever.

### 17.4 Conceptual consolidation pipeline

A clean summary of the pipeline is:

1. raw session events
2. LLM analyzes the conversation
3. key facts are extracted
4. concise memories are stored
5. future search retrieves those memories

That is the essence of Day 3b.

### 17.5 Scope is critical

In Memory Bank, generated memories are scoped.  
A common default is scoping by `user_id`.

This is important because it prevents cross-user leakage and keeps personalization attached to the right identity.

### 17.6 You can also provide pre-extracted memories

Memory Bank supports not only automatic extraction from sessions, but also direct memory inputs for consolidation.

That means a human or an upstream agent can decide what the durable memory should be, and Memory Bank can still help merge it with existing knowledge.

### 17.7 How to choose between the two memory services

| Memory service | Best use case | Retrieval style | Persistence | Extraction quality |
|---|---|---|---|---|
| `InMemoryMemoryService` | demos, learning, local experiments | keyword search | none | minimal |
| `VertexAiMemoryBankService` | production personalization | semantic search | yes | LLM-driven extraction and consolidation |

---

## 18. Sessions vs Memory vs RAG

This distinction is absolutely worth mastering.

### 18.1 Sessions
Sessions hold the active conversation thread.

Use sessions for:
- current interaction continuity
- in-progress task state
- turn-by-turn event history

### 18.2 Memory
Memory holds personalized, cross-session knowledge.

Use memory for:
- recurring preferences
- stable facts about a user or account
- learned context from earlier interactions
- durable knowledge distilled from prior sessions

### 18.3 RAG
RAG retrieves external knowledge from a knowledge base.

Use RAG for:
- product documentation
- policies
- manuals
- public or organizational reference content
- large shared corpora

### 18.4 The clean separation

| Mechanism | Main question it answers |
|---|---|
| Session | “What is happening in this conversation right now?” |
| Memory | “What do we already know about this user or situation from before?” |
| RAG | “What external knowledge should we fetch for this task?” |

### 18.5 The key takeaway

Do not confuse memory with RAG.

- **RAG** gives general or shared knowledge
- **memory** gives personalized or interaction-derived knowledge
- **sessions** give current thread continuity

Strong agents use all three, but for different jobs.

---

## 19. Context Growth: Compression and Caching

A subtle but essential Day 3 lesson is that continuity has a cost.

If you keep every event in raw form and keep loading everything into every prompt, the system becomes:
- slower
- more expensive
- less relevant
- harder to control

That is why context engineering is not “save everything.”  
It is “save the right things, compress the rest, retrieve only what matters.”

### 19.1 Context compression
ADK supports context compression by summarizing older workflow events.

This is useful when:
- a session grows long
- older details matter only as summary
- you need continuity without unbounded token growth

The feature uses a sliding-window compaction approach and can be configured with compaction intervals and overlap settings.

### 19.2 Context caching
If you repeatedly send large stable instructions or large shared data, ADK can use context caching with supported Gemini models to reduce repeated token cost and improve responsiveness.

### 19.3 The Day 3 architectural lesson

The winning pattern is not:
- full raw history
- plus full memory
- plus full retrieved documents
- plus all tool outputs
- every turn

The winning pattern is:
- keep the active thread structured
- summarize stale details
- retrieve memory only when useful
- cache expensive repeated context
- assemble only what the current turn needs

That is what “context engineering” really means in practice.

---

## 20. Production Lessons Hidden Inside Day 3

Day 3 contains several production lessons even though the notebooks start simple.

### 20.1 Identifiers are architecture
`app_name`, `user_id`, and `session_id` are not just bookkeeping.

They determine:
- which conversation is resumed
- whose memory is searched
- what scope state belongs to
- how isolation is enforced

### 20.2 Storage choice changes user experience
Temporary session storage produces “forgetful” behavior after restarts.  
Persistent storage produces “continuous” behavior.

### 20.3 Not all history deserves memory
If you dump every message into long-term memory, your system gets noisy and expensive.

Memory should focus on **signal**, not raw volume.

### 20.4 Retrieval policy is a product decision
Choosing between `LoadMemory` and `PreloadMemory` is not only technical.

It affects:
- latency
- reliability
- personalization quality
- cost
- explainability

### 20.5 State should stay small and structured
Session state is most valuable when it is:
- serializable
- interpretable
- compact
- tied to decision-making

### 20.6 Long-term memory needs governance
As soon as memory becomes durable and user-specific, you must think about:
- correct scoping
- privacy boundaries
- update paths
- deletion paths
- conflicting memories
- stale memories

Day 3 plants the seed for these production issues even before Day 4 and Day 5 formalize them further.

---

## 21. Common Mistakes to Avoid

### Mistake 1 — Treating sessions and memory as the same thing
They are related, but not the same.

### Mistake 2 — Storing everything in memory
Memory should hold important, reusable knowledge, not every sentence ever spoken.

### Mistake 3 — Only using in-memory services and assuming that is enough
Great for demos, not enough for durable user experience.

### Mistake 4 — Directly mutating `session.state` on a retrieved session object
This can bypass the event system and persistence flow.

### Mistake 5 — Using `LoadMemory` when memory is mandatory every turn
If recall is required, use a proactive strategy.

### Mistake 6 — Using `PreloadMemory` for everything
If memory is rarely needed, proactive loading can be wasteful.

### Mistake 7 — Putting non-serializable objects into state
Keep state simple, structured, and durable.

### Mistake 8 — Forgetting to share services across demo runners
If the services are not shared, your second runner will not see prior session or memory data.

### Mistake 9 — Thinking memory replaces RAG
Memory is personalized and evolving; RAG is external knowledge retrieval.

### Mistake 10 — Ignoring context growth
Longer history without compression or selective retrieval eventually becomes a performance problem.

---

## 22. Day 3 Revision Checklist

You understood Day 3 well if you can explain all of the following without looking at the notebook:

- what a `Session` is
- what an `Event` is
- what `session.state` is for
- how `SessionService` differs from `MemoryService`
- why `InMemorySessionService` is only temporary
- why `DatabaseSessionService` or `VertexAiSessionService` changes the product experience
- how `add_session_to_memory()` works conceptually
- what `load_memory` does
- what `PreloadMemoryTool` does
- why callbacks help automate memory writing
- why memory should be consolidated rather than copied raw
- how memory differs from RAG
- why context compression matters
- why direct mutation of retrieved session state is risky

---

## 23. Short Glossary

**Session**  
One ongoing conversation or work thread.

**Event**  
One recorded interaction inside a session.

**State**  
Structured scratchpad data used during or around the current conversation.

**SessionService**  
Component that creates, stores, resumes, updates, lists, and deletes sessions.

**Memory**  
Searchable information that persists across sessions.

**MemoryService**  
Component that ingests sessions into memory and retrieves relevant memories later.

**LoadMemory**  
Reactive memory retrieval tool used when the agent decides memory is needed.

**PreloadMemory**  
Proactive memory retrieval tool that loads memory at the start of a turn.

**Consolidation**  
The process of turning noisy raw interaction history into cleaner, durable, useful memories.

**RAG**  
Retrieval-Augmented Generation; fetching external knowledge from a document or knowledge store.

**Context Compression**  
Summarizing older session content so context stays manageable.

**Context Caching**  
Reusing stable large context across requests to reduce repeated token cost.

---

## 24. Resources Reviewed

### Official course resources
- [Kaggle — 5-Day AI Agents Intensive Course with Google](https://www.kaggle.com/learn-guide/5-day-agents)
- [Kaggle — Context Engineering: Sessions & Memory](https://www.kaggle.com/whitepaper-context-engineering-sessions-and-memory)
- [Kaggle Notebook — Day 3a: Agent Sessions](https://www.kaggle.com/code/kaggle5daysofai/day-3a-agent-sessions)
- [Kaggle Notebook — Day 3b: Agent Memory](https://www.kaggle.com/code/kaggle5daysofai/day-3b-agent-memory)

### ADK documentation
- [ADK — Sessions & Memory Overview](https://adk.dev/sessions/)
- [ADK — Session](https://adk.dev/sessions/session/)
- [ADK — State](https://adk.dev/sessions/state/)
- [ADK — Memory](https://adk.dev/sessions/memory/)
- [ADK — Runtime Event Loop](https://adk.dev/runtime/event-loop/)
- [ADK — Context Compression](https://adk.dev/context/compaction/)
- [ADK — Context Caching](https://adk.dev/context/caching/)
- [ADK — LLM Agents](https://adk.dev/agents/llm-agents/)

### Vertex AI documentation
- [Vertex AI Agent Engine — Overview](https://docs.cloud.google.com/agent-builder/agent-engine/overview)
- [Vertex AI Agent Engine — Sessions Overview](https://docs.cloud.google.com/agent-builder/agent-engine/sessions/overview)
- [Vertex AI Agent Engine — Memory Bank Overview](https://docs.cloud.google.com/agent-builder/agent-engine/memory-bank/overview)
- [Vertex AI Agent Engine — Set up Memory Bank](https://docs.cloud.google.com/agent-builder/agent-engine/memory-bank/set-up)
- [Vertex AI Agent Engine — Generate Memories](https://docs.cloud.google.com/agent-builder/agent-engine/memory-bank/generate-memories)
- [Vertex AI Agent Engine — Fetch Memories](https://docs.cloud.google.com/agent-builder/agent-engine/memory-bank/fetch-memories)

---

## Final Takeaway

Day 3 teaches the real architecture behind useful agent systems.

- **Sessions** keep the current thread coherent.
- **State** keeps the current task organized.
- **Memory** lets the system retain useful knowledge across time.
- **Compression and retrieval policy** keep the whole thing efficient.

If Day 1 teaches what an agent is, and Day 2 teaches how it acts, then Day 3 teaches how it **remains coherent over time**.

That is the step that turns an LLM demo into a stateful agent application.
