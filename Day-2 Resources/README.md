# Day 2 Documentation — Agent Tools & Interoperability with MCP

> A GitHub-ready study document for **Day 2** of Kaggle’s **5-Day AI Agents Intensive Course with Google**.

---

## Table of Contents

- [1. Day 2 Goal](#1-day-2-goal)
- [2. What Day 2 Includes](#2-what-day-2-includes)
- [3. Why Tools Matter in Agentic Systems](#3-why-tools-matter-in-agentic-systems)
- [4. The Core Tooling Model You Should Learn on Day 2](#4-the-core-tooling-model-you-should-learn-on-day-2)
- [5. Tool Design Principles from the Whitepaper](#5-tool-design-principles-from-the-whitepaper)
- [6. Day 2a — Agent Tools](#6-day-2a--agent-tools)
- [7. What Day 2a Actually Teaches You](#7-what-day-2a-actually-teaches-you)
- [8. The Currency Agent Pattern](#8-the-currency-agent-pattern)
- [9. Why the Course Adds a Calculation Agent](#9-why-the-course-adds-a-calculation-agent)
- [10. Agent Tools vs Sub-Agents](#10-agent-tools-vs-sub-agents)
- [11. Day 2 Tool Taxonomy in ADK](#11-day-2-tool-taxonomy-in-adk)
- [12. Day 2b — Tool Patterns, MCP, and Long-Running Operations](#12-day-2b--tool-patterns-mcp-and-long-running-operations)
- [13. MCP Explained Clearly](#13-mcp-explained-clearly)
- [14. MCP in ADK](#14-mcp-in-adk)
- [15. Example MCP Servers Used or Mentioned on Day 2](#15-example-mcp-servers-used-or-mentioned-on-day-2)
- [16. Long-Running Operations and Human Approval](#16-long-running-operations-and-human-approval)
- [17. Resume and Invocation IDs](#17-resume-and-invocation-ids)
- [18. Security and Enterprise Readiness Themes](#18-security-and-enterprise-readiness-themes)
- [19. Best Practices You Should Carry Forward](#19-best-practices-you-should-carry-forward)
- [20. Common Mistakes to Avoid](#20-common-mistakes-to-avoid)
- [21. Day 2 Revision Checklist](#21-day-2-revision-checklist)
- [22. Short Glossary](#22-short-glossary)
- [23. Resources Reviewed](#23-resources-reviewed)

---

## 1. Day 2 Goal

Day 2 is about one big transition:

**from agents that can answer**  
to  
**agents that can act**

Day 1 introduced agents, orchestration, and workflow patterns.  
Day 2 focuses on the mechanism that makes those systems useful in the real world:

- **tools**
- **external integrations**
- **interoperability**
- **human approval**
- **resumable execution**

By the end of Day 2, you should be able to explain:

1. why agents need tools
2. how ADK turns Python functions into callable tools
3. when to use an **AgentTool** instead of direct LLM reasoning
4. what **MCP** is and why it matters
5. how a workflow can **pause**, wait for approval, and **resume safely**
6. what kinds of security risks appear once agents gain access to outside systems

---

## 2. What Day 2 Includes

Kaggle structures Day 2 around three layers of material:

### Concept resources
- the **Day 2 course guide**
- the **“Agent Tools & Interoperability with MCP” whitepaper**
- the **summary podcast for Unit 2**

### Hands-on resources
- **Day 2a — Agent Tools**
- **Day 2b — Agent Tools Best Practices**

### Reference resources
- **ADK Function Tools documentation**
- **ADK MCP Tools documentation**
- **ADK confirmation / resume documentation**
- **official MCP example server references**

A strong study order is:

1. read the Day 2 overview
2. go through the whitepaper
3. run **Day 2a**
4. run **Day 2b**
5. revisit the docs to map the ideas to actual ADK components

---

## 3. Why Tools Matter in Agentic Systems

A language model alone is limited.

It can generate text from its training and current prompt, but it cannot reliably:

- access live business data
- fetch current information
- perform privileged actions
- interact with enterprise systems
- execute exact deterministic logic every time

That is why the whitepaper defines a tool as something an LLM-based application uses **outside the model’s native capabilities**.

A useful shortcut for remembering Day 2:

- a model can **reason**
- a tool lets the system **know something new** or **do something external**

This “know or do” distinction is one of the core Day 2 ideas.

### Examples

| Need | Tool role |
|---|---|
| Current exchange rate | lets the model **know** something |
| Payment fee lookup | lets the model **know** something |
| Code execution for exact math | lets the model **do** deterministic computation |
| Place shipping order | lets the model **do** something in an external process |
| Generate an image from an MCP server | lets the model **use an external capability** |

The key architectural point is that the model should **decide when to use a tool**, but the tool itself should provide the real capability.

---

## 4. The Core Tooling Model You Should Learn on Day 2

Day 2 repeatedly reinforces this mental model:

### A tool is a contract

A tool is not just “some function in your codebase.”

For the model, a tool is an interface with:

- a **name**
- a **description**
- **parameters**
- an expected **output shape**

That is why function signatures, docstrings, and type hints matter so much.

### The LLM should not guess what the tool does

The more clearly you define the tool, the better the model can decide:

- when to call it
- how to populate arguments
- how to interpret the result
- when to recover from failure

### Good tooling is not only about capability

It is also about:

- **reliability**
- **clarity**
- **control**
- **security**
- **observability**

That is why Day 2b moves from “how to add tools” to “how to use them safely and correctly.”

---

## 5. Tool Design Principles from the Whitepaper

The whitepaper is not just a definition document.  
It is mainly trying to teach **how to design tools so agents can use them well**.

### 5.1 Describe actions, not implementation details

Tool descriptions should explain **what the tool accomplishes** and **when it should be used**, not the internal code path or API plumbing.

Good:
- “Look up the fee percentage for a payment method”
- “Place a shipping order after confirmation”
- “Fetch an image from the MCP server”

Less useful:
- “Call endpoint X with JSON body Y”
- “Use internal helper Z before request W”
- “Invoke SDK class A and parse field B”

The model needs decision-making guidance, not low-level developer internals.

### 5.2 Keep tool outputs structured

The notebooks repeatedly use dictionary outputs with fields like:

- `status`
- `rate`
- `fee_percentage`
- `error_message`
- `order_id`

That is not accidental.

Structured returns make it easier for the model to:

- detect success vs failure
- branch to the next step
- explain results clearly
- recover from bad inputs

### 5.3 Keep outputs concise

A powerful Day 2 point is that tools should not return huge blobs of unnecessary data.

Overly large responses can:

- make the context window noisy
- make follow-up reasoning worse
- increase latency
- increase token cost

Return what the model actually needs for the next decision.

### 5.4 Use strong schemas

Type hints and parameter descriptions improve tool use because they become part of the tool schema the model sees.

Examples:
- `method: str`
- `base_currency: str`
- `target_currency: str`
- `num_containers: int`

### 5.5 Separate reasoning from deterministic execution

If the task requires exact arithmetic, code execution, or strict business logic, do not force the LLM to “mentally simulate” the answer.  
Use a tool or executor.

This principle appears directly in Day 2a when the course moves currency math into a specialist calculation agent backed by a code executor.

### 5.6 Gate sensitive actions

Once a tool can do something privileged, irreversible, costly, or security-sensitive, you should not treat it like a harmless read-only function.

Typical gating patterns include:

- confirmation
- approval thresholds
- read-only mode
- least privilege
- input validation
- tool filtering
- audit logging

That mindset becomes the center of Day 2b.

---

## 6. Day 2a — Agent Tools

Day 2a is the “learn the mechanics” notebook.

Its purpose is to show that tools are not abstract theory.  
They are how you extend an ADK agent with real capabilities.

### The notebook teaches you to:

- turn Python functions into tools
- attach those tools to an ADK `LlmAgent`
- create a **multi-tool agent**
- build a specialist agent and expose it through **`AgentTool`**
- understand the difference between custom and built-in tools

The notebook uses a **currency conversion assistant** as the main example.

---

## 7. What Day 2a Actually Teaches You

The notebook teaches four practical patterns.

### 7.1 Pattern 1 — Function tools

A regular Python function can become a tool when you add it to an agent’s `tools` list.

The ADK framework inspects:

- the function name
- the docstring
- the parameters
- type hints
- default values

and turns that into a schema the LLM can call.

This is the fastest way to connect custom business logic to an agent.

### 7.2 Pattern 2 — Multi-tool reasoning

The agent does not solve the whole task with one tool.

Instead, it chains multiple tool calls:

1. get fee information
2. get exchange rate information
3. calculate the final value
4. explain the answer

This is an important Day 2 design pattern:

> tools often supply partial facts  
> the agent composes them into a final answer

### 7.3 Pattern 3 — Specialization through AgentTool

When one part of the problem needs very different behavior, the course creates a dedicated specialist agent.

That specialist becomes callable by the root agent through **`AgentTool`**.

This is a strong pattern because it lets you keep responsibilities separate.

### 7.4 Pattern 4 — Built-in execution for reliability

The course shows that pure LLM arithmetic is not reliable enough for exact calculation.  
So it uses a **built-in code executor** through a separate calculation agent.

This is a major lesson:

- LLM = planner / interpreter / explainer
- code execution = exact computation
- combine them instead of forcing the model to do everything alone

---

## 8. The Currency Agent Pattern

The Day 2a example is simple but very instructive.

### Step 1: Fee lookup tool

A custom function returns the fee percentage for a payment method.

This is mock business logic, but it mirrors a real company pattern:

- internal fee tables
- internal policy logic
- company-specific naming
- structured success or error output

### Step 2: Exchange-rate tool

A second function returns the exchange rate between two currencies.

Again, it is mocked in the notebook, but it stands in for real integrations like:

- forex APIs
- pricing services
- treasury systems
- internal finance engines

### Step 3: Root currency agent

The `currency_agent` is instructed to:

1. use the fee tool
2. use the exchange-rate tool
3. inspect the returned `status`
4. compute the final answer
5. explain the result clearly

This teaches two important habits:

- always check tool output before trusting it
- tell the model exactly how to react to failure states

### Step 4: Structured errors

The functions return values like:

- success with useful fields
- error with an `error_message`

That matters because it trains the agent to behave like an application instead of a chatbot guessing its way out of problems.

---

## 9. Why the Course Adds a Calculation Agent

The Day 2a notebook then improves the design.

### The problem

The currency agent can reason about the process, but the final arithmetic is still vulnerable to normal LLM weaknesses:

- arithmetic slips
- rounding inconsistencies
- formula drift
- non-deterministic outputs

### The solution

The notebook creates a **`CalculationAgent`** that only emits Python code and runs it with ADK’s **`BuiltInCodeExecutor`**.

Then the root agent calls that specialist through **`AgentTool`**.

### Why this matters

This is one of the best practical patterns in the whole course:

- use one agent to interpret the task
- use another component to execute deterministic logic
- keep the main agent in control of the conversation

It is a clean example of **delegation without losing orchestration**.

---

## 10. Agent Tools vs Sub-Agents

This distinction is easy to miss, so Day 2a explicitly explains it.

### AgentTool

With an AgentTool:

- Agent A calls Agent B like a tool
- Agent B returns its result to Agent A
- Agent A stays in control of the overall conversation

This is good for:
- calculations
- classification
- one specialized internal step
- reusable internal capabilities

### Sub-agent handoff

With a sub-agent pattern:

- control shifts from one agent to another
- the specialist becomes the active controller
- the original agent no longer manages the conversation in the same way

This is better for:
- support escalations
- deep handoff to a domain specialist
- workflows where ownership genuinely changes

### Day 2 takeaway

Use **AgentTool** for **delegation**.  
Use **sub-agents** for **handoff**.

The currency example is delegation, not handoff.

---

## 11. Day 2 Tool Taxonomy in ADK

Day 2a ends by widening the lens and showing the main tool categories.

### 11.1 Custom tools

These are tools you define yourself.

#### Function tools
The simplest pattern.  
A Python function becomes a tool.

#### Long-running function tools
Used when execution may pause or continue over time.

#### Agent tools
An agent becomes callable as a tool by another agent.

#### MCP tools
ADK tools backed by external MCP servers.

#### OpenAPI tools
Tools generated from API specifications.

### 11.2 Built-in tools

These are capabilities available through ADK and related integrations.

Examples highlighted in the notebook include:

- **Google Search**
- **BuiltInCodeExecutor**
- Google Cloud toolsets like BigQuery / Spanner / API Hub
- third-party integrations such as GitHub or Hugging Face

### What you should remember

Custom tools are how you expose **your own logic**.  
Built-in tools are how you adopt **existing capabilities quickly**.

Both are useful.  
The right choice depends on whether the capability is unique to your system or already well-supported.

---

## 12. Day 2b — Tool Patterns, MCP, and Long-Running Operations

Day 2b is where the course becomes more operational.

Instead of asking only “how do I add a tool?”, it asks:

- how do I connect to tool ecosystems I did not build?
- how do I work with tools that are not instant?
- how do I pause a workflow for human approval?
- how do I resume safely without losing state?

This notebook has two main themes:

1. **Model Context Protocol (MCP)**
2. **Long-running operations with resumability**

These are very important because they move you from toy demos toward production-grade patterns.

---

## 13. MCP Explained Clearly

MCP is an interoperability layer for tools.

A simple way to think about it:

> instead of every agent framework writing a custom integration for every service,  
> MCP defines a standard way for agents to connect to external tool providers

### 13.1 The problem MCP addresses

Without standardization, you get a many-to-many integration problem:

- many agent clients
- many external services
- many one-off connectors
- duplicated effort
- fragmented behaviors
- inconsistent tooling interfaces

MCP exists to reduce that fragmentation.

### 13.2 The core architecture

The Day 2 whitepaper describes three roles:

- **Host**
- **Client**
- **Server**

In the Day 2b notebook, your ADK agent acts as the **client**.  
The external MCP service acts as the **server**.  
The overall application environment is the **host** context.

### 13.3 The communication layer

The whitepaper explains that MCP uses **JSON-RPC 2.0** as its base message format.

It also highlights two main transport styles:

- **stdio** for local / subprocess style communication
- **Streamable HTTP** for remote client-server communication

That is why the notebook uses:

- `StdioConnectionParams` for local demo servers
- streamable / HTTP-style connection params for remote services

### 13.4 MCP primitives

The whitepaper introduces several MCP capability types.

Server-side capabilities:
- **Tools**
- **Resources**
- **Prompts**

Client-side capabilities:
- **Sampling**
- **Elicitation**
- **Roots**

For Day 2, the most important one is **Tools**, because that is what the notebooks actually use.

### 13.5 Why MCP matters

MCP lets agent developers focus on:

- reasoning
- user experience
- orchestration
- workflow design

while external providers can build reusable servers that expose capabilities in a standardized way.

That is the real value proposition.

---

## 14. MCP in ADK

ADK integrates MCP through **`McpToolset`**.

This lets an ADK agent consume tools from an MCP server as part of its own tool list.

### Local demo pattern

In the notebook, ADK launches an MCP server using `npx` and a stdio connection.  
That is excellent for demos and local experimentation.

Conceptually, the flow looks like this:

```python
McpToolset(
    connection_params=StdioConnectionParams(
        server_params=StdioServerParameters(
            command="npx",
            args=["-y", "@modelcontextprotocol/server-everything"],
            tool_filter=["getTinyImage"],
        ),
        timeout=30,
    )
)
```

### Remote pattern

For remote MCP services, ADK can connect through streamable HTTP style configuration.

That pattern is more aligned with deployed services.

### Practical meaning

Once connected:

1. ADK discovers the server’s available tools
2. the model can choose to call them
3. the tool call is forwarded through MCP
4. the response returns to the agent
5. the agent continues reasoning

This is the bridge between **agent logic** and **external capability ecosystems**.


### 14.1 A useful ADK distinction

ADK supports **two** MCP directions:

1. **using existing MCP servers inside an ADK agent**
2. **exposing ADK tools through an MCP server for other clients**

Day 2 is focused on the first pattern.  
That is the important thing to remember for the Kaggle notebooks: your agent is the **consumer** of MCP tools, not the MCP server author.


---

## 15. Example MCP Servers Used or Mentioned on Day 2

### 15.1 Everything server

The Day 2b demo uses the **Everything** server from the official MCP examples.

Why this server is used:
- it is a reference / test server
- it exposes prompts, resources, and tools
- it is ideal for learning and experimentation

The notebook filters down to the `getTinyImage` tool to keep the demo minimal.

### 15.2 Kaggle MCP

The notebook also shows a Kaggle MCP example.

The idea is that an agent can use MCP to interact with Kaggle assets such as:
- datasets
- notebooks
- competitions
- metadata

This is a nice example because it shows how MCP is not only about toy demos.  
It can expose real developer workflows.

### 15.3 GitHub MCP

The notebook also mentions a GitHub-style remote MCP server configuration.

That reinforces two ideas:

- MCP is useful for developer tooling
- read-only restrictions are often wise when first integrating powerful systems

---

## 16. Long-Running Operations and Human Approval

The second half of Day 2b is one of the most practical parts of the course.

### The central idea

Not all tool calls should execute instantly.

Some operations need to:

- wait for human approval
- pause until an external event happens
- stop for compliance checks
- hold for cost review
- wait for background processing

That pattern is called a **long-running operation**.

### The shipping example

The notebook builds a shipping coordinator tool that behaves differently based on order size:

- small order → auto-approved
- large order → pause and request approval
- resumed call → finalize based on the returned decision

This models real-world constraints very well.

### Why this matters in production

This is exactly the kind of thing real systems need for:

- reimbursements
- procurement
- customer account deletion
- infrastructure provisioning
- database changes
- admin actions
- expensive batch jobs

An “agent that acts” without approval control is often not safe enough for production.

---

### 16.1 Alternative confirmation styles in ADK

The notebook teaches the **advanced confirmation** pattern through `ToolContext.request_confirmation(...)`, but ADK also supports a simpler boolean-style confirmation wrapper.

That means there are really two mental models:

- **simple yes/no confirmation** for straightforward approvals
- **advanced confirmation with payload** when you need structured input or richer approval data

The shipping example belongs to the second category because the workflow is explicitly modeled as a paused tool execution that later resumes.


## 17. Resume and Invocation IDs

The long-running workflow in Day 2b depends on several ADK ideas.

### 17.1 `ToolContext`

`ToolContext` is how the tool accesses workflow state relevant to tool execution.

In the notebook it is used for two key jobs:

- request confirmation
- inspect the returned confirmation

### 17.2 `request_confirmation()`

When the tool detects a case that needs human input, it calls:

- `tool_context.request_confirmation(...)`

This pauses the workflow and causes ADK to emit a special confirmation event.

### 17.3 Confirmation event

The notebook looks for the special event:

- `adk_request_confirmation`

That event signals that the workflow stopped at a decision checkpoint.

### 17.4 `App` + `ResumabilityConfig`

A plain `LlmAgent` is not enough for this pattern.

The course wraps the agent in an **`App`** and enables:

- `ResumabilityConfig(is_resumable=True)`

That allows the workflow state to be saved and resumed later.

### 17.5 Runner + Session

The workflow then uses a runner and session management so the paused execution can be resumed in the correct context.

### 17.6 Invocation ID

This is the most important operational detail.

Each run has an **`invocation_id`**.

When the workflow pauses, you must keep that same `invocation_id`.  
When resuming, you pass it back so ADK knows:

- continue the existing execution  
not  
- start an entirely new run

### 17.7 Why the notebook includes helper functions

The notebook writes small helper utilities to:

- inspect emitted events
- extract the approval information
- build the confirmation response
- resume the execution correctly

This shows that long-running workflows need **workflow code**, not just agent code.

That is a crucial Day 2 lesson.

---

## 18. Security and Enterprise Readiness Themes

The whitepaper becomes more serious in its later sections.

Once you connect agents to external tools, the risks change.

### 18.1 The risk is no longer only hallucination

Tool-enabled agents create additional failure modes:

- unauthorized actions
- over-broad access
- execution of harmful operations
- hidden privilege escalation
- weak auditability
- dangerous tool exposure

### 18.2 Confused deputy risk

One of the whitepaper’s most important security examples is the **confused deputy** problem.

This happens when:

- the **user** lacks permission
- the **MCP server** has broad permission
- the agent relays the request
- the server executes using its own authority instead of checking the user’s authority

This can turn the server into an unintended privilege escalator.

### 18.3 Good defensive habits for tool systems

Day 2 strongly implies the following rules:

- use **least privilege**
- expose only the tools you need
- prefer **read-only** capability where possible
- filter tools with `tool_filter`
- validate arguments
- require approval for high-risk actions
- log execution steps
- separate harmless reads from privileged writes

### 18.4 Development vs production posture

The ADK MCP documentation explicitly distinguishes environments.

- stdio is simple and good for development
- remote MCP servers are better for higher-scale deployments
- process-heavy stdio patterns are not ideal for high-scale production

So Day 2 is also teaching a deployment mindset, even before the course reaches the production day.

---

## 19. Best Practices You Should Carry Forward

This is the practical “what to actually do” section.

### 19.1 For custom function tools
- write clear docstrings
- use type hints
- keep purpose narrow
- return structured outputs
- make failures explicit
- avoid ambiguous parameter names

### 19.2 For tool responses
- return only what the model needs
- include clear success / failure fields
- keep schemas stable
- avoid giant payloads unless the next step truly needs them

### 19.3 For specialist agents
- create them when a subtask needs different behavior or stronger constraints
- use `AgentTool` when the root agent should stay in control
- do not confuse delegation with full conversation handoff

### 19.4 For MCP
- use MCP when you want standard external interoperability
- use local stdio MCP setups for experiments
- use remote / HTTP-style MCP for deployed service patterns
- filter exposed tools aggressively
- prefer read-only capability first

### 19.5 For long-running operations
- add approval checkpoints for risky or expensive actions
- preserve invocation identifiers
- make the pause state explicit to your workflow
- build workflow code to detect and resume confirmation events
- treat human approval as part of system design, not as an afterthought

---

## 20. Common Mistakes to Avoid

### Mistake 1: Making the tool description too vague
If the model cannot tell what the tool is for, it will call it badly or ignore it.

### Mistake 2: Returning unstructured text from tools
Free-form strings are harder for agents to reason over than predictable dictionaries.

### Mistake 3: Letting the LLM do exact computation
Use a deterministic execution path for arithmetic, code, or policy logic.

### Mistake 4: Using an AgentTool where you really need a handoff
Delegation and ownership transfer are not the same pattern.

### Mistake 5: Treating MCP as “just another plugin”
MCP is a protocol layer with architecture, transports, capabilities, and security implications.

### Mistake 6: Forgetting that resumability needs workflow logic
Pause/resume is not magical.  
You must inspect events, capture IDs, and resume correctly.

### Mistake 7: Granting broad tool privileges too early
Start read-only, narrow-scope, and observable.

### Mistake 8: Shipping without human approval for destructive actions
If an action is expensive, privileged, or irreversible, approval should be designed in from the start.

---

## 21. Day 2 Revision Checklist

Use this to test whether you actually understood Day 2.

### Concepts
- [ ] I can explain why agents need tools.
- [ ] I understand the difference between “know something” and “do something” tools.
- [ ] I can explain what MCP is in simple words.
- [ ] I understand why MCP reduces custom integration overhead.
- [ ] I know why long-running operations matter.

### ADK mechanics
- [ ] I know how a Python function becomes a `FunctionTool`.
- [ ] I know what role docstrings and type hints play.
- [ ] I understand how `AgentTool` works.
- [ ] I know why `BuiltInCodeExecutor` improves reliability.
- [ ] I know what `McpToolset` does.

### Workflow mechanics
- [ ] I know what `ToolContext` is used for.
- [ ] I know how `request_confirmation()` pauses a tool.
- [ ] I know what `adk_request_confirmation` means.
- [ ] I know why `App(..., resumability_config=...)` is required.
- [ ] I know why the same `invocation_id` must be reused during resume.

### Design judgement
- [ ] I know when to use custom tools vs built-in tools.
- [ ] I know when MCP is the better choice than custom integration.
- [ ] I know when an operation should require approval.
- [ ] I understand the confused deputy risk.
- [ ] I would start with least privilege and read-only access in production.

---

## 22. Short Glossary

**Tool**  
A callable external capability exposed to the agent.

**FunctionTool**  
A custom tool created from a normal function.

**AgentTool**  
An agent wrapped so another agent can call it like a tool.

**BuiltInCodeExecutor**  
ADK capability that runs code in a controlled execution environment.

**MCP**  
Model Context Protocol, a standard way for agents to connect to external tool providers.

**McpToolset**  
ADK component used to connect an agent to an MCP server.

**ToolContext**  
Execution context injected into tools for advanced workflow behavior such as confirmation.

**Confirmation**  
A checkpoint where the tool pauses and asks for human or system approval.

**Long-running operation**  
A tool flow that cannot or should not finish immediately.

**App**  
The ADK application wrapper that can add features like resumability.

**ResumabilityConfig**  
Configuration enabling workflows to pause and continue later.

**Invocation ID**  
The identifier used to resume the same workflow execution rather than starting a new one.

**Confused deputy**  
A security pattern where a privileged server performs actions on behalf of a less-privileged requester without enforcing the requester’s permissions.

---

## 23. Resources Reviewed

The documentation above is based on the official Day 2 resource flow and the supporting ADK / MCP documentation.

### Kaggle course resources
- [Kaggle Learn Guide — 5-Day AI Agents Intensive Course](https://www.kaggle.com/learn-guide/5-day-agents)
- [Day 2 Whitepaper — Agent Tools & Interoperability with MCP](https://www.kaggle.com/whitepaper-agent-tools-and-interoperability-with-mcp)
- [Day 2a — Agent Tools](https://www.kaggle.com/code/kaggle5daysofai/day-2a-agent-tools)
- [Day 2b — Agent Tools Best Practices](https://www.kaggle.com/code/kaggle5daysofai/day-2b-agent-tools-best-practices)

### ADK references
- [ADK Function Tools](https://adk.dev/tools-custom/function-tools/)
- [ADK MCP Tools](https://adk.dev/tools-custom/mcp-tools/)
- [ADK Tool Confirmation](https://adk.dev/tools-custom/confirmation/)
- [ADK Resume Stopped Agents](https://adk.dev/runtime/resume/)
- [ADK MCP Overview](https://adk.dev/mcp/)

### MCP references
- [Model Context Protocol — Example Servers](https://modelcontextprotocol.io/examples)

---

## Final Summary

Day 2 is the day you stop treating agents like smart chatbots and start treating them like **controlled software systems with external capabilities**.

The durable lessons are:

- tools extend capability
- schemas shape tool quality
- deterministic execution beats LLM guessing for exact logic
- MCP standardizes interoperability
- approval and resumability are essential for real-world actions
- tool power requires security design, not just clever prompts

If Day 1 taught you **how agents are structured**, Day 2 teaches you **how agents connect to the world safely and effectively**.
