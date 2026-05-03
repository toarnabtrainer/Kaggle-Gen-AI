# Day 5 Documentation — Prototype to Production

> GitHub-ready study documentation for **Day 5** of Kaggle’s **5-Day Agents** course.  
> Scope: this document follows the official **Day 5: Prototype to Production** flow — the course guide, the whitepaper, the companion podcast, and the two codelabs **Day 5a — Agent2Agent Communication** and **Day 5b — Agent Deployment** — and then connects them to the current ADK, A2A, and Vertex AI Agent Engine documentation.

---

## Table of Contents

- [1. Day 5 Goal](#1-day-5-goal)
- [2. What Day 5 Includes](#2-what-day-5-includes)
- [3. Why Day 5 Matters](#3-why-day-5-matters)
- [4. The Day 5 Mental Model](#4-the-day-5-mental-model)
- [5. Core Vocabulary](#5-core-vocabulary)
- [6. Whitepaper Foundations: From Demo to Operations](#6-whitepaper-foundations-from-demo-to-operations)
- [7. People, Process, and Technology](#7-people-process-and-technology)
- [8. Evaluation-Gated Deployment](#8-evaluation-gated-deployment)
- [9. CI/CD for Agents](#9-cicd-for-agents)
- [10. Safe Rollout Strategies](#10-safe-rollout-strategies)
- [11. Security from the Start](#11-security-from-the-start)
- [12. Operating Loop: Observe, Act, Evolve](#12-operating-loop-observe-act-evolve)
- [13. Day 5a — Agent2Agent Communication](#13-day-5a--agent2agent-communication)
- [14. What Day 5a Actually Teaches](#14-what-day-5a-actually-teaches)
- [15. When to Use A2A vs Local Sub-Agents](#15-when-to-use-a2a-vs-local-sub-agents)
- [16. A2A Building Blocks](#16-a2a-building-blocks)
- [17. Day 5a Walkthrough: Product Catalog + Customer Support](#17-day-5a-walkthrough-product-catalog--customer-support)
- [18. The A2A Request Flow](#18-the-a2a-request-flow)
- [19. How A2A and MCP Fit Together](#19-how-a2a-and-mcp-fit-together)
- [20. Registry Architectures](#20-registry-architectures)
- [21. Day 5b — Agent Deployment](#21-day-5b--agent-deployment)
- [22. What Day 5b Actually Teaches](#22-what-day-5b-actually-teaches)
- [23. Vertex AI Agent Engine in Plain English](#23-vertex-ai-agent-engine-in-plain-english)
- [24. Standard Deployment Package Structure](#24-standard-deployment-package-structure)
- [25. Deploying with the ADK CLI](#25-deploying-with-the-adk-cli)
- [26. Testing a Deployed Agent](#26-testing-a-deployed-agent)
- [27. Sessions in Production](#27-sessions-in-production)
- [28. Long-Term Memory with Memory Bank](#28-long-term-memory-with-memory-bank)
- [29. Cost Management and Cleanup](#29-cost-management-and-cleanup)
- [30. Agent Starter Pack, Cloud Run, and GKE](#30-agent-starter-pack-cloud-run-and-gke)
- [31. Common Mistakes on Day 5](#31-common-mistakes-on-day-5)
- [32. What You Should Be Able to Explain After Day 5](#32-what-you-should-be-able-to-explain-after-day-5)
- [33. Revision Checklist](#33-revision-checklist)
- [34. Short Glossary](#34-short-glossary)
- [35. Resources Reviewed](#35-resources-reviewed)

---

## 1. Day 5 Goal

Day 5 is the final shift in the course:

- **Earlier days** teach you how to build agents, give them tools, maintain context, and evaluate quality.
- **Day 5** teaches you how to turn those ideas into a **production system**.

That means Day 5 is not only about writing more agent code. It is about learning how to:

- expose agents as reusable services,
- connect agents across network and team boundaries,
- deploy agents to managed infrastructure,
- test them after deployment,
- monitor them in operation,
- add long-term memory responsibly,
- and manage production risks such as cost, safety, rollout, and reliability.

A simple way to summarize Day 5 is:

> **Day 5 = interoperability + deployment + operations.**

---

## 2. What Day 5 Includes

The official Day 5 learning path combines **conceptual material** and **hands-on labs**.

### Concept resources

- The **Day 5 section** in the Kaggle course guide.
- The **Prototype to Production** whitepaper.
- The **companion podcast** for the whitepaper.
- An optional **recorded livestream** for the day.

### Hands-on resources

- **Day 5a — Agent2Agent Communication**
- **Day 5b — Agent Deployment**

### Supporting reference docs

To understand the day fully, it also helps to read the current platform documentation behind the notebooks:

- ADK documentation on **A2A**
- official **A2A protocol** announcement and overview
- ADK and Vertex AI documentation on **Agent Engine**
- documentation for **Sessions**
- documentation for **Memory Bank**
- ADK deployment docs for testing deployed agents

### Best study order

A strong order for Day 5 is:

1. Read the **whitepaper**
2. Listen to the **podcast**
3. Do **Day 5a**
4. Do **Day 5b**
5. Revisit the whitepaper’s operational ideas after the labs

This order works because the whitepaper gives the “why,” while the notebooks show the “how.”

---

## 3. Why Day 5 Matters

A notebook demo can look impressive while still being nowhere near production-ready.

An agent can appear to work in a controlled lab but fail badly in the real world because of:

- missing evaluation gates,
- missing observability,
- unsafe tool permissions,
- poor authentication boundaries,
- unpredictable cost or latency,
- state bugs across sessions,
- brittle integrations with external systems,
- or no rollback strategy when behavior degrades.

The whitepaper emphasizes that the **last mile** from prototype to production is often the hardest part. It frames production work not as polish, but as the bulk of the real engineering effort.

This is why Day 5 matters:

- **Day 5a** teaches how agents can communicate as services.
- **Day 5b** teaches how to deploy and operate them on managed infrastructure.
- The **whitepaper** teaches how to do both responsibly.

---

## 4. The Day 5 Mental Model

Use this as the central map for the whole day:

```text
Build an agent
    ↓
Decide whether it stays local or becomes a service
    ↓
If it becomes a service, expose it through A2A
    ↓
Consume remote agents through formal contracts
    ↓
Deploy the agent to managed infrastructure
    ↓
Evaluate before release
    ↓
Roll out safely
    ↓
Observe, control, and improve after release
```

Another useful mental model is:

### Pre-production

- evaluation
- CI/CD
- security review
- rollout design

### Production

- monitoring
- tracing
- logs
- cost control
- memory/state management
- incident response
- continuous improvement

### System design split

- **MCP** helps an agent use **tools and resources**
- **A2A** helps an agent collaborate with **other agents**
- **Agent Engine** helps you **run and manage** the resulting system in production

---

## 5. Core Vocabulary

### A2A (Agent2Agent)

An open protocol for communication between agents across process, network, framework, and organizational boundaries.

### Agent Card

A machine-readable description of an agent’s identity and capabilities. It acts like a service contract or discoverable “business card.”

### `to_a2a()`

An ADK helper that exposes an agent as an A2A-compatible service and automatically generates the agent card.

### `RemoteA2aAgent`

A client-side ADK abstraction that lets one agent call a remote A2A agent almost like a local sub-agent.

### Local sub-agent

An agent that lives inside the same application or process and is orchestrated locally.

### Agent Engine

Google Cloud’s managed runtime for deploying, scaling, observing, and operating AI agents.

### Session

The container for interaction history, events, and state across turns in a conversation or workflow.

### Memory Bank

A service for generating and retrieving long-term memory across sessions.

### CI/CD

Continuous integration and continuous delivery/deployment. For agents, this includes code checks, tool validation, eval gates, rollout controls, and release automation.

### Canary release

A rollout pattern where a small percentage of traffic goes to the new version first.

### Blue-green deployment

A rollout pattern where two separate environments exist and traffic switches from the old to the new one after validation.

### A/B testing

A pattern where two versions are exposed to traffic for comparison.

### AgentOps

The discipline of operating, monitoring, evaluating, deploying, and continuously improving production agents.

---

## 6. Whitepaper Foundations: From Demo to Operations

The **Prototype to Production** whitepaper is the conceptual backbone of Day 5.

Its main message is straightforward:

> building the agent is only the beginning; most of the hard work is in operating it safely and reliably.

The paper highlights several important realities:

### 6.1 The “last mile” gap

The gap between a notebook prototype and a real deployment is large. Production systems need infrastructure, testing, controls, observability, and governance.

### 6.2 Agent-specific operational complexity

Agents are harder than traditional deterministic software in several ways:

- they reason dynamically,
- select tools conditionally,
- may call external systems,
- may carry state across time,
- and can have unpredictable cost/latency profiles.

### 6.3 Three operational pillars

The paper consistently centers three practical pillars:

1. **automated evaluation**
2. **automated deployment / CI-CD**
3. **comprehensive observability**

These are not optional extras. They are the production foundation.

### 6.4 Agent systems are composite systems

An agent system is not just “the model.” It includes:

- code,
- prompts,
- tools,
- orchestration rules,
- credentials,
- environment settings,
- retrieval/memory layers,
- deployment infrastructure,
- and evaluation datasets.

That is why production failures can come from many places outside the model itself.

---

## 7. People, Process, and Technology

One of the most useful ideas in the whitepaper is that production is not only technical. It sits at the intersection of:

- **people**
- **process**
- **technology**

### People

Different roles usually need to cooperate:

- application developers,
- platform engineers,
- security teams,
- SRE / operations,
- product owners,
- compliance or governance stakeholders.

### Process

Healthy production systems require agreed workflows for:

- code review,
- eval review,
- rollout approval,
- incident handling,
- secrets management,
- and post-release monitoring.

### Technology

Technology provides the mechanisms:

- testing and eval tooling,
- deployment platforms,
- session and memory services,
- telemetry,
- permission boundaries,
- tracing and logging,
- and rollback infrastructure.

### Why this matters for Day 5

The notebooks focus on code, but the whitepaper reminds you that real production readiness depends on all three dimensions working together.

---

## 8. Evaluation-Gated Deployment

A core production principle from Day 5 is:

> **No new agent version should go to users without passing evaluation.**

This is stronger than “run a few manual tests.”

### What should be evaluated

You need to evaluate more than the final answer. You also need to inspect:

- tool choices,
- tool order,
- argument correctness,
- safety behavior,
- groundedness,
- policy compliance,
- and multi-step trajectories.

### Why this matters

An agent can produce a plausible final answer for the wrong reasons.  
For example, it may:

- call the wrong tool but still stumble into a usable answer,
- leak hidden information during intermediate steps,
- perform unsafe side effects,
- or incur excessive cost while appearing “correct.”

### The Day 5 production mindset

Evaluation is treated as a **quality gate**, not a documentation exercise.

In practice, that means:

- integrating evals into CI/CD,
- blocking deployment on failing evals,
- and continuously updating eval sets when new failure modes are discovered.

---

## 9. CI/CD for Agents

Traditional CI/CD pipelines check code and infrastructure.  
Agent CI/CD must also check **behavior**.

### A strong agent CI/CD pipeline usually includes

- linting and unit tests
- dependency and security scans
- tool contract validation
- prompt/config verification
- evaluation suites
- staging deployment
- rollout automation
- post-deploy monitoring checks

### Why this is more important for agents

Because agent behavior is shaped by:

- prompts,
- tool availability,
- model versions,
- configuration changes,
- environment variables,
- and evolving evaluation data.

A tiny change can alter behavior significantly.

### The “shift left” idea

The whitepaper emphasizes catching problems as early as possible:

- before merge,
- before deployment,
- before full traffic rollout.

This reduces production risk and makes debugging easier.

---

## 10. Safe Rollout Strategies

Day 5 treats deployment as more than “push new version live.”

The whitepaper highlights several safer rollout patterns.

### Canary releases

Release the new version to a small subset of traffic first.  
Use it when you want a controlled early signal before wider rollout.

### Blue-green deployments

Maintain old and new environments in parallel, then switch traffic after validation.  
Use it when you need cleaner rollback and stronger separation.

### A/B testing

Expose multiple versions for measured comparison.  
Use it when you want to compare outcomes, user behavior, or quality trade-offs.

### Why rollout strategies matter for agents

Agents can regress in subtle ways:

- increased hallucinations,
- silent tool misuse,
- worse cost efficiency,
- degraded latency,
- or lower policy compliance.

Safe rollout patterns limit blast radius.

---

## 11. Security from the Start

The whitepaper treats security as a first-class design requirement, not an afterthought.

### Agent-specific risks

Production agents introduce several security concerns:

- **prompt injection**
- **rogue or over-privileged actions**
- **data leakage**
- **unsafe tool execution**
- **credential misuse**
- **weak tenant isolation**
- **improper approval flows for sensitive actions**

### Secure design principles for Day 5

#### Least privilege

Give each agent only the permissions it actually needs.

#### Human approval for high-risk actions

Sensitive operations should require explicit confirmation or policy gates.

#### Strong boundaries for tools and remote agents

Do not assume every external system is trustworthy.  
A tool call or remote agent invocation is a security boundary.

#### Secrets management

Never hardcode secrets in notebooks or repositories.  
Use approved secret storage and managed authentication wherever possible.

#### Defense in depth

Use multiple layers:

- input validation,
- permission checks,
- evals,
- monitoring,
- rollback controls,
- and incident response.

### Practical takeaway

Productionizing an agent means designing for misuse and failure, not just happy-path success.

---

## 12. Operating Loop: Observe, Act, Evolve

One of the clearest operational models in the whitepaper is:

> **Observe → Act → Evolve**

### Observe

Collect telemetry from the running system:

- logs,
- traces,
- metrics,
- tool paths,
- latency,
- errors,
- and cost signals.

### Act

Use real-time operational controls to respond:

- throttle traffic,
- switch rollout strategy,
- disable a bad tool,
- revert a release,
- or intervene when cost or safety thresholds are exceeded.

### Evolve

Use patterns found in observability data to improve the system over time:

- revise prompts,
- improve tools,
- add evaluations,
- redesign architecture,
- refine memory behavior,
- or change deployment policy.

### Why this matters

A production agent is not “finished” at launch.  
It must be continuously operated and improved.

---

## 13. Day 5a — Agent2Agent Communication

Day 5a focuses on one key question:

> **How do agents collaborate when they are not all inside the same application?**

The notebook teaches **A2A** as the answer.

### Central idea

Instead of bundling every specialist into one local codebase, you can expose some agents as **remote services** with formal contracts and then consume them from other agents.

That lets teams build:

- cross-service agent systems,
- cross-team integrations,
- cross-framework systems,
- and cross-language architectures.

### Why this matters

This is the difference between:

- a single multi-agent app in one notebook, and
- an actual distributed agent ecosystem.

---

## 14. What Day 5a Actually Teaches

The Day 5a notebook is practical and specific. It teaches you to:

- understand **when A2A is the right design choice**
- expose an ADK agent through **`to_a2a()`**
- run that exposed agent as an A2A server
- inspect the generated **agent card**
- consume the remote service through **`RemoteA2aAgent`**
- integrate the remote agent into another ADK agent as if it were a local specialist
- trace the end-to-end request flow
- understand why this pattern is useful for external vendors, different teams, or independent deployments

### The core Day 5a lesson

A2A is not just “remote tool calling.”  
It is **service-level delegation from one agent to another agent**.

The remote side is not a dumb function.  
It can be an autonomous specialist with its own instructions, tools, state, and internal reasoning.

---

## 15. When to Use A2A vs Local Sub-Agents

This is one of the most important decisions in the whole day.

| Use **A2A** when... | Use **local sub-agents** when... |
|---|---|
| the specialist is owned by another team or organization | everything lives in the same codebase |
| the specialist runs as an external service | you need low latency inside one process |
| frameworks or languages differ | shared in-memory state matters |
| you want a formal contract and network boundary | orchestration is tightly coupled |
| you want independent deployment and lifecycle management | the delegation is internal implementation detail |

### The simplest rule

- **Local sub-agents** are best for tightly coupled internal orchestration.
- **A2A** is best for reusable, independently operated, formally exposed agent services.

### Common mistake

Do not use A2A just because it sounds more advanced.  
If the specialist is purely internal and same-process, local sub-agents are usually simpler and faster.

---

## 16. A2A Building Blocks

To understand Day 5a properly, learn these building blocks.

### 16.1 Agent card

An **agent card** is the discoverable contract for an A2A agent.  
It typically describes things like:

- the agent’s name and description
- version information
- skills / capabilities
- endpoints
- supported input/output modes
- protocol metadata

Think of it as the remote agent’s structured “business card.”

### 16.2 `to_a2a()`

ADK’s **`to_a2a()`** helper turns an ADK agent into an A2A-compatible application.  
It automatically handles the A2A service wrapper and agent-card generation.

This is important because it lowers the barrier to exposing an ADK agent as a reusable network service.

### 16.3 Standard well-known card path

The notebook demonstrates that the exposed A2A service publishes its agent card at the standard **well-known path**.  
This makes discovery and inspection predictable.

### 16.4 `RemoteA2aAgent`

`RemoteA2aAgent` is the client-side abstraction used by another ADK agent.  
It reads the remote agent’s card and lets the local orchestrator treat the remote service like a specialist capability.

This is why Day 5a feels elegant: the remote agent is operationally a network service, but architecturally it can still participate in multi-agent workflows.

### 16.5 Tasks and artifacts

The A2A protocol centers around **tasks** and their lifecycle.  
A requesting agent submits work, the remote agent executes, and results are returned in a structured way.

This matters because A2A is meant for agent collaboration, not just one-off RPC.

### 16.6 Long-running and framework-agnostic interaction

The official A2A announcement explains that the protocol is designed around existing standards such as HTTP, SSE, and JSON-RPC, and supports real-world agent behavior like long-running tasks and real-time updates.

That design choice is why A2A is useful beyond one vendor or one framework.

---

## 17. Day 5a Walkthrough: Product Catalog + Customer Support

The official Day 5a example uses a practical e-commerce scenario:

- a **Product Catalog Agent**
- a **Customer Support Agent**

### Why this example is good

It illustrates a real production boundary:

- the product catalog capability could be owned by another vendor or team,
- it may live in a different codebase,
- and it may need an explicit service contract.

### Step-by-step conceptually

#### Step 1: Build the specialist

Create a **Product Catalog Agent** with the instructions and logic needed to answer product-related questions.

#### Step 2: Expose it through A2A

Wrap the catalog agent with **`to_a2a()`** and serve it.

This makes the catalog agent available as a reusable remote service.

#### Step 3: Inspect the agent card

Confirm what the service is publishing about itself:

- who it is,
- what it can do,
- how clients should talk to it.

#### Step 4: Create the consuming agent

Build a **Customer Support Agent** that handles user-facing requests and delegates catalog-specific work to the remote catalog service.

#### Step 5: Use `RemoteA2aAgent`

The support agent uses `RemoteA2aAgent` so that remote catalog access becomes part of its orchestration flow.

#### Step 6: Run the conversation

Ask a user question such as product availability, comparison, or feature clarification.  
The support agent decides when to delegate, sends the request, receives the result, and synthesizes the final response.

### Why this is important

This example shows how **independent agent services** can still feel integrated in one conversational workflow.

---

## 18. The A2A Request Flow

Here is the Day 5a flow in plain English:

```text
User asks Customer Support Agent a question
    ↓
Customer Support Agent decides it needs product information
    ↓
RemoteA2aAgent sends a standardized A2A request
    ↓
Product Catalog Agent receives the task
    ↓
Product Catalog Agent reasons and responds
    ↓
Result is returned to Customer Support Agent
    ↓
Customer Support Agent produces the final answer
```

### What is happening architecturally

- orchestration stays with the **requesting** agent,
- specialization lives with the **remote** agent,
- and the protocol provides a formal boundary between them.

### Why this is useful

It lets you decouple:

- deployment lifecycle,
- ownership,
- internal implementation,
- and technology choices.

---

## 19. How A2A and MCP Fit Together

This distinction matters a lot.

### MCP

MCP is primarily about giving an agent access to:

- tools,
- resources,
- and structured external context.

It is a tool/resource interoperability protocol.

### A2A

A2A is about connecting one agent to another agent:

- an autonomous collaborator,
- a specialized service,
- or a separately managed capability.

### Simple rule

- Use **MCP** when the agent needs **resources or tools**
- Use **A2A** when the agent needs **another agent**

### Why the whitepaper says they are complementary

A real production system often needs both:

- an agent may use **MCP** to access databases, files, APIs, or enterprise tools,
- while also using **A2A** to delegate larger goals to a remote specialist agent.

So A2A does not replace MCP, and MCP does not replace A2A.

---

## 20. Registry Architectures

The whitepaper also introduces the idea of an **Agent Registry**.

### What problem a registry solves

As organizations grow, they may end up with many specialized agents owned by different teams.  
Without a discovery mechanism, these services remain siloed.

A registry helps teams:

- discover reusable agents,
- inspect their agent cards,
- understand capabilities and ownership,
- and compose systems without tight coupling.

### When a registry becomes useful

You usually need a registry when:

- multiple teams are publishing agents,
- reuse matters,
- capabilities are growing quickly,
- and central visibility is needed.

### When not to overbuild it

Small systems do not need a complex registry on day one.  
Start simple, then add discovery and governance when the number of remote agents grows.

---

## 21. Day 5b — Agent Deployment

Day 5b answers the next question:

> **Once you have an agent architecture, how do you actually deploy it?**

The notebook uses **Vertex AI Agent Engine** as the production target.

### Central idea

You package an ADK agent for deployment, push it through the ADK CLI, obtain a managed remote runtime, and then test and operate the deployed service.

### Why this matters

The agent is no longer just a notebook process.  
It becomes an environment-managed service with:

- lifecycle controls,
- infrastructure,
- sessions,
- observability,
- and cloud operations.

---

## 22. What Day 5b Actually Teaches

The Day 5b notebook teaches you to:

- create a **production-ready ADK agent package**
- configure its dependencies and environment
- specify runtime resource settings
- deploy it to **Vertex AI Agent Engine** using the **ADK CLI**
- retrieve and test the deployed service
- understand where **sessions** fit in production
- understand how to add long-term memory through **Memory Bank**
- and clean up cloud resources to avoid cost surprises

The notebook uses a **Weather Assistant** example because it is simple enough to focus on deployment concepts rather than business logic complexity.

---

## 23. Vertex AI Agent Engine in Plain English

**Agent Engine** is the managed runtime used in Day 5b.

In plain language, it gives you a cloud-hosted place to:

- run an agent,
- scale it,
- manage sessions,
- observe behavior,
- and connect to production services like memory and telemetry.

### What Agent Engine provides

Based on the current documentation, Agent Engine includes managed support for:

- runtime execution
- deployment and scaling
- session handling
- memory integration
- observability
- governance-related controls and identity/security capabilities

### Helpful terminology note

In some APIs and resource names, deployed agents still appear as **ReasoningEngine** resources for backward compatibility.  
So if you see a resource path containing `reasoningEngines`, that is expected.

---

## 24. Standard Deployment Package Structure

The Day 5b notebook makes an important production point:

> a deployable agent is a **package**, not just a notebook cell.

A typical standard deployment package includes something like:

```text
sample_agent/
├── agent.py
├── requirements.txt
├── .env
└── .agent_engine_config.json
```

### `agent.py`

Contains the agent definition:

- name
- model
- instructions
- tools
- root agent object

### `requirements.txt`

Lists the runtime dependencies.  
The notebook includes ADK plus telemetry-related dependencies.

### `.env`

Contains environment settings used by the deployed runtime, such as cloud location and Vertex AI usage flags.

### `.agent_engine_config.json`

Specifies deployment/runtime parameters such as:

- min/max instances
- CPU/memory limits
- other deployment controls

### Why this matters

Production deployment requires reproducibility.  
You want the runtime, config, and code to travel together.

---

## 25. Deploying with the ADK CLI

The notebook uses the **ADK CLI** to deploy to Agent Engine.

### High-level deployment flow

1. Prepare your Google Cloud project
2. Enable required APIs
3. Authenticate correctly
4. Package the agent directory
5. Run the deployment command
6. Capture the returned resource name
7. Test the deployed agent
8. Monitor and clean up when done

### APIs commonly enabled in the notebook

The notebook enables several cloud services tied to deployment and operations, including:

- Vertex AI
- Cloud Storage
- Cloud Logging
- Cloud Monitoring
- Cloud Trace
- Telemetry-related services

### Typical deployment command pattern

A generic standard deployment command looks like this:

```bash
adk deploy agent_engine \
  --project=$PROJECT_ID \
  --region=$LOCATION_ID \
  --display_name="my-agent" \
  path_to_agent_folder
```

The Day 5b notebook also demonstrates a version that explicitly points to the Agent Engine config file.

### What happens during deployment

The deployment flow typically:

- packages the agent code,
- uploads the artifact,
- builds the runtime image/container behind the scenes,
- provisions the managed service,
- and returns the deployed resource identifier.

### Important mindset

Deployment is not the end.  
It is the start of managed operation.

---

## 26. Testing a Deployed Agent

After deployment, the notebook tests the remote agent through the platform SDK.

### Why this step matters

A deployment can succeed technically while the agent still behaves incorrectly because of:

- wrong environment variables,
- missing dependencies,
- tool issues,
- permissions problems,
- or session misconfiguration.

### Typical post-deploy test flow

- initialize the Vertex AI client or SDK,
- list deployed agents or fetch the target resource,
- select the deployed agent,
- create or use a session if required,
- send a query,
- inspect the response and trace output.

### What to verify

- the agent is reachable,
- the expected model/tool behavior appears,
- environment configuration worked,
- latency is acceptable,
- and responses align with the intended instructions.

### Practical production takeaway

“Deployment successful” does **not** mean “agent validated.”  
Always test the running artifact.

---

## 27. Sessions in Production

By Day 5, sessions should already make sense from Day 3.  
The new question is:

> **What do sessions look like once the agent is deployed?**

### Core idea

When ADK agents are deployed to Agent Engine, **session management is handled as part of the managed runtime architecture**.

### Why sessions still matter

Sessions remain the backbone for:

- turn history,
- events,
- state,
- and context continuity.

### Production meaning of a session

A deployed session is not just a local Python object.  
It is part of the managed operational model of the deployed agent.

### What you should remember

- **Session** = short- to medium-term conversational/workflow continuity
- **Memory Bank** = durable cross-session memory

Do not confuse them.

---

## 28. Long-Term Memory with Memory Bank

The Day 5b notebook introduces **Vertex AI Memory Bank** as the production answer to long-term memory.

### The problem it solves

A regular session usually ends.  
If the agent needs continuity across future sessions, session history alone is not enough.

### What Memory Bank does

Memory Bank generates and retrieves durable memories so the agent can retain useful information across sessions.

### How it fits conceptually

```text
Current turn
    ↓
Session holds recent interaction history
    ↓
Memory Bank stores selected long-term information
    ↓
Future sessions can retrieve relevant memory
```

### Important Day 5 message

Memory in production is not “store every conversation forever.”  
It is a managed service with retrieval and generation behavior.

### ADK integration concepts

The current ADK and Agent Engine docs describe several ways to work with memory:

- `PreloadMemory` / `PreloadMemoryTool`
- `LoadMemory`
- `add_session_to_memory`
- callback-based memory writing

These patterns connect what you learned on Day 3 to a production memory service.

### Very important caution

The documentation warns that memory generation may still persist information judged “useful,” including potentially sensitive details.  
So you should not treat Memory Bank as an automatic privacy filter.

You still need:

- data governance,
- privacy review,
- retention policy,
- and careful memory design.

---

## 29. Cost Management and Cleanup

One of the most practical Day 5b lessons is about **cost discipline**.

### Why Day 5 emphasizes cleanup

Managed deployments can continue running after your experiment ends.  
That means you can keep incurring charges unintentionally.

### Cost drivers you should keep in mind

- model usage
- tool/API calls
- deployed runtime instances
- session and memory activity
- logging/monitoring overhead
- traffic volume

### Good Day 5 habits

- deploy only what you are actively testing
- choose appropriate region and resource sizing
- monitor usage early
- clean up experiments promptly
- avoid leaving idle production resources running

### Production takeaway

Cloud deployment is part of engineering design, not just a platform detail.  
Cost control must be intentional.

---

## 30. Agent Starter Pack, Cloud Run, and GKE

The Day 5 notebook centers on **standard deployment to Agent Engine**, but the docs make a broader point: there are multiple deployment paths.

### Standard Agent Engine deployment

Best when you want a direct managed path using ADK CLI and Agent Engine.

### Agent Starter Pack (ASP)

The whitepaper and docs present **Agent Starter Pack** as a stronger production accelerator that includes things like:

- prebuilt templates
- infrastructure patterns
- CI/CD support
- observability setup
- Terraform and environment scaffolding

This is useful when you want a more complete production baseline instead of a minimal direct deploy.

### Cloud Run and GKE

The notebook also contrasts Agent Engine with alternatives such as:

- **Cloud Run**
- **GKE**

### How to think about the options

- Use **Agent Engine** when you want managed agent-native runtime features.
- Use **Cloud Run** or **GKE** when your architecture or platform standards require more custom container/infrastructure control.
- Use **Agent Starter Pack** when you want a more opinionated production foundation with CI/CD and ops patterns included.

---

## 31. Common Mistakes on Day 5

### Mistake 1: Using A2A for everything

Not every specialist needs to be a remote service.  
If it is in the same app and tightly coupled, local sub-agents are usually better.

### Mistake 2: Treating A2A as a replacement for MCP

They solve different problems.  
A2A is for agent-to-agent delegation.  
MCP is for tool/resource interoperability.

### Mistake 3: Thinking deployment is the finish line

Deployment only gets the system running.  
Production quality depends on evals, rollout, monitoring, and iteration.

### Mistake 4: Skipping post-deploy testing

A green deploy can still hide broken dependencies or permissions.

### Mistake 5: Confusing sessions with memory

Sessions manage ongoing interaction state.  
Memory Bank manages durable cross-session memory.

### Mistake 6: Ignoring cost

Leaving agents deployed after testing is an easy way to accumulate unnecessary charges.

### Mistake 7: Underestimating security boundaries

Remote agents, tools, and memory systems are all trust boundaries.  
Treat them that way.

### Mistake 8: Rolling out without a gate

Manual spot checks are not enough.  
Use evaluation-gated deployment.

### Mistake 9: Building a registry too early

Registries are useful at scale, but unnecessary complexity for a tiny system.

### Mistake 10: Assuming the model alone determines quality

Production behavior depends on the full system: prompts, tools, configs, sessions, memory, deployment, and monitoring.

---

## 32. What You Should Be Able to Explain After Day 5

By the end of Day 5, you should be able to explain all of the following clearly.

### Architecture

- why some specialists stay local and others become remote A2A services
- how a remote agent can be consumed as a collaborator
- why agent cards matter
- how A2A differs from MCP

### Operations

- why production requires evaluation-gated deployment
- why rollout strategies matter
- why observability is not optional
- how “Observe → Act → Evolve” works

### Deployment

- what a deployable ADK package contains
- what Agent Engine provides
- how the ADK CLI deployment flow works
- why post-deploy testing is required

### State and memory

- how deployed sessions work
- how Memory Bank extends memory across sessions
- why long-term memory requires governance and caution

### Production discipline

- why costs must be monitored
- why cleanup matters
- why security must be designed in from the start

---

## 33. Revision Checklist

Use this as a self-test before moving on.

- [ ] I can explain the difference between **local sub-agents** and **A2A remote agents**.
- [ ] I know when **A2A** is a better fit than local orchestration.
- [ ] I know what an **agent card** is and why it matters.
- [ ] I understand what **`to_a2a()`** does.
- [ ] I understand what **`RemoteA2aAgent`** does.
- [ ] I can describe the **Product Catalog + Customer Support** Day 5a example.
- [ ] I can explain the difference between **A2A** and **MCP**.
- [ ] I understand why production requires **evaluation-gated deployment**.
- [ ] I can name and explain **canary**, **blue-green**, and **A/B** rollout strategies.
- [ ] I understand the whitepaper’s **Observe → Act → Evolve** operating loop.
- [ ] I know what **Vertex AI Agent Engine** provides.
- [ ] I know the basic structure of a deployable ADK agent package.
- [ ] I understand the purpose of **`.agent_engine_config.json`**.
- [ ] I know that deployment success is not the same as validation success.
- [ ] I understand the difference between **session continuity** and **Memory Bank**.
- [ ] I know why **cleanup and cost control** are essential in Day 5.

---

## 34. Short Glossary

### Agent Engine
Managed runtime for deploying and operating AI agents on Google Cloud.

### Agent Card
Structured description of an A2A agent’s identity and capabilities.

### Agent Registry
A discovery layer for reusable remote agents, especially in multi-team environments.

### A2A
Protocol for agent-to-agent communication across service boundaries.

### Artifact
Structured output produced during an A2A task lifecycle.

### Canary
A gradual rollout pattern where only a small traffic slice hits the new version first.

### CI/CD
Automation pipeline for building, validating, releasing, and rolling back software or agents.

### Memory Bank
Long-term memory service for retaining useful information across sessions.

### MCP
Protocol for connecting models/agents to tools and resources.

### RemoteA2aAgent
Client-side ADK object that lets one agent talk to a remote A2A agent.

### Session
Managed container of turns, events, and state for ongoing interaction.

### `to_a2a()`
ADK helper that exposes a local ADK agent as an A2A-compatible service.

---

## 35. Resources Reviewed

### Official Day 5 course resources

- [Kaggle 5-Day Agents Learn Guide](https://www.kaggle.com/learn-guide/5-day-agents)
- [Prototype to Production — Whitepaper](https://www.kaggle.com/whitepaper-prototype-to-production)
- [Prototype to Production — Companion Podcast](https://www.youtube.com/watch?v=8Wyt9l7ge-g)
- [Day 5a — Agent2Agent Communication](https://www.kaggle.com/code/kaggle5daysofai/day-5a-agent2agent-communication)
- [Day 5b — Agent Deployment](https://www.kaggle.com/code/kaggle5daysofai/day-5b-agent-deployment)

### A2A references

- [ADK A2A Introduction](https://adk.dev/a2a/intro/)
- [ADK A2A Quickstart — Exposing an Agent](https://adk.dev/a2a/quickstart-exposing/)
- [ADK A2A Extension](https://adk.dev/a2a/a2a-extension/)
- [A2A Protocol Announcement](https://developers.googleblog.com/en/a2a-a-new-era-of-agent-interoperability/)
- [A2A Ecosystem Update / Linux Foundation Note](https://developers.googleblog.com/en/google-cloud-donates-a2a-to-the-linux-foundation/)

### Deployment and production references

- [Vertex AI Agent Engine Overview](https://docs.cloud.google.com/agent-builder/agent-engine/overview)
- [ADK — Deploy to Agent Engine](https://adk.dev/deploy/agent-engine/)
- [ADK — Standard Deployment to Agent Engine](https://adk.dev/deploy/agent-engine/deploy/)
- [ADK — Testing Deployed Agents](https://adk.dev/deploy/agent-engine/test/)
- [Agent Engine Sessions Overview](https://docs.cloud.google.com/agent-builder/agent-engine/sessions/overview)
- [Memory Bank Overview](https://docs.cloud.google.com/agent-builder/agent-engine/memory-bank/overview)
- [Memory Bank Setup](https://docs.cloud.google.com/agent-builder/agent-engine/memory-bank/set-up)
- [ADK Memory Documentation](https://adk.dev/sessions/memory/)

---

## Final Summary

Day 5 is where the course stops being about isolated notebooks and becomes about **agent systems**.

The day teaches three big ideas:

1. **Agents can collaborate across service boundaries** using **A2A**
2. **Agents can be deployed and operated as managed services** using **Agent Engine**
3. **Production quality depends on evaluation, rollout, observability, security, and operational discipline**

If Day 1 taught you how to make an agent act, Day 5 teaches you how to make an agent system **survive contact with the real world**.
