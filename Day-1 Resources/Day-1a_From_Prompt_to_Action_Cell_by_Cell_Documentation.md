
# Cell-by-Cell Documentation  
## `agentic-ai-day-1a-from-prompt-to-action.ipynb`

This document explains the attached Kaggle notebook **cell by cell**, with emphasis on the **12 executable code cells** and the flow that connects them.

The notebook is the Day 1a exercise from the Google/Kaggle agents course. Its teaching goal is simple but important:

> move from **prompting a model** to building an **agent that can take an action**, specifically by using **Google Search** through ADK.

The notebook contains:

- **35 total cells**
- **23 markdown cells**
- **12 code cells**

---

## What this notebook teaches

At a high level, the notebook walks through this sequence:

1. Prepare the Kaggle runtime.
2. Load a Gemini API key securely.
3. Import the minimum ADK building blocks.
4. Add a Kaggle-specific helper for opening the ADK web UI.
5. Configure retry behavior for API robustness.
6. Define a simple ADK agent.
7. Run the agent in memory with test prompts.
8. Scaffold a file-based ADK project.
9. Launch the ADK web interface for interactive debugging.

A useful mental model for the notebook is:

**Prompt → Agent → Tool decision → External action → Final answer**

---

## Quick dependency map

If you want to understand the execution order before reading each cell, this is the dependency chain:

- **Cell 4** is optional cleanup.
- **Cell 7** must run before any Gemini/ADK model call because it provides `GOOGLE_API_KEY`.
- **Cell 9** must run before **Cell 13** because `types.HttpRetryOptions` is imported there.
- **Cell 13** must run before **Cell 16** because the retry config is passed into the model.
- **Cell 16** must run before **Cell 18** because the runner needs an agent.
- **Cell 18** must run before **Cells 21 and 24** because the agent is invoked through the runner.
- **Cell 11** must run before **Cell 28** because it defines `get_adk_proxy_url()`.
- **Cell 26** and **Cell 28** both must run before **Cell 32** because `adk web` needs a generated agent folder and the Kaggle proxy prefix.

---

## Cell map

| Notebook Cell | Role | Why it exists |
|---|---|---|
| 4 | Cleanup | Clears Kaggle working directory |
| 7 | Authentication | Loads Gemini API key from Kaggle Secrets |
| 9 | Imports | Brings in ADK, Gemini, runner, tool, and SDK types |
| 11 | Helper utilities | Builds a Kaggle-safe proxy URL for ADK Web |
| 13 | Retry config | Makes model/API calls more resilient |
| 16 | Agent definition | Creates the main ADK agent |
| 18 | Runner setup | Creates the runtime wrapper used to execute the agent |
| 21 | First run | Sends the first prompt through the agent |
| 24 | Second run | Reuses the same runner/session for another question |
| 26 | CLI scaffolding | Generates a file-based ADK project |
| 28 | Proxy URL generation | Displays a clickable link for the ADK web UI |
| 32 | Web UI launch | Starts the ADK development server |

---

## Section-by-section explanation

### Before the code starts

The surrounding markdown cells do three things:

- introduce the notebook as **“Your First AI Agent: From Prompt to Action”**
- explain that the notebook will use **ADK**, **Gemini**, and **Google Search**
- introduce the core agent loop:  
  **Prompt → Agent → Thought → Action → Observation → Final Answer**

That framing matters because all later code cells are built around this exact idea: the model is not just answering from memory; it is allowed to **act** through a tool.

---

# Detailed code-cell explanations


## Notebook Cell 4 — Clear the Kaggle working directory

**Section:** Setup

```python
# clearing folder /kaggle/working/
!rm -rf /kaggle/working/*
print("Folder /kaggle/working/ got cleaned.")
```

### What this cell does

This cell deletes everything under `/kaggle/working/` and then prints a confirmation message.

### Line-by-line explanation


- `# clearing folder /kaggle/working/`  
  A plain comment describing the cell’s purpose.

- `!rm -rf /kaggle/working/*`  
  The leading `!` means “run this as a shell command inside the notebook environment.”  
  `rm -rf` force-removes files and folders recursively.  
  In practice, this wipes the notebook’s writable working directory so the rest of the demo starts from a clean state.

- `print("Folder /kaggle/working/ got cleaned.")`  
  Prints a friendly confirmation into notebook output.


### Why this cell matters

It prevents leftover files from earlier runs from interfering with the demo, especially later when the notebook creates the `sample_agent/` project folder.


### Saved output / visible effect

The saved notebook prints a single confirmation line.


```text
Folder /kaggle/working/ got cleaned.
```


### Notes and cautions


- This is **destructive** for anything currently stored in `/kaggle/working/`.
- It is useful in Kaggle, but you would usually remove or modify this in a local development environment.
- The command does **not** delete read-only course files outside the working directory.



## Notebook Cell 7 — Load the Gemini API key from Kaggle Secrets

**Section:** Setup → Configure your Gemini API Key

```python
import os
from kaggle_secrets import UserSecretsClient

try:
    GOOGLE_API_KEY = UserSecretsClient().get_secret("GOOGLE_API_KEY")
    os.environ["GOOGLE_API_KEY"] = GOOGLE_API_KEY
    print("✅ Gemini API key setup complete.")
except Exception as e:
    print(
        f"🔑 Authentication Error: Please make sure you have added 'GOOGLE_API_KEY' to your secrets. Details: {e}"
    )
```

### What this cell does

This cell securely pulls `GOOGLE_API_KEY` from Kaggle Secrets and exposes it as an environment variable for downstream libraries.

### Line-by-line explanation


- `import os`  
  Imports Python’s standard operating-system module so the notebook can set environment variables.

- `from kaggle_secrets import UserSecretsClient`  
  Imports Kaggle’s secret-management helper. This is Kaggle-specific.

- `try:`  
  Starts protected logic so the notebook can fail gracefully if the secret is missing.

- `GOOGLE_API_KEY = UserSecretsClient().get_secret("GOOGLE_API_KEY")`  
  Asks Kaggle Secrets for a secret literally named `GOOGLE_API_KEY`.

- `os.environ["GOOGLE_API_KEY"] = GOOGLE_API_KEY`  
  Stores the key into the environment. This is important because many SDKs and tools automatically look for credentials in environment variables.

- `print("✅ Gemini API key setup complete.")`  
  Confirms success.

- `except Exception as e:`  
  Catches any problem, such as the secret not being configured.

- The final `print(...)` in the `except` block  
  Produces a user-friendly error message explaining what needs to be fixed.


### Why this cell matters

Without this cell, the Gemini-backed ADK agent cannot authenticate. This is the bridge between Kaggle’s secret store and the libraries used later in the notebook.


### Saved output / visible effect

If the secret exists, the notebook prints a success message. If not, it prints an authentication error.


```text
✅ Gemini API key setup complete.
```


### Notes and cautions


- `UserSecretsClient` is a **Kaggle-only** pattern. In local projects you would usually use a `.env` file, a shell export, or a cloud secret manager.
- The code intentionally avoids hardcoding the API key in the notebook source.



## Notebook Cell 9 — Import the ADK pieces used by the notebook

**Section:** Setup → Import ADK components

```python
from google.adk.agents import Agent
from google.adk.models.google_llm import Gemini
from google.adk.runners import InMemoryRunner
from google.adk.tools import google_search
from google.genai import types

print("✅ ADK components imported successfully.")
```

### What this cell does

This cell imports the exact classes and modules needed for the rest of the notebook: the agent class, Gemini model wrapper, in-memory runner, built-in Google Search tool, and Gemini SDK types.

### Line-by-line explanation


- `from google.adk.agents import Agent`  
  Imports the main ADK agent class used in this notebook. In ADK, `Agent` is the LLM-powered agent type used for reasoning and tool use.

- `from google.adk.models.google_llm import Gemini`  
  Imports the Gemini model wrapper used to tell ADK which Gemini model to call.

- `from google.adk.runners import InMemoryRunner`  
  Imports a notebook-friendly runner that executes the agent and manages runtime state in memory.

- `from google.adk.tools import google_search`  
  Imports ADK’s built-in Google Search tool. This is what gives the agent a way to access up-to-date web information.

- `from google.genai import types`  
  Imports type definitions from the Google Gen AI SDK. This notebook uses it next for `HttpRetryOptions`.

- `print("✅ ADK components imported successfully.")`  
  Confirms that all imports worked.


### Why this cell matters

This is the notebook’s dependency checkpoint. If this cell fails, the rest of the notebook cannot proceed because every later cell depends on one or more of these imports.


### Saved output / visible effect

The saved notebook shows a success message after the imports complete.


```text
✅ ADK components imported successfully.
```


### Notes and cautions


- The notebook intentionally imports only a **small set** of ADK components to keep the first example simple.
- `types` is not part of ADK itself; it comes from the underlying Google Gen AI SDK and is used for retry configuration.



## Notebook Cell 11 — Define Kaggle helper functions for the ADK web UI

**Section:** Setup → Helper functions

```python
# Define helper functions that will be reused throughout the notebook

from IPython.core.display import display, HTML
from jupyter_server.serverapp import list_running_servers


# Gets the proxied URL in the Notebooks environment
def get_adk_proxy_url():
    PROXY_HOST = "https://kkb-production.jupyter-proxy.kaggle.net"
    ADK_PORT = "8000"

    servers = list(list_running_servers())
    if not servers:
        raise Exception("No running Jupyter servers found.")

    baseURL = servers[0]["base_url"]

    try:
        path_parts = baseURL.split("/")
        kernel = path_parts[2]
        token = path_parts[3]
    except IndexError:
        raise Exception(f"Could not parse kernel/token from base URL: {baseURL}")

    url_prefix = f"/k/{kernel}/{token}/proxy/proxy/{ADK_PORT}"
    url = f"{PROXY_HOST}{url_prefix}"

    styled_html = f"""
    <div style="padding: 15px; border: 2px solid #f0ad4e; border-radius: 8px; background-color: #fef9f0; margin: 20px 0;">
        <div style="font-family: sans-serif; margin-bottom: 12px; color: #333; font-size: 1.1em;">
            <strong>⚠️ IMPORTANT: Action Required</strong>
        </div>
        <div style="font-family: sans-serif; margin-bottom: 15px; color: #333; line-height: 1.5;">
            The ADK web UI is <strong>not running yet</strong>. You must start it in the next cell.
            <ol style="margin-top: 10px; padding-left: 20px;">
                <li style="margin-bottom: 5px;"><strong>Run the next cell</strong> (the one with <code>!adk web ...</code>) to start the ADK web UI.</li>
                <li style="margin-bottom: 5px;">Wait for that cell to show it is "Running" (it will not "complete").</li>
                <li>Once it's running, <strong>return to this button</strong> and click it to open the UI.</li>
            </ol>
            <em style="font-size: 0.9em; color: #555;">(If you click the button before running the next cell, you will get a 500 error.)</em>
        </div>
        <a href='{url}' target='_blank' style="
            display: inline-block; background-color: #1a73e8; color: white; padding: 10px 20px;
            text-decoration: none; border-radius: 25px; font-family: sans-serif; font-weight: 500;
            box-shadow: 0 2px 5px rgba(0,0,0,0.2); transition: all 0.2s ease;">
            Open ADK Web UI (after running cell below) ↗
        </a>
    </div>
    """

    display(HTML(styled_html))

    return url_prefix


print("✅ Helper functions defined.")
```

<img width="1616" height="356" alt="image" src="https://github.com/user-attachments/assets/8ff6686d-d7cc-4f3e-bd04-8a753fc6beca" />


### What this cell does

This cell defines a reusable helper named `get_adk_proxy_url()` that makes the ADK Web UI accessible from inside Kaggle notebooks.

### Line-by-line explanation


- `from IPython.core.display import display, HTML`  
  Imports display tools so the notebook can render custom HTML directly in output.

- `from jupyter_server.serverapp import list_running_servers`  
  Lets the notebook inspect the currently running Jupyter server metadata.

- `def get_adk_proxy_url():`  
  Defines a function but does **not** execute it yet.

Inside the function:

1. `PROXY_HOST = "https://kkb-production.jupyter-proxy.kaggle.net"`  
   Sets the Kaggle proxy host used for externally reachable notebook services.

2. `ADK_PORT = "8000"`  
   Assumes the ADK web server will run on port 8000.

3. `servers = list(list_running_servers())`  
   Collects information about the active Jupyter server(s).

4. `if not servers: raise Exception(...)`  
   Stops early if no server info is available.

5. `baseURL = servers[0]["base_url"]`  
   Reads the notebook server’s base URL.

6. `path_parts = baseURL.split("/")` followed by extraction of `kernel` and `token`  
   Parses the URL to recover the kernel identifier and auth token embedded in Kaggle’s notebook routing.

7. `url_prefix = f"/k/{{kernel}}/{{token}}/proxy/proxy/{{ADK_PORT}}"`  
   Builds the path prefix that Kaggle’s proxy expects.

8. `url = f"{{PROXY_HOST}}{{url_prefix}}"`  
   Creates the full clickable URL.

9. `styled_html = f"""..."""`  
   Generates a styled warning card in HTML. The HTML explains that the next cell must start the server before the button works.

10. `display(HTML(styled_html))`  
    Renders the warning card and the “Open ADK Web UI” button.

11. `return url_prefix`  
    Returns only the prefix portion, which the CLI uses later in `adk web --url_prefix ...`.

After the function:

- `print("✅ Helper functions defined.")`  
  Confirms the function definition was loaded successfully.


### Why this cell matters

Kaggle notebooks cannot simply expose `localhost:8000` directly to your browser. This helper is the notebook-specific glue that turns a local ADK server into a URL the Kaggle environment can proxy safely.


### Saved output / visible effect

When this cell runs, it only prints a confirmation. The HTML card does not appear until the function is actually called later in Cell 28.


```text
✅ Helper functions defined.
```


### Notes and cautions


- This helper is **specific to the Kaggle notebook environment**. In a local laptop setup, you would usually not need it.
- The function assumes the structure of Kaggle’s Jupyter base URL. If Kaggle changed that routing format, the parser would need to change too.
- The generated link contains authentication information. It should be treated as **sensitive**.



## Notebook Cell 13 — Configure automatic retry behavior

**Section:** Setup → Configure Retry Options

```python
retry_config=types.HttpRetryOptions(
    attempts=5,  # Maximum retry attempts
    exp_base=7,  # Delay multiplier
    initial_delay=1, # Initial delay before first retry (in seconds)
    http_status_codes=[429, 500, 503, 504] # Retry on these HTTP errors
)
print("Code executed succcessfully.")
```

### What this cell does

This cell builds a retry policy object and stores it in `retry_config` so Gemini calls can automatically retry on transient HTTP failures.

### Line-by-line explanation


- `retry_config = types.HttpRetryOptions(...)`  
  Creates an HTTP retry configuration object using the Google Gen AI SDK types module imported earlier.

Parameters used:

- `attempts=5`  
  Allow up to five total tries.

- `exp_base=7`  
  Use exponential backoff. The delay grows with each retry instead of staying fixed.

- `initial_delay=1`  
  Start with a one-second wait before the first retry.

- `http_status_codes=[429, 500, 503, 504]`  
  Retry on:
  - `429` → rate limiting / too many requests
  - `500` → generic server error
  - `503` → service unavailable
  - `504` → gateway timeout

- `print("Code executed succcessfully.")`  
  Confirms the retry object was created.


### Why this cell matters

This cell improves resilience. Model and tool calls sometimes fail for temporary reasons, and retry logic prevents a teaching notebook from failing too easily on a short-lived backend issue.


### Saved output / visible effect

The saved notebook prints a simple success message.


```text
Code executed succcessfully.
```


### Notes and cautions


- Retry logic helps with **transient** failures, not permanent misconfiguration.
- Errors like bad credentials (`401`), invalid requests (`400`), or missing setup will still need manual fixing.



## Notebook Cell 16 — Define the root ADK agent

**Section:** Section 2 → Define your agent

```python
root_agent = Agent(
    name="helpful_assistant",
    model=Gemini(
        model="gemini-2.5-flash-lite",
        retry_options=retry_config
    ),
    description="A simple agent that can answer general questions.",
    instruction="You are a helpful assistant. Use Google Search for current info or if unsure.",
    tools=[google_search]
)

print("✅ Root Agent defined.")
```

### What this cell does

This cell creates the notebook’s main agent and stores it in `root_agent`.

### Line-by-line explanation


- `root_agent = Agent(`  
  Instantiates the ADK LLM agent.

- `name="helpful_assistant"`  
  Gives the agent a stable internal identifier.

- `model=Gemini(...)`  
  Configures the LLM backend for the agent.

Inside `Gemini(...)`:

- `model="gemini-2.5-flash-lite"`  
  Selects the Gemini model variant this notebook wants to use.

- `retry_options=retry_config`  
  Attaches the retry policy from Cell 13 so model calls are more fault-tolerant.

Back in the `Agent(...)` constructor:

- `description="A simple agent that can answer general questions."`  
  Provides a short human-readable summary of what the agent is for.

- `instruction="You are a helpful assistant. Use Google Search for current info or if unsure."`  
  This is the most important behavioral steering string in the notebook.  
  It tells the model:
  - behave like a helpful assistant
  - use Google Search when the answer depends on current information
  - use Google Search when it is uncertain

- `tools=[google_search]`  
  Gives the agent access to one built-in tool: Google Search.

- `print("✅ Root Agent defined.")`  
  Confirms the agent object exists.


### Why this cell matters

This cell is the notebook’s core. Everything before it was setup; everything after it is execution. The agent becomes “agentic” here because it is no longer just a plain model call—it now has a tool and an instruction telling it when to use that tool.


### Saved output / visible effect

The notebook prints a success confirmation after the agent object is created.


```text
✅ Root Agent defined.
```


### Notes and cautions


- The agent uses only one tool: `google_search`. That keeps the first demo simple and also aligns with ADK’s documented limitation that the built-in Google Search tool is typically used by itself within an agent unless specific workarounds are applied.
- The `instruction` is not just descriptive text; it acts like the agent’s behavioral policy.



## Notebook Cell 18 — Create an in-memory runner

**Section:** Section 2 → Run your agent

```python
runner = InMemoryRunner(agent=root_agent)

print("✅ Runner created.")
```

### What this cell does

This cell wraps the agent in an `InMemoryRunner`, which is the runtime object used to execute prompts against the agent.

### Line-by-line explanation


- `runner = InMemoryRunner(agent=root_agent)`  
  Creates an in-memory runtime controller for the previously defined `root_agent`.

- `print("✅ Runner created.")`  
  Confirms that the runtime wrapper was created successfully.


### Why this cell matters

The runner is the execution layer. It is responsible for orchestrating the interaction with the agent, handling session/runtime mechanics, and providing convenient methods like `run_debug()` that the notebook uses next.


### Saved output / visible effect

The notebook prints a confirmation message.


```text
✅ Runner created.
```


### Notes and cautions


- This runner is ideal for notebooks and quick experiments because it avoids persistent infrastructure.
- Because it is in-memory, state is not durable across kernel restarts.



## Notebook Cell 21 — Run the first agent query

**Section:** Section 2 → Run your agent

```python
response = await runner.run_debug(
    "What is Agent Development Kit from Google? What languages is the SDK available in?"
)
```

### What this cell does

This cell sends the first user prompt to the runner and stores the returned result in `response`.

### Line-by-line explanation


- `response = await runner.run_debug(...)`  
  Calls the runner’s asynchronous debug helper.

Key details:

- `await`  
  This works because Jupyter/IPython notebooks support top-level `await`. In a plain Python script, you would usually need an async function and an event loop.

- `runner.run_debug(...)`  
  This is a convenience method aimed at easier prototyping and debugging. It reduces the boilerplate needed to create and manage session details manually.

- Prompt text:  
  `"What is Agent Development Kit from Google? What languages is the SDK available in?"`  
  This prompt is a good first test because it invites the agent to retrieve factual product information.

- `response = ...`  
  Saves the returned object into a Python variable for later use or inspection.


### Why this cell matters

This is the first true end-to-end proof that the notebook works: authentication, model setup, agent configuration, runner setup, and tool availability all come together here.


### Saved output / visible effect

The saved notebook output shows that a new debug session was created and that the agent answered with a grounded summary of ADK and its SDK languages.


```text
### Created new session: debug_session_id

User > What is Agent Development Kit from Google? What languages is the SDK available in?
helpful_assistant > The Google Agent Development Kit (ADK) is an open-source framework designed to simplify the creation, deployment, and orchestration of AI agents and multi-agent systems. It applies software development principles to AI agent creation, offering flexibility and precise control over agent behavior and orchestration. The ADK is optimized for Google's Gemini models and ecosystem but is model-agnostic, deployment-agnostic, and compatible with other frameworks. It provides a structured way for developers to compose agents that can interact with tools, communicate with each other, and reason about their actions.

The ADK SDK is available in the following programming languages:
*   Python
*   Go
*   Java
*   TypeScript

There is also an ADK for Web, which serves as a developer UI for easier development and debugging.
```


### Notes and cautions


- The exact wording of the answer can vary from run to run because LLM output is non-deterministic.
- Even though the notebook stores the result in `response`, the important learning value here is the visible execution trace printed by `run_debug()`.



## Notebook Cell 24 — Run a second prompt in the same debug flow

**Section:** Section 2 → Your Turn

```python
response = await runner.run_debug("What's the weather in Kolkata?")
```

### What this cell does

This cell sends a second question to the same runner, again using `run_debug()`, and overwrites `response` with the new result.

### Line-by-line explanation


- `response = await runner.run_debug("What's the weather in Kolkata?")`  
  Sends a current-information question to the same runner.

Important things to notice:

- The variable `response` is reused.  
  The first response is replaced by the second one.

- The question is intentionally time-sensitive.  
  This is exactly the kind of prompt that encourages the agent to use Google Search.

- Because the runner is reused, the debug flow continues from the same notebook session context rather than creating a brand-new agent definition.


### Why this cell matters

This cell proves the notebook’s main teaching point: the agent is useful not only for static knowledge questions, but also for questions where the model should go out to the web for fresh information.


### Saved output / visible effect

The saved output explicitly says `Continue session: debug_session_id`, showing that the notebook is continuing the same debug flow. The weather text in the saved notebook is only an example snapshot and will change when rerun later.


```text
### Continue session: debug_session_id

User > What's the weather in Kolkata?
helpful_assistant > The weather in Kolkata is currently mostly sunny with a temperature of 94°F (34°C). The "feels like" temperature is 106°F (41°C) due to 51% humidity.

The forecast for today, Saturday, April 18, 2026, predicts mostly sunny skies during the day and clear at night, with a 15% chance of rain. The temperature is expected to range between 80°F (27°C) and 96°F (36°C), with humidity around 58%. Another source indicates a high of 35°C and a low of 27°C, with a feels-like temperature of 37°C and wind speeds around 11 km/h. For the upcoming week, temperatures are expected to remain high, generally in the high 90s Fahrenheit (around 36-38°C), with mostly sunny or partly cloudy conditions and a low chance of rain.
```


### Notes and cautions


- Because the question is about weather, the answer is **time-sensitive** and will change whenever the notebook is rerun.
- Reusing `response` is fine in a demo, but in a real notebook you might keep separate variables such as `response_1` and `response_2` if you want to compare outputs later.



## Notebook Cell 26 — Scaffold a file-based ADK project from the CLI

**Section:** Section 3 → Try the ADK Web Interface

```python
!adk create sample_agent --model gemini-2.5-flash-lite --api_key $GOOGLE_API_KEY
```

### What this cell does

This cell uses the ADK command-line tool to generate a new agent project folder named `sample_agent` inside Kaggle’s working directory.

### Line-by-line explanation


- `!adk create sample_agent --model gemini-2.5-flash-lite --api_key $GOOGLE_API_KEY`  
  Runs the ADK CLI from the notebook shell.

Breakdown:

- `adk create sample_agent`  
  Creates a new agent project folder called `sample_agent`.

- `--model gemini-2.5-flash-lite`  
  Preconfigures the generated project to use the same Gemini model variant chosen earlier in the notebook.

- `--api_key $GOOGLE_API_KEY`  
  Injects the API key stored in the environment.  
  The `$GOOGLE_API_KEY` part is shell syntax, not Python syntax.

Why this is needed:

- The earlier cells defined an agent **inline inside the notebook**.
- The ADK Web UI works with a **file-based project structure**.
- This command bridges that gap by generating the minimal project files ADK expects.


### Why this cell matters

The notebook now shifts from “inline demo code” to “real ADK project structure.” That is what makes the next web-based development/debugging step possible.


### Saved output / visible effect

The saved output shows the generated files inside `/kaggle/working/sample_agent/`.


```text
/usr/local/lib/python3.12/dist-packages/google/cloud/aiplatform/models.py:52: FutureWarning: Support for google-cloud-storage < 3.0.0 will be removed in a future version of google-cloud-aiplatform. Please upgrade to google-cloud-storage >= 3.0.0.
  from google.cloud.aiplatform.utils import gcs_utils
[32m
Agent created in /kaggle/working/sample_agent:
- .env
- __init__.py
- agent.py
[0m
```


### Notes and cautions


- The warning about `google-cloud-storage` in the saved output is a **future warning**, not a fatal failure.
- The generated `.env` file is part of the scaffolded project structure.
- The current working directory matters here because the generated folder appears under `/kaggle/working/`.



## Notebook Cell 28 — Generate the Kaggle proxy URL and display the ADK Web button

**Section:** Section 3 → Try the ADK Web Interface

```python
url_prefix = get_adk_proxy_url()
```

<img width="1616" height="356" alt="image" src="https://github.com/user-attachments/assets/8ff6686d-d7cc-4f3e-bd04-8a753fc6beca" />

### What this cell does

This cell calls the helper function defined earlier, stores the resulting URL prefix in `url_prefix`, and displays a styled HTML button inside the notebook output.

### Line-by-line explanation


- `url_prefix = get_adk_proxy_url()`  
  Calls the helper from Cell 11.

Two things happen at once:

1. **Return value**  
   The function returns a path fragment like `/k/.../proxy/proxy/8000`, which is saved into `url_prefix`.

2. **Side effect**  
   The function also renders a block of HTML explaining how to open the ADK Web UI correctly.

Why the side effect is useful:

- It gives the learner a clickable button.
- It warns them that the server is **not running yet**.
- It tells them to start the next cell first, otherwise the button leads to a `500` error.


### Why this cell matters

This is the notebook’s bridge between backend setup and user interaction. It transforms a local notebook-only server into something the Kaggle browser session can open safely.


### Saved output / visible effect

The saved output is an HTML info card with a warning and a button. The link is sensitive because it contains notebook-routing/authentication information.


```text
(Rendered HTML card with an 'Open ADK Web UI' button; the notebook stores the path prefix in `url_prefix`.)
```


### Notes and cautions


- The button will not work until Cell 32 starts the server.
- The generated URL should **not be shared**, because it contains notebook-specific access routing details.



## Notebook Cell 32 — Launch the ADK web interface

**Section:** Section 3 → Try the ADK Web Interface

```python
!adk web --url_prefix {url_prefix}
```

### What this cell does

This cell starts the ADK development web server and uses the Kaggle-specific proxy prefix so the UI is reachable from the browser.

### Line-by-line explanation


- `!adk web --url_prefix {url_prefix}`  
  Runs the ADK web server through the shell.

Breakdown:

- `adk web`  
  Starts ADK’s browser-based development/debugging interface.

- `--url_prefix {url_prefix}`  
  Passes in the Kaggle proxy path computed in Cell 28 so the web UI works through Kaggle’s routing layer instead of exposing plain localhost.

Runtime behavior:

- The command starts a server on port 8000.
- The notebook cell keeps running because the server stays alive.
- You then go back to the button from Cell 28 and open the UI.
- When finished, you interrupt the notebook cell to shut down the server.


### Why this cell matters

This is the final interactive step. It moves the learner from script-style execution to a browser-based ADK development experience where they can chat with the agent, inspect sessions, and debug behavior.


### Saved output / visible effect

The saved logs show the server starting, binding to `127.0.0.1:8000`, serving the `sample_agent` app, and then shutting down after an interrupt (`^C`).


```text
/usr/local/lib/python3.12/dist-packages/google/cloud/aiplatform/models.py:52: FutureWarning: Support for google-cloud-storage < 3.0.0 will be removed in a future version of google-cloud-aiplatform. Please upgrade to google-cloud-storage >= 3.0.0.
  from google.cloud.aiplatform.utils import gcs_utils
INFO:google_adk.google.adk.cli.utils.service_factory:Using in-memory memory service
INFO:google_adk.google.adk.cli.utils.local_storage:Using per-agent session storage rooted at /kaggle/working
INFO:google_adk.google.adk.cli.utils.local_storage:Using file artifact service at /kaggle/working/.adk/artifacts
/usr/local/lib/python3.12/dist-packages/google/adk/cli/fast_api.py:141: UserWarning: [EXPERIMENTAL] InMemoryCredentialService: This feature is experimental and may change or be removed in future versions without notice. It may introduce breaking changes at any time.
  credential_service = InMemoryCredentialService()
/usr/local/lib/python3.12/dist-packages/google/adk/auth/credential_service/in_memory_credential_service.py:33: UserWarning: [EXPERIMENTAL] BaseCredentialService: This feature is experimental and may change or be removed in future versions without notice. It may introduce bre
...
```


### Notes and cautions


- This cell is **blocking by design**. It usually stays running until you stop it manually.
- ADK documentation describes the web interface as a **development/debugging** tool, not a production deployment mechanism.
- The command must be run from the **parent directory that contains the generated agent folder**, which this notebook satisfies because `sample_agent/` was created under `/kaggle/working/`.



---

## End-to-end logic of the notebook

Now that the cell-by-cell details are clear, the full logic is:

1. Clean the temporary Kaggle workspace.
2. Retrieve a Gemini API key securely.
3. Import ADK + Gemini + Google Search pieces.
4. Define a Kaggle-specific helper for proxied web access.
5. Configure retry behavior for reliability.
6. Create a Gemini-powered ADK agent with one tool: Google Search.
7. Wrap the agent in an in-memory runner.
8. Ask a factual question to see the agent respond.
9. Ask a time-sensitive question to see why tool use matters.
10. Generate a standalone ADK project on disk.
11. Compute a browser-safe proxy URL for Kaggle.
12. Launch the ADK web UI and interact with the generated project.

---

## What each major object represents

### `GOOGLE_API_KEY`
The credential needed to call Gemini.

### `retry_config`
A reusable retry policy that gets attached to the Gemini model wrapper.

### `root_agent`
The notebook’s main ADK agent.  
This object contains the agent identity, its model, its instruction, and its tools.

### `runner`
The runtime object that actually executes the agent and handles the conversation flow.

### `response`
A variable that holds the result of the latest `run_debug()` call.

### `sample_agent/`
A generated file-based ADK project used by the web interface.

### `url_prefix`
A Kaggle-specific routing path that lets a local ADK server be opened from the browser.

---

## Most important learning takeaways

### 1. The agent becomes “agentic” only when you give it a tool
Before Cell 16, you mostly have setup code.  
At Cell 16, the notebook gives the agent a tool (`google_search`) and an instruction telling it when to use that tool.

### 2. Instructions matter as much as the tool itself
The line:

```python
instruction="You are a helpful assistant. Use Google Search for current info or if unsure."
```

is crucial.  
Without that instruction, the model may be less likely to use the search tool appropriately.

### 3. `InMemoryRunner` is a fast notebook-friendly runtime
It reduces setup friction so learners can focus on the agent concept first.

### 4. There are two styles of working in the notebook
- **Inline notebook style** → Cells 16, 18, 21, 24  
- **Project + web UI style** → Cells 26, 28, 32

Both are valuable:
- the first is easier for understanding
- the second is closer to real development workflow

### 5. Kaggle requires special routing for local web apps
The helper function in Cell 11 is not “agent logic”; it is environment plumbing that makes the development UI usable inside Kaggle.

---

## Common pitfalls and how to interpret them

### Missing secret error in Cell 7
Cause: `GOOGLE_API_KEY` was not added to Kaggle Secrets.  
Fix: add the secret with the exact same name.

### Import error in Cell 9
Cause: runtime/environment mismatch.  
Fix: confirm the Kaggle environment includes the expected ADK and Google Gen AI packages.

### Retry config cell works but model call still fails
Cause: retry logic only helps with temporary HTTP failures, not permanent setup issues like invalid credentials.

### Clicking the web button before starting Cell 32
Cause: the server is not yet running.  
Result: often a `500` or similar load failure.

### Cell 32 never “finishes”
That is normal. It is running a live web server.

### Weather or search answers differ from the saved output
That is also normal. Search-grounded and time-sensitive results change over time.

### Cell 4 deleted something you wanted
That is possible. It force-clears `/kaggle/working/`.

---

## Minimal run paths

### If you only want the core agent demo
Run:

- Cell 7
- Cell 9
- Cell 13
- Cell 16
- Cell 18
- Cell 21  
Optional: Cell 24

### If you want the ADK web UI demo too
Also run:

- Cell 11
- Cell 26
- Cell 28
- Cell 32

Cell 4 remains optional cleanup.

---

## Final summary

This notebook is a very focused first-agent exercise.

It starts with environment preparation, defines a single Gemini-based ADK agent, gives that agent the Google Search tool, and then demonstrates two execution modes:

1. **run the agent directly in the notebook with `InMemoryRunner`**
2. **launch a browser-based ADK Web UI from a generated project folder**

The most important conceptual shift the notebook teaches is this:

- a plain LLM answers from its internal model state
- an ADK agent can decide to **use a tool**
- that tool lets the system pull in fresh external information before producing the final answer

That is the core jump from **prompting** to **action**.

---

## Official reference links used to interpret the notebook

These are the main official references that match the notebook’s building blocks:

- [ADK overview](https://adk.dev/)
- [ADK Agents](https://adk.dev/agents/)
- [ADK LLM Agents](https://adk.dev/agents/llm-agents/)
- [ADK Python Quickstart](https://adk.dev/get-started/python/)
- [ADK Web Interface](https://adk.dev/runtime/web-interface/)
- [ADK Apps and `run_debug()` example](https://adk.dev/apps/)
- [Gemini model integration in ADK](https://adk.dev/agents/models/google-gemini/)
- [Google Search tool for ADK](https://adk.dev/integrations/google-search/)
- [Google Search grounding in ADK](https://adk.dev/grounding/google_search_grounding/)
- [Google Gen AI Python SDK docs](https://googleapis.github.io/python-genai/)

