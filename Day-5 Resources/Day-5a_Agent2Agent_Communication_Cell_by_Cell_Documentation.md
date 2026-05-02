# Day 5a — Agent2Agent Communication with ADK  
## Cell-by-Cell Documentation for `agentic-ai-day-5a-agent2agent-communication.ipynb`

This document explains the uploaded notebook **cell by cell**. It is written so you can keep it in a GitHub repository as a technical walkthrough of the notebook.

---

## 1) What this notebook is teaching

This notebook demonstrates a full **agent-to-agent integration** pattern:

1. Build a normal ADK agent (`product_catalog_agent`)
2. Expose it over the network using **A2A** with `to_a2a()`
3. Run that exposed agent as a small web service with **Uvicorn**
4. Read its **agent card**
5. Create a second agent (`customer_support_agent`) that consumes the first one through `RemoteA2aAgent`
6. Execute the customer-support agent through an ADK `Runner`

The central idea is that a remote agent can be consumed almost like a local agent once ADK has the remote agent card and the A2A connection details.

---

## 2) Notebook statistics

| Item | Value |
|---|---:|
| Total cells | 32 |
| Markdown cells | 20 |
| Code cells | 12 |
| Executed code cells in saved notebook | 10 |
| Unexecuted code cells in saved notebook | 2 |
| Main concepts | `LlmAgent`, function tools, `to_a2a()`, agent cards, `RemoteA2aAgent`, `Runner`, `InMemorySessionService`, `run_async()` |

---

## 3) High-level architecture

```text
Customer
   |
   v
customer_support_agent (local ADK LlmAgent)
   |
   | delegates to sub-agent
   v
remote_product_catalog_agent (RemoteA2aAgent client/proxy)
   |
   | A2A over HTTP
   v
product_catalog_server (Uvicorn + A2A app)
   |
   v
product_catalog_agent (remote ADK LlmAgent)
   |
   v
get_product_info() tool
```

### Why this matters

This pattern is useful when:

- another team owns the remote agent,
- the remote agent lives in another codebase,
- the remote agent may be written in another language or framework,
- you want a clean network boundary between agents.

---

## 4) Important notebook-specific observations

These are not conceptual issues with A2A itself. They are specific to the **saved notebook snapshot** you uploaded.

### A. Cell 6 shows a setup failure in the saved output
The notebook tries to load `GOOGLE_API_KEY` from Kaggle secrets. In the saved output, that lookup fails with:

```text
🔑 Authentication Error: Please make sure you have added 'GOOGLE_API_KEY' to your Kaggle secrets. Details: Connection error trying to communicate with service.
```

So the notebook snapshot was saved in an environment where Kaggle secret retrieval did not succeed.

### B. Cell 23 fails for the same root cause
Later, the notebook calls the customer-support agent, but the LLM backend still has no valid credentials. The saved output ends with:

```text
ValueError: Missing key inputs argument! To use the Google AI API, provide (`api_key`) arguments.
```

So the A2A structure is correct, but the run cannot complete because the Gemini credentials are missing.

### C. Cells 25 and 26 were never executed
The two extra demo queries exist, but the notebook snapshot shows no execution count for those cells.

### D. There is a small editorial mismatch in the notebook text
The narrative in the later markdown explanation mentions an example about **iPhone 15 Pro**, but the actual first test query in the executable code asks about **iPhone 14 Pro**. That looks like a notebook write-up mismatch, not a code bug.

---

## 5) Core concepts you need before reading the cells

### `LlmAgent`
This is the ADK agent class used for both the product catalog agent and the customer support agent.

### Function tools
The notebook turns a normal Python function into a tool by placing it in the agent's `tools=[...]` list.

### `to_a2a()`
This wraps an existing ADK agent as an A2A-compatible app and auto-generates an agent card.

### Agent card
This is the machine-readable description of the remote agent: name, description, skills, endpoint URL, protocol metadata, and related capability information.

### `RemoteA2aAgent`
This is the consumer-side proxy. It lets one ADK agent call another remote agent by reading its agent card.

### `Runner` + session service
The notebook uses `Runner` and `InMemorySessionService` to execute the customer support agent in a proper ADK session context.

---

## 6) Cell-by-cell walkthrough

---

### Cell 0 — Markdown  
**Content:** Copyright notice.

**Purpose:**  
Purely legal/header content. It does not affect execution.

---

### Cell 1 — Markdown  
**Title:** `Agent2Agent (A2A) Communication with ADK`

**Purpose:**  
This is the notebook’s conceptual introduction. It explains:

- why multi-agent systems need inter-agent communication,
- what problem A2A solves,
- what you will build,
- the difference between a standalone agent and a network-accessible agent.

**Why it matters:**  
It frames the notebook as a **distributed agent** example, not just a local multi-agent example. That distinction is important because in Day 1 and much of Day 2, agents mostly collaborate inside one process. Here, the collaboration crosses a process boundary and a network boundary.

---

### Cell 2 — Markdown  
**Title:** `Get started with Kaggle Notebooks`

**Purpose:**  
This is Kaggle onboarding guidance:

- verify your Kaggle account,
- make your own editable copy,
- run cells in order.

**Why it matters:**  
The notebook assumes you can execute cells and attach secrets. Without a personal editable copy and Kaggle secrets, the demo will not work.

---

### Cell 3 — Markdown  
**Title:** `Setup`

**Purpose:**  
Section divider. Signals that the next few cells prepare the environment before the A2A example begins.

---

### Cell 4 — Markdown  
**Title:** `Install dependencies`

**Purpose:**  
Explains that Kaggle already includes `google-adk`, and that a local environment would typically install A2A support using:

```bash
pip install -q google-adk[a2a]
```

**Why it matters:**  
A2A support is not just “plain ADK.” The notebook depends on the A2A extras as well.

---

### Cell 5 — Markdown  
**Title:** `Configure your Gemini API Key`

**Purpose:**  
Explains the notebook’s authentication dependency:

- generate a Gemini API key in Google AI Studio,
- store it in Kaggle secrets,
- expose it to the notebook as `GOOGLE_API_KEY`.

**Why it matters:**  
Both the remote product-catalog agent and the local support agent use Gemini-backed `LlmAgent`s. If the API key is missing, the notebook structure still builds, but the actual model call fails later.

---

### Cell 6 — Code  
```python
import os
from kaggle_secrets import UserSecretsClient

try:
    GOOGLE_API_KEY = UserSecretsClient().get_secret("GOOGLE_API_KEY")
    os.environ["GOOGLE_API_KEY"] = GOOGLE_API_KEY
    print("✅ Setup and authentication complete.")
except Exception as e:
    print(
        f"🔑 Authentication Error: Please make sure you have added 'GOOGLE_API_KEY' to your Kaggle secrets. Details: {e}"
    )
```

#### What this cell does
This cell retrieves the secret named `GOOGLE_API_KEY` from Kaggle’s secret store and puts it into `os.environ`, so downstream ADK / Gemini code can read it.

#### Why it is written this way
- `UserSecretsClient()` is Kaggle’s notebook mechanism for secure secret retrieval.
- Setting `os.environ["GOOGLE_API_KEY"]` makes the key available to libraries that expect the environment variable instead of a directly passed parameter.
- The `try/except` avoids an immediate notebook crash and prints a human-readable diagnostic instead.

#### What happened in the saved notebook
The saved output shows the secret retrieval failed.

#### Why this matters later
The notebook also launches a subprocess in Cell 16. That subprocess inherits the parent environment. So if this cell succeeds, the remote server can also use the same API key. If this cell fails, both the local and remote agents are under-credentialed.

---

### Cell 7 — Markdown  
**Title:** `Import ADK components`

**Purpose:**  
Transition cell that explains the next code cell will import the libraries needed for A2A and agent execution.

---

### Cell 8 — Code  
```python
import json
import requests
import subprocess
import time
import uuid

from google.adk.agents import LlmAgent
from google.adk.agents.remote_a2a_agent import (
    RemoteA2aAgent,
    AGENT_CARD_WELL_KNOWN_PATH,
)

from google.adk.a2a.utils.agent_to_a2a import to_a2a
from google.adk.models.google_llm import Gemini
from google.adk.runners import Runner
from google.adk.sessions import InMemorySessionService
from google.genai import types

import warnings
warnings.filterwarnings("ignore")
```

#### What this cell imports
- `json` — pretty-print the agent card
- `requests` — hit the remote HTTP endpoint
- `subprocess` — launch Uvicorn in the background
- `time` — polling wait loop
- `uuid` — unique session IDs for repeated tests

ADK-specific imports:
- `LlmAgent` — both agents are built with this
- `RemoteA2aAgent` — remote-agent client proxy
- `AGENT_CARD_WELL_KNOWN_PATH` — standard agent-card path suffix
- `to_a2a` — expose an ADK agent over A2A
- `Gemini` — model backend
- `Runner` — runtime executor
- `InMemorySessionService` — lightweight session manager for demos
- `types` — request/retry/content helpers from `google.genai`

#### Why `warnings.filterwarnings("ignore")` is used
The notebook hides non-critical warnings so the output stays clean. This is a demo-friendly choice, but in production you usually want to inspect warnings rather than silence them globally.

#### Output
The cell prints:
```text
✅ ADK components imported successfully.
```

#### Why this cell matters
This is the first cell that tells you the notebook is not just explaining A2A conceptually. It is going to:
- expose one agent,
- consume it from another agent,
- run it through the ADK runtime.

---

### Cell 9 — Markdown  
**Title:** `Configure Retry Options`

**Purpose:**  
Introduces the next cell, which defines retry behavior for Gemini requests.

**Why it matters:**  
LLM calls can fail transiently due to rate limits or temporary service instability. Retry logic helps make demos and integrations less brittle.

---

### Cell 10 — Code  
```python
retry_config = types.HttpRetryOptions(
    attempts=5,
    exp_base=7,
    initial_delay=1,
    http_status_codes=[429, 500, 503, 504],
)
```

#### What this cell does
Creates a retry policy object for HTTP-backed model requests.

#### Parameter meaning
- `attempts=5` — total retry attempts
- `exp_base=7` — exponential backoff multiplier
- `initial_delay=1` — initial wait before retrying
- `http_status_codes=[429, 500, 503, 504]` — only retry on these transient-ish failures

#### Why it matters
This retry config is later passed into both Gemini model definitions:
- the product catalog agent
- the customer support agent

So both agents use the same resiliency behavior.

#### Practical note
Retry logic does **not** fix missing credentials. It only helps if the request is valid but temporarily rejected or unavailable.

---

### Cell 11 — Markdown  
**Title:** `Section 1: Create the Product Catalog Agent (To Be Exposed)`

**Purpose:**  
Introduces the remote agent that will later be exposed via A2A.

**Important conceptual point:**  
This notebook treats the product catalog agent as if it belongs to an **external vendor**. That is why A2A makes sense here: you want to consume the capability without directly embedding or rewriting the vendor’s implementation.

---

### Cell 12 — Code  
```python
def get_product_info(product_name: str) -> str:
    ...
product_catalog_agent = LlmAgent(
    model=Gemini(model="gemini-2.5-flash-lite", retry_options=retry_config),
    name="product_catalog_agent",
    description="External vendor's product catalog agent that provides product information and availability.",
    instruction="...",
    tools=[get_product_info],
)
```

#### What this cell does
This is one of the most important cells in the notebook. It does two things:

1. Defines `get_product_info(product_name: str) -> str`
2. Builds `product_catalog_agent`, an `LlmAgent` that uses that function as a tool

#### Part 1: `get_product_info()`
This function is the mock database layer.

It stores a Python dictionary called `product_catalog` with several product records:
- iPhone 15 Pro
- Samsung Galaxy S24
- Dell XPS 15
- MacBook Pro 14
- Sony WH-1000XM5
- iPad Air
- LG UltraWide 34

The function:
- normalizes the incoming product name to lowercase,
- looks it up in the dictionary,
- returns a formatted string if found,
- otherwise returns a fallback message plus the list of supported products.

#### Why this function matters
This is the actual capability the remote agent exposes. The remote agent is not directly querying a database here; instead it uses this function tool as a stand-in for a vendor-owned product catalog.

#### Part 2: `product_catalog_agent`
This agent is configured with:
- a Gemini model,
- a name and description,
- an instruction block,
- the tool `get_product_info`.

#### Why the tool is attached in `tools=[get_product_info]`
In ADK, putting a normal typed Python function in the `tools` list lets the framework expose it as a tool the agent can call.

#### Why the instruction text matters
The instruction tells the LLM:
- it is a product catalog specialist,
- it must use the tool for product lookups,
- it should include price, stock, and specs,
- it should handle multiple products if asked.

This instruction is what turns a raw LLM into a specialized catalog agent.

#### Output
The cell prints that the agent was created successfully and is ready to be exposed via A2A.

---

### Cell 13 — Markdown  
**Title:** `Section 2: Expose the Product Catalog Agent via A2A`

**Purpose:**  
Explains what `to_a2a()` is about to do.

**Key ideas introduced here:**
- wrap an agent as an A2A-compatible server,
- auto-generate an agent card,
- expose the well-known card endpoint,
- let other agents discover and call it.

**Why this markdown matters:**  
This is the point where the notebook shifts from **building an agent** to **publishing an agent**.

---

### Cell 14 — Code  
```python
product_catalog_a2a_app = to_a2a(
    product_catalog_agent, port=8001
)
```

#### What this cell does
It converts `product_catalog_agent` into an A2A-compatible application object.

#### What `to_a2a()` gives you
Conceptually, this wrapper does the heavy lifting required to expose the agent over A2A:
- creates the server-side A2A application,
- wires the protocol handling,
- auto-generates an agent card from agent metadata and exposed skills,
- prepares the app to be served on a web server.

#### Why `port=8001` is used
This notebook uses:
- `8001` for the remote catalog agent server
- the local notebook process for the rest of the demo

Keeping the exposed agent on a dedicated port simulates a real separate service.

#### Important subtle point
The object returned by `to_a2a()` is **not yet running**. It is only an application object. You still need a server process to host it. That happens in Cell 16.

#### Output
The notebook prints the intended service URL and agent-card URL:
- `http://localhost:8001`
- `http://localhost:8001/.well-known/agent-card.json`

---

### Cell 15 — Markdown  
**Title:** `Section 3: Start the Product Catalog Agent Server`

**Purpose:**  
Explains that the next cell will actually launch the remote server in the background.

**Why it matters:**  
A2A needs a network-accessible remote agent. Until a server is running, `RemoteA2aAgent` has nothing to connect to.

---

### Cell 16 — Code  
This is the longest operational cell in the notebook.

#### Core idea
The notebook must run `uvicorn module:app ...`. Uvicorn expects an importable Python module containing the ASGI app object. Because the remote catalog agent only exists inside notebook memory, the notebook first writes a standalone Python file to `/tmp/product_catalog_server.py`.

#### What the cell does step by step

##### Step 1 — Build a Python source string
It creates a triple-quoted string named `product_catalog_agent_code` that contains:
- the retry config,
- the `get_product_info()` function,
- the `product_catalog_agent`,
- the `app = to_a2a(product_catalog_agent, port=8001)` line.

This recreates the remote agent in a plain Python module.

##### Step 2 — Write that source to disk
```python
with open("/tmp/product_catalog_server.py", "w") as f:
    f.write(product_catalog_agent_code)
```

Why this is necessary:
- Notebook cells are not directly importable as `module:app`
- Uvicorn works most naturally with a real module path

##### Step 3 — Start Uvicorn in the background
```python
server_process = subprocess.Popen([...], cwd="/tmp", stdout=subprocess.PIPE, stderr=subprocess.PIPE, env={**os.environ})
```

This launches:
```bash
uvicorn product_catalog_server:app --host localhost --port 8001
```

Important details:
- `cwd="/tmp"` makes the generated file importable as `product_catalog_server`
- `stdout` and `stderr` are piped so the notebook output stays uncluttered
- `env={**os.environ}` passes the environment to the subprocess, including `GOOGLE_API_KEY` if Cell 6 succeeded

##### Step 4 — Poll the server until it responds
The cell repeatedly requests:
```text
http://localhost:8001/.well-known/agent-card.json
```

If the request returns HTTP 200, it concludes the server is ready.

##### Step 5 — Save the process handle globally
```python
globals()["product_catalog_server_process"] = server_process
```

This makes it possible to stop or inspect the process later from another cell if needed.

#### Why this cell is clever
The notebook simulates a real microservice-style remote agent **inside a notebook** by:
- serializing code to a temp file,
- launching a web server process,
- polling until it is live.

That is exactly the kind of engineering detail that turns a demo idea into a working distributed-agent example.

#### Output
The saved output shows:
- the file was written,
- the server started,
- the poll loop succeeded,
- the endpoint is live.

---

### Cell 17 — Markdown  
**Title:** `View the Auto-Generated Agent Card`

**Purpose:**  
Introduces inspection of the metadata document produced by `to_a2a()`.

**Why it matters:**  
The agent card is the discovery surface of the remote agent. Other agents use it to learn:
- who this agent is,
- what it can do.

---

### Cell 18 — Code  
```python
response = requests.get("http://localhost:8001/.well-known/agent-card.json", timeout=5)
...
agent_card = response.json()
print(json.dumps(agent_card, indent=2))
```

#### What this cell does
This is a manual verification step:
- fetch the agent card,
- parse it as JSON,
- pretty-print it,
- print a short human summary.

#### Why this cell exists
Even though `RemoteA2aAgent` will later consume the card automatically, it is helpful to inspect the card directly so you can understand what A2A discovery looks like.

#### What the saved output shows
The card includes fields like:
- `name`
- `description`
- `url`
- `protocolVersion`
- `preferredTransport`
- `skills`
- default input/output modes

#### Especially important: `skills`
In the saved snapshot, the card exposes **2 skills**:
1. the model-backed agent capability
2. the `get_product_info` tool capability

This is a concrete example of how the remote agent’s abilities become visible to consumers.

#### Practical note
Some card fields can vary across ADK / A2A versions. If you re-run this notebook in a different environment, metadata details such as protocol version may differ.

---

### Cell 19 — Markdown  
**Title:** `Section 4: Create the Customer Support Agent (Consumer)`

**Purpose:**  
Introduces the consumer side of the A2A relationship.

**Core idea:**  
The notebook will now build a local agent that can ask the remote catalog agent for product information without directly embedding the vendor logic.

---

### Cell 20 — Code  
```python
remote_product_catalog_agent = RemoteA2aAgent(
    name="product_catalog_agent",
    description="Remote product catalog agent from external vendor that provides product information.",
    agent_card=f"http://localhost:8001{AGENT_CARD_WELL_KNOWN_PATH}",
)
```

#### What this cell does
Creates a **client-side proxy** for the remote A2A agent.

#### Why the constructor fields matter
- `name` — local ADK name for this remote agent object
- `description` — helps the parent agent decide when to delegate to it
- `agent_card=...` — points to the remote agent-card URL, which is the discovery entry point

#### Why `AGENT_CARD_WELL_KNOWN_PATH` is used
This constant avoids hardcoding the path string in multiple places and keeps the code aligned with ADK’s expected well-known card path.

#### Conceptual meaning
This object is not the remote agent itself. It is a local proxy that knows how to:
- fetch and interpret the remote card,
- translate delegation into A2A requests,
- pass the remote result back into ADK’s local execution flow.

#### Why this is the key Day 5a idea
Once this proxy exists, the rest of your code can treat a remote agent much like a local sub-agent. That is the ergonomic advantage A2A + ADK is trying to provide.

---

### Cell 21 — Code  
```python
customer_support_agent = LlmAgent(
    model=Gemini(model="gemini-2.5-flash-lite", retry_options=retry_config),
    name="customer_support_agent",
    description="A customer support assistant that helps customers with product inquiries and information.",
    instruction="...",
    sub_agents=[remote_product_catalog_agent],
)
```

#### What this cell does
Creates the **consumer/orchestrator agent** that will answer customer questions and delegate catalog lookups to the remote product-catalog agent.

#### Why `sub_agents=[remote_product_catalog_agent]` matters
This is the delegation connection.

The support agent itself does not know product inventory details. Instead, it is told:
1. use the `product_catalog_agent` sub-agent,
2. get the product facts first,
3. then produce the customer-facing answer.

#### Why this is a sub-agent and not a normal tool
A tool usually exposes a bounded function call.
A sub-agent exposes another agent’s reasoning/capability boundary.

In this notebook:
- the remote catalog side is modeled as another agent,
- so the support agent delegates to it as a sub-agent.

#### Instruction behavior
The instruction block makes the support agent:
- friendly and professional,
- reliant on the product-catalog sub-agent for product facts,
- responsible for phrasing the final user answer.

This is a common orchestration pattern:
- specialist agent gets the facts,
- front-line agent handles UX and response tone.

#### Output
The notebook prints that the support agent is ready and has one remote sub-agent.

---

### Cell 22 — Markdown  
**Title:** `Section 5: Test A2A Communication`

**Purpose:**  
Explains that the notebook is now going to execute the multi-agent workflow.

**Why it matters:**  
This is where the notebook switches from configuration to actual runtime behavior.

---

### Cell 23 — Code  
This cell defines and immediately calls:

```python
async def test_a2a_communication(user_query: str):
    ...
await test_a2a_communication("Can you tell me about the iPhone 14 Pro? Is it in stock?")
```

#### What this function does
It wraps the entire execution pattern for one query.

#### Step-by-step breakdown

##### 1. Create a session service
```python
session_service = InMemorySessionService()
```
This provides lightweight in-memory session storage for the demo.

##### 2. Define runtime identifiers
```python
app_name = "supporter_app"
user_id = "demo_user"
session_id = f"demo_session_{uuid.uuid4().hex[:8]}"
```

Why unique `session_id` matters:
- avoids collisions across repeated test runs,
- prevents one test conversation from reusing another test’s state.

##### 3. Create the session
```python
session = await session_service.create_session(...)
```

This is required before the runner executes the agent.

##### 4. Create the runner
```python
runner = Runner(
    agent=customer_support_agent,
    app_name=app_name,
    session_service=session_service
)
```

The runner is the execution engine that:
- loads the session,
- sends the user message into the runtime,
- yields events as the agent works.

##### 5. Build the user message
```python
test_content = types.Content(parts=[types.Part(text=user_query)])
```

This wraps raw user text into the structured content format expected by ADK.

##### 6. Run the agent asynchronously
```python
async for event in runner.run_async(...):
```

This is the actual invocation point. During execution:
- the support agent receives the message,
- decides it needs catalog information,
- delegates to the remote agent,
- the remote agent uses its tool,
- the result returns,
- the support agent produces a final answer.

##### 7. Filter only final responses
```python
if event.is_final_response() and event.content:
    ...
```

The runner can emit intermediate events. The notebook chooses to print only the final user-facing response.

#### Why this cell is important
This is the full runtime bridge between:
- the ADK session system,
- the support agent,
- the remote A2A agent,
- the remote product tool,
- and the final printed answer.

#### What was supposed to happen
The query asks about **iPhone 14 Pro**, which is **not in the mock catalog**. So if credentials were configured correctly, the likely logical outcome would be:
- the remote catalog agent fails to find iPhone 14 Pro,
- it returns the fallback “not found” message plus available products,
- the support agent reformulates that into a customer-friendly answer.

#### What actually happened in the saved notebook
The call failed with a `ValueError` because no usable Gemini credentials were available.

#### Important subtle point
The A2A architecture was already set up correctly by this stage. The failure here is not because `RemoteA2aAgent` is wrong or because the remote server failed to start. The failure is because the model backend could not authenticate.

---

### Cell 24 — Markdown  
**Title:** `Try More Examples`

**Purpose:**  
Introduces two additional demo calls.

**Why it matters:**  
It shows the test helper is reusable: the same setup function can be called repeatedly with different user prompts.

---

### Cell 25 — Code  
```python
await test_a2a_communication(
    "I'm looking for a laptop. Can you compare the Dell XPS 15 and MacBook Pro 14 for me?"
)
```

#### What this cell is intended to test
A multi-product comparison flow.

#### Why this is a good A2A test
The product-catalog agent instruction explicitly says it can handle multiple products. So this query tests:
- delegation to the remote agent,
- multiple product lookups,
- synthesis back into one customer-friendly comparison.

#### Saved notebook state
This cell was not executed in the uploaded notebook snapshot.

#### Expected logical result if run successfully
The support agent should:
- delegate to the remote catalog agent,
- obtain details for both products,
- compare price, stock, and specifications,
- present a structured answer.

---

### Cell 26 — Code  
```python
await test_a2a_communication(
    "Do you have the Sony WH-1000XM5 headphones? What's the price?"
)
```

#### What this cell is intended to test
A simple single-product lookup for an item that **does exist** in the mock catalog.

#### Why this is useful
This is the “happy path” example:
- one product,
- one remote lookup,
- one direct factual answer.

#### Saved notebook state
This cell was also not executed in the uploaded snapshot.

#### Expected logical result if run successfully
The remote product agent should return the Sony headphones record, and the support agent should present the price and stock availability cleanly.

---

### Cell 27 — Markdown  
**Title:** `Understanding What Just Happened`

**Purpose:**  
This is the notebook’s conceptual postmortem.

It explains:
- the A2A flow,
- the role of `RemoteA2aAgent`,
- the network hop to the remote service,
- why the pattern is good for cross-organization integration.

**Why this markdown is useful:**  
It converts the demo into a general reusable design pattern:
- support agent stays local to your company,
- catalog agent belongs to an external vendor,
- A2A becomes the contract between them.

---

### Cell 28 — Markdown  
**Title:** `Next Steps and Learning Resources`

**Purpose:**  
Suggests practical extensions:
- more remote agents,
- real databases,
- authentication,
- production deployment.

**Why it matters:**  
It shows that this notebook is intentionally the **minimum working A2A example**, not the final production architecture.

---

### Cell 29 — Markdown  
**Content:** Summary separator + key takeaways.

**Purpose:**  
Condenses the notebook into four ideas:
- A2A protocol
- exposing agents
- consuming agents
- typical use cases

**Why it matters:**  
This is a good short recap section for README readers.

---

### Cell 30 — Markdown  
**Content:** Congratulations / learn more / next steps.

**Purpose:**  
Wrap-up cell with official references and encouragement to explore deployment next.

**Why it matters:**  
It connects Day 5a to the broader production/deployment story in Day 5.

---

### Cell 31 — Markdown  
**Content:** Author attribution.

**Purpose:**  
Credits the notebook author.

---

## 7) End-to-end execution flow in plain English

If the notebook is configured correctly, the runtime flow is:

1. The notebook loads the Gemini key.
2. A product-catalog agent is created.
3. `to_a2a()` wraps it as an A2A app.
4. Uvicorn serves that app on port 8001.
5. A local support agent is created.
6. A `RemoteA2aAgent` points at the remote card URL.
7. The support agent treats that remote agent as a sub-agent.
8. A user question is sent to the support agent using `Runner.run_async()`.
9. The support agent delegates the catalog portion of the task to the remote agent.
10. The remote catalog agent uses `get_product_info()`.
11. The result comes back through A2A.
12. The support agent writes the final customer-facing reply.

---

## 8) What the notebook teaches technically

### A. Exposing an agent is a separate concern from defining the agent
You first define `product_catalog_agent`, then expose it using `to_a2a()`.

### B. A remote agent still needs its own runtime
The exposed app must be served by an actual web server. In this notebook that server is Uvicorn.

### C. Agent cards are the discovery layer
The consumer side does not just “call a URL blindly.” It begins from the remote agent card.

### D. The consumer agent stays simple
The customer-support agent just delegates to a sub-agent. ADK hides most of the network complexity.

### E. Proper execution still needs Runner + sessions
Even though the notebook is small, the agent is still executed through the same ADK runtime primitives you use elsewhere.

---

## 9) Common errors and fixes

### Problem: `GOOGLE_API_KEY` not found
**Symptom:** Cell 6 prints the Kaggle secret retrieval error.  
**Fix:** Add the secret in Kaggle Secrets and make sure it is attached to the notebook.

### Problem: `ValueError: Missing key inputs argument`
**Symptom:** Cell 23 fails at model invocation time.  
**Fix:** Ensure Cell 6 succeeded and the environment variable is present before launching the server and before running the test query.

### Problem: remote server never becomes ready
**Symptom:** Cell 16 keeps printing dots and eventually warns the server may not be ready.  
**Fixes:**
- inspect `server_process.stderr`,
- confirm port `8001` is free,
- confirm the generated file `/tmp/product_catalog_server.py` was written correctly,
- confirm dependencies are present.

### Problem: agent card fetch fails
**Symptom:** Cell 18 cannot fetch `/.well-known/agent-card.json`.  
**Fix:** The Uvicorn server from Cell 16 is not reachable yet or failed to start.

### Problem: product not found
**Symptom:** the user asks about something outside the mock catalog.  
**Fix:** Not a bug. The tool intentionally returns a fallback message with available products.

---

## 10) Why the temp-file pattern in Cell 16 is used

A notebook can hold Python objects in memory, but `uvicorn` typically launches an ASGI app using the pattern:

```bash
uvicorn some_module:app
```

That means:
- `some_module` must be importable,
- `app` must exist as a top-level object in that module.

Because notebook cells are not importable modules in the same way, the notebook writes an actual Python file into `/tmp` and then starts Uvicorn against that file.

This is a very practical notebook engineering trick.

---

## 11) Short concept glossary

### A2A
An open protocol for agent-to-agent communication.

### Agent card
The discovery document that describes an agent’s identity, URL, and capabilities.

### `to_a2a()`
ADK helper that wraps an agent as an A2A app.

### `RemoteA2aAgent`
Local ADK proxy for a remote A2A-served agent.

### `Runner`
ADK execution engine that runs the agent and yields events.

### Session
The conversation/runtime context associated with a user and a session ID.

### Function tool
A normal Python function exposed to the agent as callable capability.

---

## 12) What to remember from this notebook

If you remember only five things, remember these:

1. **A2A is for remote agent collaboration**, not just local orchestration.
2. **`to_a2a()` publishes** an ADK agent as a remote service.
3. **Agent cards are how remote agents are discovered.**
4. **`RemoteA2aAgent` lets the local agent consume the remote one almost like a local sub-agent.**
5. **Authentication still matters** even when the architecture is correct.

---

## 13) Recommended improvements if you productionize this notebook

- replace the mock product dictionary with a real database or service layer,
- store structured product data instead of long formatted strings,
- add explicit error handling around remote timeouts and network failures,
- surface server logs instead of piping them silently,
- add authentication between agents,
- add observability and evaluation,
- deploy the remote agent to a real environment instead of `localhost`,
- persist sessions beyond memory if conversations need continuity.

---

## 14) How this notebook maps to the official ADK / A2A model

This notebook lines up closely with the official architecture:

- **Expose phase:** `to_a2a()` wraps an existing ADK agent so it can be served over the network and discovered by other agents.
- **Discovery phase:** the remote service publishes an **agent card**, which acts as the machine-readable description of the agent.
- **Consume phase:** `RemoteA2aAgent` uses that agent card to create a local proxy object for the remote capability.
- **Execution phase:** `Runner.run_async()` executes the local orchestration agent inside a session, while ADK handles delegation and remote communication under the hood.
- **Interoperability boundary:** the notebook is showing **agent-to-agent** communication. This is different from **agent-to-tool** communication; in the current ecosystem, A2A and MCP are complementary rather than competing standards.

In other words, the notebook is not inventing a custom remote-call pattern. It is demonstrating the exact expose → discover → consume → run lifecycle that the A2A model is designed for.


## 15) References and official resources

- [Kaggle Day 5a notebook — Agent2Agent Communication](https://www.kaggle.com/code/kaggle5daysofai/day-5a-agent2agent-communication)
- [Kaggle 5-Day Agents course guide](https://www.kaggle.com/learn-guide/5-day-agents)
- [ADK A2A overview](https://adk.dev/a2a/)
- [ADK Introduction to A2A](https://adk.dev/a2a/intro/)
- [ADK quickstart — exposing a remote agent via A2A](https://adk.dev/a2a/quickstart-exposing/)
- [ADK quickstart — consuming a remote agent via A2A](https://adk.dev/a2a/quickstart-consuming/)
- [ADK LLM agents documentation](https://adk.dev/agents/llm-agents/)
- [ADK runtime event loop](https://adk.dev/runtime/event-loop/)
- [ADK custom function tools](https://adk.dev/tools-custom/function-tools/)
- [A2A protocol official documentation](https://a2a-protocol.org/latest/)
- [A2A agent discovery / agent cards](https://a2a-protocol.org/latest/topics/agent-discovery/)
- [A2A specification](https://a2a-protocol.org/latest/specification/)

---

## 16) Final takeaway

This notebook is a compact but real demonstration of **distributed agent architecture**.

It shows that the jump from:
- “one agent with tools”
to
- “multiple agents that communicate across a network”

is mainly about three things:

1. **publishing** the specialist agent,
2. **discovering** it through an agent card,
3. **consuming** it through a remote-agent proxy.

That is the heart of Day 5a.
