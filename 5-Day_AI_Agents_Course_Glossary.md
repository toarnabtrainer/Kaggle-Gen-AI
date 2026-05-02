# 5-Day AI Agents Course Glossary

This glossary is designed for the **Kaggle 5-Day AI Agents Intensive Course with Google** and covers the most important terms students see across **Day 1 to Day 5**.

## How to use this glossary
- **Abbreviation** shows the short form, if there is one.
- **Full form / expansion** gives the expanded meaning of the abbreviation or the formal name of the class/method.
- Some entries such as `FunctionTool` or `run_debug()` are **course keywords**, not abbreviations, so their abbreviation field is marked as `—`.
- **Relevant day(s)** shows where the term is most important.

---

## 1) Core foundation terms

| Term | Abbreviation | Full form / expansion | Definition | Relevant day(s) |
|---|---|---|---|---|
| Artificial Intelligence | AI | Artificial Intelligence | A broad field of computing focused on building systems that can perform tasks associated with human intelligence, such as reasoning, prediction, language understanding, or decision support. | D1–D5 |
| Agent | — | Agent | An AI application that uses a model, instructions, tools, and control logic to work toward a user goal. | D1–D5 |
| Large Language Model | LLM | Large Language Model | A model trained on large text corpora that can understand and generate language, follow instructions, and support reasoning-style workflows. | D1–D5 |
| Generative AI | GenAI | Generative Artificial Intelligence | A branch of AI focused on generating new content such as text, code, images, audio, or structured outputs. | D1–D5 |
| Gemini | — | Gemini models | Google’s family of generative AI models used in the course notebooks. | D1–D5 |
| Prompt | — | Prompt | The text input or instruction given to a model or agent. | D1–D5 |
| Instruction | — | Instruction | The behavioral guidance given to an agent, such as its role, boundaries, and goals. | D1–D5 |
| Tool | — | Tool | An external capability an agent can call, such as a Python function, API, search service, or code executor. | D1–D5 |
| Multi-agent system | — | Multi-agent system | A system in which multiple specialized agents collaborate to solve a task. | D1, D5 |
| Orchestration | — | Orchestration | The control logic that decides how agents, tools, and steps are sequenced or routed. | D1, D5 |
| Workflow | — | Workflow | A structured control flow such as sequential, parallel, or loop execution. | D1 |
| `LlmAgent` | — | `LlmAgent` (ADK class used in course notebooks) | An ADK agent class used in the course notebooks to pair a language model with instructions and tools. | D1 |
| `SequentialAgent` | — | `SequentialAgent` | An ADK workflow agent that runs its steps in order, one after another. | D1 |
| `ParallelAgent` | — | `ParallelAgent` | An ADK workflow agent that can run multiple branches concurrently. | D1 |
| `LoopAgent` | — | `LoopAgent` | An ADK workflow agent that repeats a workflow until a stopping condition or limit is reached. | D1 |
| `output_key` | — | `output_key` | A state key name used to save an agent’s output so later steps can reuse it. | D1, D3 |

---

## 2) ADK, tools, and interoperability terms

| Term | Abbreviation | Full form / expansion | Definition | Relevant day(s) |
|---|---|---|---|---|
| Agent Development Kit | ADK | Agent Development Kit | Google’s open-source framework for building, debugging, evaluating, and deploying agents. | D1–D5 |
| Application Programming Interface | API | Application Programming Interface | A defined way for software components to communicate with each other. | D2–D5 |
| Software Development Kit | SDK | Software Development Kit | A package of libraries, tools, and examples that helps developers build on a platform. | D2–D5 |
| Command-Line Interface | CLI | Command-Line Interface | A terminal-based interface used for commands such as `adk create`, `adk web`, or `adk eval`. | D1, D4, D5 |
| Model Context Protocol | MCP | Model Context Protocol | An open standard for connecting AI applications to external data sources, tools, and workflows. | D2 |
| `FunctionTool` | — | `FunctionTool` | An ADK tool wrapper that turns a Python function into a callable agent tool. | D2 |
| `AgentTool` | — | `AgentTool` | An ADK wrapper that lets one agent be used as a tool by another agent. | D1, D2 |
| `ToolContext` | — | `ToolContext` | The runtime context object provided to a tool for things like state access, confirmation, and metadata. | D2, D3 |
| `BuiltInCodeExecutor` | — | `BuiltInCodeExecutor` | An ADK component used to execute generated code in a controlled environment. | D2 |
| Human-in-the-loop | HITL | Human-in-the-loop | A design pattern where a human reviews or approves important or sensitive actions before they proceed. | D2, D5 |
| Resumability | — | Resumability | The ability to pause a workflow and continue it later from the same point. | D2 |
| `invocation_id` | — | `invocation_id` | A unique identifier used to resume the correct paused tool or workflow step. | D2 |
| `App` | — | `App` | An ADK application container that ties together agents, services, plugins, and runtime configuration. | D2–D5 |
| `Runner` | — | `Runner` | The ADK runtime object that executes an agent or app and emits events during execution. | D3–D5 |

---

## 3) Sessions, state, and memory terms

| Term | Abbreviation | Full form / expansion | Definition | Relevant day(s) |
|---|---|---|---|---|
| Session | — | Session | The conversation thread that stores the history of an interaction between a user and an agent. | D3, D5 |
| State | — | State | The session-scoped scratchpad used to store dynamic values for the current conversation. | D1, D3 |
| Event | — | Event | A recorded step in execution, such as a message, tool call, output, or state update. | D3, D4 |
| Session service | — | Session service | The backend component responsible for storing and retrieving sessions. | D3 |
| `InMemorySessionService` | — | `InMemorySessionService` | A non-persistent session backend that stores session data only in process memory. | D3 |
| `DatabaseSessionService` | — | `DatabaseSessionService` | A persistent session backend that stores session data in a database such as SQLite. | D3 |
| Memory | — | Memory | Longer-term stored information that can be reused across sessions, such as preferences or facts. | D3, D5 |
| Memory service | — | Memory service | The backend component responsible for storing and retrieving long-term memory. | D3, D5 |
| `add_session_to_memory` | — | `add_session_to_memory` | A method or pattern used to save useful session content into long-term memory. | D3 |
| `load_memory` | — | `load_memory` | A retrieval step that loads relevant memory into the current interaction. | D3 |
| `preload_memory` | — | `preload_memory` | A proactive retrieval step that loads memory before the main agent response begins. | D3 |
| `search_memory` | — | `search_memory` | A direct query against stored memories to find relevant information. | D3 |
| Callback | — | Callback | A function that is automatically triggered at a specific point in a workflow. | D3, D4 |
| `after_agent_callback` | — | `after_agent_callback` | A callback executed after an agent step, often used to save memory automatically. | D3 |
| Context compaction | — | Context compaction | The process of reducing conversation history while preserving the information most needed for future turns. | D3 |
| SQLite | — | SQLite | A lightweight embedded database engine often used in notebooks and local demos. | D3 |
| Structured Query Language | SQL | Structured Query Language | The language used to create, query, and manage relational database data. | D3 |

---

## 4) Observability and evaluation terms

| Term | Abbreviation | Full form / expansion | Definition | Relevant day(s) |
|---|---|---|---|---|
| Observability | — | Observability | The ability to inspect how a system behaves internally by studying logs, traces, events, and related telemetry. | D4, D5 |
| Logging | — | Logging | Recording runtime messages that help developers debug and operate a system. | D4, D5 |
| Plugin | — | Plugin | An extension component that adds custom runtime behavior such as logging or tracing. | D4 |
| `LoggingPlugin` | — | `LoggingPlugin` | A plugin pattern used to capture execution details for debugging and analysis. | D4 |
| `run_debug()` | — | `run_debug()` | An ADK helper used for programmatic debugging and step-by-step inspection of agent runs. | D1, D4 |
| Evaluation | Eval | Evaluation | The structured measurement of agent quality using defined test cases and scoring criteria. | D4, D5 |
| Eval case | — | Evaluation case | A single test scenario containing an input and an expected behavior or scoring rubric. | D4 |
| `response_match_score` | — | `response_match_score` | A metric that checks how well the final answer matches the expected answer or rubric. | D4 |
| `tool_trajectory_avg_score` | — | `tool_trajectory_avg_score` | A metric that checks whether the sequence of tool calls was appropriate. | D4 |
| `test_config.json` | — | `test_config.json` | A JSON file that defines evaluation configuration such as criteria, cases, or settings. | D4 |
| `integration.evalset.json` | — | `integration.evalset.json` | A JSON file that stores evaluation cases for batch testing. | D4 |
| User simulation | — | User simulation | Automated synthetic interactions used to test how an agent behaves across conversations. | D4 |
| Telemetry | — | Telemetry | Diagnostic data emitted by a running system, such as logs, traces, and metrics. | D4, D5 |

---

## 5) Production and agent-to-agent communication terms

| Term | Abbreviation | Full form / expansion | Definition | Relevant day(s) |
|---|---|---|---|---|
| Agent2Agent | A2A | Agent2Agent | An open protocol for discovery, communication, and collaboration between agents. | D5 |
| Agent card | — | Agent Card | A metadata document that describes an A2A agent’s identity, capabilities, and endpoint. | D5 |
| `RemoteA2aAgent` | — | `RemoteA2aAgent` | An ADK client-side object that lets a local system call a remote A2A agent. | D5 |
| `to_a2a()` | — | `to_a2a()` | An ADK helper that exposes an agent as an A2A service. | D5 |
| Endpoint | — | Endpoint | The network address at which an API or remote agent can be reached. | D2, D5 |
| Uvicorn | — | Uvicorn | A Python ASGI server commonly used to serve web apps and agent endpoints. | D5 |
| Agent Engine / Agent Platform Runtime | — | Google Cloud managed agent runtime | The managed Google Cloud runtime used in the course for deploying and scaling agents; newer Google Cloud documentation groups this under the Gemini Enterprise Agent Platform / Agent Platform Runtime naming. | D5 |
| Managed runtime | — | Managed runtime | A hosted execution environment where the cloud provider manages most infrastructure concerns. | D5 |
| Deployment | — | Deployment | The act of making an agent available outside the local development notebook or machine. | D5 |
| Memory Bank | — | Memory Bank | A managed long-term memory capability in Google Cloud’s agent platform for durable user facts and preferences. | D5 |
| Identity and Access Management | IAM | Identity and Access Management | The security model used to control who or what can access deployed agent resources. | D5 |
| Continuous Integration / Continuous Delivery | CI/CD | Continuous Integration / Continuous Delivery | Automated pipelines used to build, test, and release software changes safely. | D5 |

---

## 6) Supporting technical terms used in notebooks

| Term | Abbreviation | Full form / expansion | Definition | Relevant day(s) |
|---|---|---|---|---|
| JavaScript Object Notation | JSON | JavaScript Object Notation | A lightweight text format used for configuration files and structured data exchange. | D2–D5 |
| Hypertext Transfer Protocol | HTTP | Hypertext Transfer Protocol | The protocol used by web clients and servers to exchange requests and responses. | D2, D5 |
| Uniform Resource Locator | URL | Uniform Resource Locator | The address of a web resource, endpoint, or API. | D2, D5 |
| Base64 | — | Base64 encoding | A text-safe encoding used to represent binary data such as images or files. | D2 |
| `async` | — | `async` keyword | A Python keyword used to declare asynchronous functions. | D2–D5 |
| `await` | — | `await` keyword | A Python keyword used to wait for an asynchronous result without blocking the whole program. | D2–D5 |
| Coroutine | — | Coroutine | An asynchronous function execution unit that can be paused and resumed. | D2–D5 |
| Notebook magic | — | Notebook magic command | A Jupyter/Kaggle command such as `!pip install` or `%%writefile` that is handled by the notebook environment rather than normal Python syntax. | D1–D5 |

---

## Quick revision tips
- **Day 1:** focus on **agent, tool, orchestration, workflow, multi-agent system**.
- **Day 2:** focus on **ADK, MCP, FunctionTool, AgentTool, ToolContext, HITL**.
- **Day 3:** focus on **session, state, event, memory, callback, SQLite**.
- **Day 4:** focus on **observability, logging, evaluation, eval case, telemetry**.
- **Day 5:** focus on **A2A, Agent Card, remote agent, deployment, managed runtime**.

---

## Suggested official references
- [Kaggle 5-Day AI Agents Intensive Course](https://www.kaggle.com/learn-guide/5-day-agents)
- [Agent Development Kit (ADK) overview](https://adk.dev/)
- [Google Cloud ADK overview](https://docs.cloud.google.com/gemini-enterprise-agent-platform/build/adk)
- [ADK sessions, state, and memory docs](https://adk.dev/sessions/)
- [ADK evaluation docs](https://adk.dev/evaluate/)
- [Official A2A protocol docs](https://a2aproject.github.io/A2A/)
- [Official MCP introduction](https://modelcontextprotocol.io/docs/getting-started/intro)
- [Google Cloud Agent Platform overview](https://docs.cloud.google.com/gemini-enterprise-agent-platform/overview)

