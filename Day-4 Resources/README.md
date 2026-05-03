# Day 4 Documentation — Agent Quality

> A GitHub-ready study document for **Day 4** of Kaggle’s **5-Day AI Agents Intensive Course with Google**.

> **Scope note:** This document follows the official Day 4 course flow — the Day 4 guide, the **Agent Quality** whitepaper, the companion podcast, and the two codelabs **Day 4a — Agent Observability** and **Day 4b — Agent Evaluation**.  
> Where useful, it also maps the course ideas to the current ADK documentation so the notebook lessons stay practical outside Kaggle.

---

## Table of Contents

- [1. Day 4 Goal](#1-day-4-goal)
- [2. What Day 4 Includes](#2-what-day-4-includes)
- [3. Why Agent Quality Matters](#3-why-agent-quality-matters)
- [4. The Day 4 Mental Model](#4-the-day-4-mental-model)
- [5. The Core Day 4 Vocabulary](#5-the-core-day-4-vocabulary)
- [6. Foundations from the Whitepaper](#6-foundations-from-the-whitepaper)
- [7. The Four Pillars of Agent Quality](#7-the-four-pillars-of-agent-quality)
- [8. The Outside-In Evaluation Strategy](#8-the-outside-in-evaluation-strategy)
- [9. Day 4a — Agent Observability](#9-day-4a--agent-observability)
- [10. What Day 4a Actually Teaches You](#10-what-day-4a-actually-teaches-you)
- [11. Logs, Traces, and Metrics](#11-logs-traces-and-metrics)
- [12. The Research Paper Finder Debugging Exercise](#12-the-research-paper-finder-debugging-exercise)
- [13. Development Debugging with ADK Web](#13-development-debugging-with-adk-web)
- [14. Production Observability with LoggingPlugin](#14-production-observability-with-loggingplugin)
- [15. Callbacks, Plugins, and Custom Monitoring](#15-callbacks-plugins-and-custom-monitoring)
- [16. Day 4b — Agent Evaluation](#16-day-4b--agent-evaluation)
- [17. What Day 4b Actually Teaches You](#17-what-day-4b-actually-teaches-you)
- [18. Interactive Evaluation in the ADK Web UI](#18-interactive-evaluation-in-the-adk-web-ui)
- [19. Evaluation Files Explained](#19-evaluation-files-explained)
- [20. The Main Metrics Used on Day 4](#20-the-main-metrics-used-on-day-4)
- [21. Batch Regression Testing](#21-batch-regression-testing)
- [22. User Simulation](#22-user-simulation)
- [23. LLM-as-a-Judge, Agent-as-a-Judge, and Human Review](#23-llm-as-a-judge-agent-as-a-judge-and-human-review)
- [24. Safety, Guardrails, and Interruptions](#24-safety-guardrails-and-interruptions)
- [25. The Agent Quality Flywheel](#25-the-agent-quality-flywheel)
- [26. Common Mistakes to Avoid](#26-common-mistakes-to-avoid)
- [27. Day 4 Revision Checklist](#27-day-4-revision-checklist)
- [28. Short Glossary](#28-short-glossary)
- [29. Resources Reviewed](#29-resources-reviewed)

---

## 1. Day 4 Goal

Day 4 is about one big transition:

**from agents that seem to work**  
to  
**agents you can observe, evaluate, and improve systematically**

Days 1 to 3 teach you how to build agents, wire tools, and manage context.  
Day 4 teaches you how to answer the harder question:

> **How do I know this agent is actually good?**

By the end of Day 4, you should be able to explain:

1. why **agent quality** is not the same as traditional software testing
2. how **observability** helps you see inside an agent run
3. how **evaluation** helps you proactively measure agent behavior over time
4. why the **trajectory** matters, not just the final answer
5. how to run **regression testing** on an agent
6. how to combine **automation, human review, and safety checks** into a quality workflow

---

## 2. What Day 4 Includes

Kaggle structures Day 4 around the same study pattern used in earlier units.

### Concept resources
- the **Day 4 course guide**
- the **“Agent Quality” whitepaper**
- the **summary podcast for Unit 4**

### Hands-on resources
- **Day 4a — Agent Observability**
- **Day 4b — Agent Evaluation**

### Reference resources
- ADK documentation for:
  - **logging**
  - **observability**
  - **evaluation**
  - **evaluation criteria**
  - **user simulation**
  - **callbacks**
  - **plugins**

### Best study order
1. read the Day 4 overview
2. listen to the summary podcast
3. read the whitepaper
4. run **Day 4a**
5. run **Day 4b**
6. revisit the ADK docs and connect the course ideas to the framework pieces

---

## 3. Why Agent Quality Matters

Traditional software often fails in obvious ways:

- exceptions
- crashes
- missing fields
- broken endpoints

Agents fail in more subtle ways:

- they choose the wrong tool
- they use the right tool with the wrong arguments
- they interpret a correct tool result incorrectly
- they answer fluently but incorrectly
- they eventually succeed, but waste time, tokens, and calls
- they behave differently after small prompt changes
- they drift as memory, tools, or prompts evolve

That is why the Day 4 whitepaper makes a major point:

**agent quality is an architectural concern, not a final testing step.**

If you only inspect the final response, you miss the most important information:

- what the agent planned
- which tool it chose
- how it parameterized that tool
- what the tool returned
- whether it understood the tool response
- how many steps it took
- whether it stayed safe and aligned

This is the main Day 4 insight:

> **For agents, the process is part of the product.**

---

## 4. The Day 4 Mental Model

A compact way to remember Day 4 is:

### Observability = reactive visibility
It helps you understand **what happened during a specific run** and **why it happened**.

### Evaluation = proactive measurement
It helps you ask **whether the agent still behaves correctly across many scenarios**.

### Together = continuous quality improvement
Observability gives you the diagnostic data.  
Evaluation gives you the repeated measurement.  
Combined, they create a feedback loop.

Another simple shorthand from the notebook flow is:

- **Observability** helps after or during a failure
- **Evaluation** helps catch degradation before users notice it

Or even shorter:

> **Observability answers “why did this run fail?”**  
> **Evaluation answers “is this system getting worse?”**

---

## 5. The Core Day 4 Vocabulary

Before reading the notebooks, you should be comfortable with these terms.

### Agent quality
A broad measure of whether the agent is useful, efficient, reliable, and safe.

### Observability
The ability to inspect the agent’s internal behavior through logs, traces, and metrics.

### Log
A record of a single event at a particular moment.

### Trace
A connected story of execution across multiple steps, tool calls, and model invocations.

### Span
A single timed unit within a trace, such as one model call or one tool call.

### Metric
A summary number derived from system behavior, such as latency, error rate, or success rate.

### Trajectory
The full path the agent took from user request to final outcome.  
This includes reasoning, tool use, intermediate steps, and final response.

### Eval case
One saved scenario used as a benchmark for future comparison.

### Eval set
A collection of eval cases, often used for batch or regression testing.

### Regression
A case where a change makes a previously working behavior worse.

### Tool trajectory score
A metric that checks whether the agent used the expected tools with the expected parameters and order.

### Response match score
A metric that checks how close the final response is to the expected response.

### LLM-as-a-Judge
Using a model to assess quality dimensions such as helpfulness, rubric adherence, or comparison outcomes.

### Agent-as-a-Judge
Using an agent or structured evaluator to inspect and score the full execution process.

### Human-in-the-Loop (HITL)
Using human review for nuanced, subjective, domain-specific, or high-stakes judgments.

### User simulation
Using an LLM-generated simulated user to create more dynamic conversations than fixed test prompts allow.

---

## 6. Foundations from the Whitepaper

The Day 4 whitepaper explains why agent evaluation is fundamentally different from older evaluation methods.

### 6.1 From model-centric to system-centric AI

A traditional model is often evaluated as a component.  
An agent must be evaluated as a **system**.

That system now includes:

- the model
- planning
- tools
- memory
- retrieval
- orchestration
- the external environment
- possibly other agents

That means the primary evaluation unit is not just an answer.  
It is the **system trajectory**.

### 6.2 Why “testing the output” is not enough

A passive LLM can already be hard to evaluate because its output is probabilistic.  
An active agent becomes harder because it can:

- plan over multiple steps
- call tools
- react to external state
- remember information
- adapt behavior over time

The result is non-determinism across the whole workflow, not just the final sentence.

### 6.3 The key shift: from verification to validation

Traditional testing often asks:

> Did the system meet the specification?

Day 4 asks a broader question:

> Did the agent create real value while staying efficient, reliable, and safe?

That is why the whitepaper emphasizes **validation** more than narrow verification.

### 6.4 “Trajectory is the truth”

This is one of the most important ideas in the whole unit.

A final answer might look acceptable even when the internal process was poor.

Examples:

- the answer is correct, but the tool path was wasteful
- the answer is correct, but it came from hallucinated reasoning
- the answer is correct, but a dangerous tool call almost happened
- the answer is wrong, but the real problem was not the final generation — it was a bad tool parameter

So the correct mental model is:

> **The final answer is only the visible surface.  
> The trajectory tells you what really happened.**

---

## 7. The Four Pillars of Agent Quality

The whitepaper frames agent quality through four connected pillars.

| Pillar | What it asks | Practical examples |
|---|---|---|
| **Effectiveness** | Did the agent achieve the user’s real goal? | task success, correctness, completeness |
| **Efficiency** | How much effort and cost did it take? | latency, token use, extra tool calls, step count |
| **Robustness** | How well does it handle ambiguity, errors, and messy reality? | retries, clarification behavior, graceful failure |
| **Safety & Alignment** | Did it stay within allowed and trustworthy behavior? | refusal behavior, policy adherence, safe tool use |

### 7.1 Effectiveness
This is the most user-facing pillar.  
It asks whether the agent actually solved the user’s problem.

Examples:
- Did the booking agent reserve the correct flight?
- Did the analysis agent produce the correct insight?
- Did the device-control agent perform the requested action?

### 7.2 Efficiency
An agent that succeeds after many unnecessary loops is still a lower-quality agent.

Efficiency is often visible through:
- total number of steps
- total latency
- token consumption
- number of failed or repeated tool calls

### 7.3 Robustness
Real-world environments are noisy. APIs fail, prompts are ambiguous, and retrieved context can be imperfect.

A robust agent:
- asks clarifying questions when necessary
- handles missing information
- retries when appropriate
- reports limitations honestly
- degrades gracefully rather than bluffing

### 7.4 Safety & Alignment
This is the non-negotiable pillar.

The agent must:
- stay within policy boundaries
- avoid harmful or unauthorized actions
- resist prompt injection and unsafe inputs
- avoid leaking private data
- remain a trustworthy proxy for the developer or organization

---

## 8. The Outside-In Evaluation Strategy

The whitepaper recommends an **Outside-In** hierarchy.

### Stage 1: Start with the black box
First ask whether the agent achieved the user’s goal.

At this level, useful signals include:
- task success rate
- user satisfaction
- correctness
- completeness
- business KPIs

This keeps evaluation anchored to value, not just internal technical details.

### Stage 2: Open the box
If performance is weak, then inspect the internal process.

This is the “glass box” or “inside-out” view.

You look at:
- planning quality
- tool selection
- tool parameterization
- interpretation of tool outputs
- retrieval quality
- efficiency
- robustness
- multi-agent communication

### Why this matters
Without this hierarchy, teams often get lost in local metrics and forget the user-facing objective.

A simple way to remember the strategy is:

1. **Did it work?**
2. **If not, where in the trajectory did it go wrong?**

### A useful Day 4 rule
Do not start by obsessing over component metrics in isolation.  
Start with whether the user goal was achieved, then drill inward only when you need diagnosis.

---

## 9. Day 4a — Agent Observability

Day 4a is the first practical half of the unit.

Its focus is not “how to build a better agent” directly.  
Its focus is:

> **How do I look inside an agent run clearly enough to debug it?**

The notebook teaches observability through a deliberately broken example rather than an ideal one.

That choice is important.

You do not really learn observability by staring at a perfect run.  
You learn it by tracing a failure from symptom to cause.

---

## 10. What Day 4a Actually Teaches You

The notebook walks through five practical lessons.

### 10.1 Logging setup matters
You configure DEBUG-level logging and write logs to local files like `logger.log`.  
That gives you raw visibility into requests, responses, and tool behavior.

### 10.2 The ADK web UI is a debugging tool
You launch `adk web --log_level DEBUG` and inspect execution visually through traces and events.

### 10.3 Broken examples are ideal for learning
The notebook creates a “Research Paper Finder” agent with an intentional bug so you can practice real debugging.

### 10.4 Traces beat guesswork
Instead of asking “Why did the agent fail?” in the abstract, you inspect the exact step where the failure happened.

### 10.5 Production needs a different strategy
The notebook then moves beyond local debugging and shows how to use `LoggingPlugin()` and custom plugins/callbacks for production observability.

That final shift is easy to miss, but it is one of the most valuable parts of Day 4a.

---

## 11. Logs, Traces, and Metrics

The observability chapter and notebook revolve around three pillars.

### 11.1 Logs
Logs are the atomic records.

They tell you:
- what event occurred
- when it occurred
- what data was attached to that event

Examples:
- model request sent
- model response returned
- tool call started
- tool call completed
- error raised
- user message received

Good logs are structured and machine-readable.  
They are the factual diary of the run.

### 11.2 Traces
A trace connects the logs into one execution story.

Instead of isolated events, you get:
- the causal chain
- the order of steps
- parent/child relationships
- the path from input to final response

If logs tell you **what** happened, traces tell you **why** it happened.

This is crucial for agents because multiple model/tool steps interact.

### 11.3 Metrics
Metrics summarize behavior at scale.

Examples include:
- latency
- error rate
- tokens per task
- API cost per run
- task completion rate
- correctness
- helpfulness

Metrics do not replace logs and traces.  
They compress them into operational signals.

### 11.4 The practical difference

| Signal type | Best for | Typical question |
|---|---|---|
| **Logs** | detailed event inspection | what happened at this step? |
| **Traces** | causal debugging | why did this run end up here? |
| **Metrics** | monitoring trends | how well is the system doing overall? |

### 11.5 A simple memory trick
- **Logs** = diary  
- **Traces** = story  
- **Metrics** = scoreboard

---

## 12. The Research Paper Finder Debugging Exercise

This is the core hands-on story in Day 4a.

### 12.1 The agent architecture
The notebook creates a research-paper-finding workflow with:

- a root agent
- a Google Search sub-agent
- a `count_papers` tool

At a high level:
1. the root agent receives the user request
2. it delegates search to the Google Search agent
3. it passes the search result to the paper-counting tool
4. it returns an answer to the user

### 12.2 The deliberate bug
The notebook intentionally creates a mismatch around the `papers` argument.

The visible symptom is that the agent does not behave as expected when asked to find papers.

The underlying issue is a **data-shape/type problem**:
the papers payload is being passed in the wrong form, and the trace reveals that the argument is treated like a string-shaped payload instead of the list-shaped structure the tool logic expects.

### 12.3 Why this exercise is good
This is a realistic agent failure.

Nothing here is exotic:
- the tool exists
- the model exists
- the architecture exists
- the failure is in how information moves between steps

That is exactly the kind of issue observability is supposed to help you solve.

---

## 13. Development Debugging with ADK Web

The Day 4a notebook teaches a very specific debugging flow.

### 13.1 Start the web UI in debug mode

```bash
adk web --log_level DEBUG
```

This gives you maximum visibility during development.

### 13.2 Reproduce the failure
Run a concrete prompt through the broken agent.

Example pattern:
- ask for research papers on a topic
- observe the bad behavior
- avoid immediately guessing at the cause

### 13.3 Inspect the trace
Open the trace for that run and find the relevant model span.

The notebook specifically teaches you to inspect the `call_llm` span tied to the `count_papers` step.

### 13.4 Open the events view
Look at:
- the function call
- the tool arguments
- the shape of the `papers` argument
- the surrounding model response

This is the moment where the real bug becomes visible.

### 13.5 Fix the interface, not the symptom
The lesson is not “change random prompt wording until it works.”  
The lesson is:

1. identify the exact step that failed
2. inspect the real payload
3. fix the contract between components

### 13.6 Use local DEBUG logs too
The notebook also shows local debugging through `logger.log`.

This is useful when you want:
- a raw textual view
- a saved artifact for review
- a way to debug without relying only on the UI

### 13.7 The hidden Day 4a lesson
The notebook’s most important debugging pattern is:

> **symptom → logs → root cause → fix**

That pattern is more important than the particular broken example.

---

## 14. Production Observability with LoggingPlugin

The notebook does not stop at development debugging.

It makes an important distinction:

### Development observability
Use:
- `adk web`
- `--log_level DEBUG`
- local log files
- manual inspection of traces and events

This is excellent for:
- local iteration
- notebook learning
- one-off debugging

### Production observability
Use:
- `LoggingPlugin()`
- structured logs
- reusable monitoring hooks
- centralized collection pipelines

The notebook explains that `LoggingPlugin()` automatically captures:
- user messages and agent responses
- timing data
- LLM requests and responses
- tool calls and tool results
- complete execution traces

### Example pattern

```python
from google.adk.runners import InMemoryRunner
from google.adk.plugins.logging_plugin import LoggingPlugin

runner = InMemoryRunner(
    agent=my_agent,
    plugins=[LoggingPlugin()],
)
```

### Why this matters
An agent that only works when a developer manually opens a debug UI is not production-ready.

Production systems need:
- repeatable instrumentation
- persistent logs
- scalable monitoring
- fewer manual steps

### The notebook’s decision rule
A very useful summary from Day 4a is:

1. **Development debugging** → use `adk web --log_level DEBUG`
2. **Common production observability** → use `LoggingPlugin()`
3. **Custom requirements** → build callbacks or plugins

That three-level mental model is worth remembering exactly.

---

## 15. Callbacks, Plugins, and Custom Monitoring

This section is where Day 4a becomes more architectural.

### 15.1 Callbacks
Callbacks are lifecycle hooks.

They let you run logic:
- before a model call
- after a model call
- before a tool call
- after a tool call
- before or after agent-level execution

They are the mechanism.

### 15.2 Plugins
Plugins package cross-cutting behavior into reusable modules.

Typical plugin use cases:
- logging and tracing
- monitoring and metrics
- policy enforcement
- request/response modification
- caching
- safety checks

They are the reusable module built on top of hooks.

### 15.3 Why this distinction matters
A callback is a hook point.  
A plugin is a packaged behavior that uses those hook points.

That difference matters when moving from experiments to reusable infrastructure.

### 15.4 Common callback patterns relevant to Day 4
The ADK docs highlight patterns such as:
- logging and monitoring
- policy and guardrail enforcement
- dynamic state management
- caching
- request/response modification

These are all Day 4 themes because quality is not only about scoring.  
It is also about **instrumentation** and **control**.

### 15.5 The Day 4a extension exercise
The notebook asks you to imagine a custom plugin that counts total tool calls in a session.

That is a strong example because it shows the transition from:
- passive debugging
to
- purposeful quality instrumentation

### 15.6 A practical rule
If you need observability logic in one notebook once, a callback may be enough.  
If you need it across many agents and environments, a plugin is usually the better design.

---

## 16. Day 4b — Agent Evaluation

Day 4b is the proactive half of the unit.

If Day 4a says:

> “See inside the run.”

Day 4b says:

> “Now measure the agent continuously so quality does not drift.”

The notebook explicitly frames evaluation as the complement to observability.

A concise Day 4b formula is:

**Observability + Evaluation = reactive + proactive quality**

That is probably the single best one-line summary of the notebook.

---

## 17. What Day 4b Actually Teaches You

The notebook uses a **home automation agent** with deliberate flaws.

That choice is important.

The goal is not to show a perfect agent.  
The goal is to show how evaluation exposes hidden weakness.

### 17.1 Build a flawed home automation agent
The agent has a `set_device_status` tool and an instruction that is intentionally too confident.  
It claims broader ability than it should.

This sets up the evaluation problems the notebook wants you to discover.

### 17.2 Create an initial successful case
You interact with the agent in the ADK web UI using a normal request such as turning on a lamp in a room.

Then you save that interaction as an evaluation case.

### 17.3 Run evaluation in the web UI
You run the saved case and inspect pass/fail status.

This shows how to treat a good interaction as a benchmark for future versions.

### 17.4 Understand the two main metrics
The notebook centers on:
- **Response Match Score**
- **Tool Trajectory Score**

### 17.5 Intentionally create failures
You edit the expected final response or create more difficult scenarios to see how the evaluation framework catches problems.

### 17.6 Move beyond the web UI
The notebook then explains why evaluating one conversation at a time does not scale and transitions to:
- `test_config.json`
- `integration.evalset.json`
- `adk eval`

### 17.7 Learn regression thinking
The real lesson is not just “how to run a command.”

It is how to turn agent behavior into a regression-tested system.

### 17.8 Explore user simulation
Finally, the notebook introduces dynamic evaluation with generated user prompts rather than only fixed test cases.

---

## 18. Interactive Evaluation in the ADK Web UI

This is the first evaluation workflow you should understand.

### 18.1 Why start in the web UI
The web UI is the easiest place to:
- talk to the agent naturally
- save good interactions
- run quick comparisons
- inspect failure diffs

It is the bridge between manual testing and formal evaluation.

### 18.2 Typical Day 4b workflow
1. start `adk web`
2. talk to the agent normally
3. create an evaluation set
4. add the current session as a case
5. run evaluation
6. inspect pass/fail and score details

### 18.3 What you learn from the first pass
The first saved case often passes because it reflects a scenario the agent handled well.

That is fine.  
The point is to establish a baseline.

### 18.4 Why the notebook then breaks the expectation
The notebook has you intentionally modify expectations or create more difficult prompts.

This shows two important realities:
- failing cases are useful
- passing a simple case does **not** prove the agent is broadly reliable

### 18.5 The value of side-by-side failure inspection
One especially useful part of the web UI workflow is the ability to compare:
- actual response
- expected response
- tool path

That makes evaluation actionable instead of abstract.

---

## 19. Evaluation Files Explained

Day 4b introduces three important file ideas.

### 19.1 `*.test.json`
This is commonly used for saved or focused evaluation cases.  
Think of it as a compact benchmark case or a small collection of benchmark interactions.

Use it when:
- you want to preserve a known-good case
- you want a quick regression target
- you are still working at a relatively small scale

### 19.2 `*.evalset.json`
This is a larger evaluation dataset used for batch runs.

The Day 4b notebook uses `integration.evalset.json` to collect multiple scenarios.

Use it when:
- you want multiple cases together
- you want batch regression testing
- you want better coverage than one saved conversation

A conceptual shape looks like this:

```json
{
  "eval_set_id": "my_suite",
  "eval_cases": [
    {
      "eval_id": "case_1",
      "conversation": [
        {
          "user_content": {"parts": [{"text": "Turn on the lamp"}]},
          "final_response": {"parts": [{"text": "Lamp turned on."}]},
          "intermediate_data": {
            "tool_uses": [
              {
                "name": "set_device_status",
                "args": {
                  "location": "office",
                  "device_id": "desk lamp",
                  "status": "ON"
                }
              }
            ]
          }
        }
      ]
    }
  ]
}
```

The important idea is not the exact syntax.  
It is that the file captures:
- the prompt
- the expected output
- optionally the expected tool behavior

### 19.3 `test_config.json`
This file defines what counts as a passing result.

The Day 4b notebook uses it to specify thresholds like:

```json
{
  "criteria": {
    "tool_trajectory_avg_score": 1.0,
    "response_match_score": 0.8
  }
}
```

This means:
- the tool usage must match perfectly
- the response text only needs to clear a similarity threshold, not match character-for-character

### 19.4 Why these files matter
Without evaluation files, testing lives in memory and screenshots.  
With them, testing becomes:
- reproducible
- automatable
- versionable
- appropriate for CI or team workflows

---

## 20. The Main Metrics Used on Day 4

The lab focuses on two metrics first because they are easy to understand and highly useful.

### 20.1 Response Match Score
This measures how similar the final response is to the expected one.

It is useful for catching:
- poor phrasing
- missing information
- changed answer content
- drift in communication style

It is **not** enough by itself because a response can sound right while using the wrong internal path.

### 20.2 Tool Trajectory Score
This checks whether the agent used the correct tools with the correct parameters and expected order.

It is useful for catching:
- wrong tool choice
- wrong arguments
- missing tool calls
- extra unnecessary tool calls
- incorrect execution path

This is one of the most important Day 4 metrics because it directly evaluates the process.

### 20.3 Why you need both
These two scores cover different failure modes.

| Metric | Catches best | Misses on its own |
|---|---|---|
| **Response Match** | changes in final wording or content | hidden process errors |
| **Tool Trajectory** | wrong internal action path | acceptable output variations in wording |

A common Day 4 debugging pattern is:
- trajectory perfect, response poor → fix instructions or response formatting
- response okay, trajectory poor → fix planning/tool usage
- both poor → deeper agent design issue

### 20.4 Broader criteria in ADK
The ADK docs go beyond the two lab metrics and include criteria such as:
- `rubric_based_final_response_quality_v1`
- `rubric_based_tool_use_quality_v1`
- `hallucinations_v1`
- `safety_v1`
- `multi_turn_task_success_v1`
- `multi_turn_trajectory_quality_v1`
- `multi_turn_tool_use_quality_v1`

That broader list matters because it shows where Day 4 can expand in production:
from simple similarity checks to richer quality and safety judgments.

---

## 21. Batch Regression Testing

This is where Day 4b becomes operational.

### 21.1 Why one-by-one testing is not enough
Interactive testing is useful, but it does not scale.

Problems:
- it is slow
- it is easy to forget old cases
- it is hard to compare across versions
- it does not fit automation workflows

### 21.2 The regression mindset
A regression test asks:

> After I changed the prompt, tools, model, memory, or orchestration, did I break something that used to work?

That question is essential in agent systems because even small changes can shift behavior.

### 21.3 CLI evaluation
The notebook demonstrates the CLI approach:

```bash
adk eval home_automation_agent \
  home_automation_agent/integration.evalset.json \
  --config_file_path=home_automation_agent/test_config.json \
  --print_detailed_results
```

### 21.4 Why `--print_detailed_results` matters
It gives you a turn-by-turn breakdown and diffs for failures.

That makes the result useful for diagnosis instead of only reporting pass/fail.

### 21.5 Programmatic evaluation
The ADK docs also support running evaluation programmatically, including `pytest`-based workflows.

That matters when you want:
- automated test pipelines
- repository-based regression testing
- integration with normal engineering workflows

### 21.6 A practical Day 4 workflow
A strong working pattern is:

1. build or modify the agent
2. save good benchmark sessions
3. add difficult and adversarial cases
4. run `adk eval`
5. inspect failures
6. fix prompt/tool/design issues
7. rerun until stable

That loop is the operational heart of Day 4.

---

## 22. User Simulation

Static test cases are useful, but real users are not static.

That is the problem user simulation tries to solve.

### 22.1 What it is
User simulation uses a model to generate prompts dynamically during evaluation rather than relying only on prewritten prompts.

### 22.2 Why it matters
Agents often fail in ways that only appear when conversations evolve naturally:
- unexpected follow-ups
- ambiguity
- clarification requests
- context carryover
- changing goals mid-conversation

Static cases are good for regression stability.  
User simulation is good for exploring conversational robustness.

### 22.3 The Day 4b framing
The notebook presents it as an answer to the limitation of fixed test cases.

### 22.4 How it works conceptually
You define a `ConversationScenario` and a `conversation_plan`.  
A model then acts like a simulated user and generates messages during evaluation.

### 22.5 What it helps uncover
- brittle clarification behavior
- weak context handling
- failure to maintain multi-turn goals
- hidden edge cases
- unnatural assumptions in the agent

### 22.6 When to use it
Use it when:
- your agent is conversational
- you care about multi-turn behavior
- fixed benchmark cases are not enough

Do not use it as the only evaluation method.  
Use it together with fixed regression cases.

---

## 23. LLM-as-a-Judge, Agent-as-a-Judge, and Human Review

The whitepaper is clear that no single judge is enough.

### 23.1 Automated metrics
These are fast and reproducible.  
They are ideal for regression testing, dashboards, and broad coverage.

Strengths:
- scalable
- repeatable
- easy to automate

Weaknesses:
- can miss nuance
- often depend on rigid expectations
- may not capture subjective quality well

### 23.2 LLM-as-a-Judge
A model can evaluate outputs using a rubric.

This is useful for:
- helpfulness
- completeness
- tone
- rubric adherence
- comparative judgments

The whitepaper also notes that pairwise comparison can be more reliable than noisy absolute scoring in some cases.

### 23.3 Agent-as-a-Judge
This is especially relevant for trajectories.

Instead of judging only the final response, the evaluator can inspect:
- plan quality
- tool selection
- parameterization
- context use
- multi-step coherence

This is closer to how Day 4 thinks about process quality.

### 23.4 Human-in-the-Loop
Humans remain essential when evaluation involves:
- nuanced intent
- domain expertise
- ethics
- subjective quality
- high-stakes consequences

Humans are also crucial for creating the “golden set” — the benchmark cases and standards the automated system should learn from.

### 23.5 Reviewer interfaces matter
The whitepaper also highlights the importance of practical review UX:
- low-friction feedback
- conversation plus reasoning side-by-side
- issue tagging like “bad plan” or “tool misuse”
- dashboards for recurring risks

### 23.6 A strong Day 4 principle
Use a hybrid stack:
- automation for scale
- model-based judges for nuance at scale
- humans for trust, calibration, and difficult cases

---

## 24. Safety, Guardrails, and Interruptions

Day 4 is not only about correctness.  
It is also about trustworthiness.

### 24.1 Safety is a quality dimension
An agent that completes a task unsafely is not a high-quality agent.

### 24.2 Runtime interruption workflows
The whitepaper recommends pausing an agent before high-stakes actions.

Examples:
- execute payment
- delete data
- send a critical external command

This is a continuation of the action-confirmation ideas from earlier days, but now framed as part of the quality and safety system.

### 24.3 Guardrails as plugins
The whitepaper suggests implementing reusable guardrails as plugins rather than isolated ad hoc checks.

Examples:
- input safety checks on `before_model_callback`
- tool-argument checks on `before_tool_callback`
- output PII checks on `after_model_callback`

This makes safety logic:
- reusable
- layered
- testable
- easier to reason about

### 24.4 Safety evaluation
A mature Day 4 mindset includes evaluating:
- harmful outputs
- prompt injection resilience
- data leakage risk
- adherence to guidelines
- fairness or bias concerns where relevant

### 24.5 Practical conclusion
Performance metrics tell you whether the agent **can** do the job.  
Safety evaluation tells you whether it **should** do it that way.

---

## 25. The Agent Quality Flywheel

The whitepaper ends by tying Day 4 together into an operational loop.

A simple version of the flywheel is:

1. **Define quality**
2. **Instrument for visibility**
3. **Evaluate the process**
4. **Architect the feedback loop**
5. **Repeat**

### 25.1 Define quality
Before measuring anything, decide what matters:
- task success
- latency
- cost
- safety
- user satisfaction
- domain-specific constraints

### 25.2 Instrument for visibility
Without logs, traces, and metrics, you cannot diagnose failures or measure the process.

### 25.3 Evaluate the process
Run structured evaluations:
- saved regression cases
- batch eval sets
- tool trajectory checks
- rubric-based checks
- user simulation
- human review where needed

### 25.4 Architect the feedback loop
Make results actionable:
- identify failing cases
- trace root causes
- adjust prompts/tools/architecture
- rerun evaluation
- monitor long-term trends

### 25.5 Why this is the real Day 4 deliverable
Day 4 is not only teaching two notebooks.  
It is teaching a habit:

> **Build agents in a way that lets you improve them continuously.**

That habit is the difference between a demo and an operable agent system.

---

## 26. Common Mistakes to Avoid

These are the most common Day 4 misunderstandings.

### 26.1 Judging only the final response
This hides planning and tool-use failures.

### 26.2 Treating logs as enough
Logs are useful, but without traces you often miss the causal story.

### 26.3 Treating one passing conversation as proof
A single successful run is not evidence of broad reliability.

### 26.4 Using only response similarity
The final response can look acceptable even when the action path was wrong.

### 26.5 Ignoring efficiency
An agent that takes too many steps is lower quality even if it succeeds.

### 26.6 Skipping adversarial or ambiguous cases
Agents often look fine on happy-path prompts and fail on edge cases.

### 26.7 Debugging by prompt guessing alone
Day 4 teaches disciplined diagnosis, not random instruction edits.

### 26.8 Depending entirely on the web UI
The UI is great for development, but scalable systems need files, automation, and reusable plugins.

### 26.9 Forgetting safety as a quality dimension
A useful but unsafe agent is still a bad agent.

### 26.10 Not versioning eval artifacts
If your test cases live only in memory, quality will drift quietly.

---

## 27. Day 4 Revision Checklist

Use this as a self-test after finishing the resources.

### Core understanding
- [ ] I can explain the difference between **observability** and **evaluation**
- [ ] I can explain why agents require **trajectory-aware** quality analysis
- [ ] I can explain the **four pillars of agent quality**
- [ ] I can describe the **Outside-In** evaluation strategy

### Day 4a
- [ ] I know the difference between **logs**, **traces**, and **metrics**
- [ ] I know how to run `adk web --log_level DEBUG`
- [ ] I know how to inspect a broken run in the Trace and Events views
- [ ] I understand the Research Paper Finder bug at a conceptual level
- [ ] I know when to use `LoggingPlugin()`
- [ ] I understand why callbacks and plugins matter for production observability

### Day 4b
- [ ] I know how to save a current session as an eval case
- [ ] I understand `response_match_score`
- [ ] I understand `tool_trajectory_avg_score`
- [ ] I know what `test_config.json` does
- [ ] I know what an `evalset` file is for
- [ ] I know how to run `adk eval`
- [ ] I understand why regression testing matters for agents
- [ ] I can explain what user simulation adds

### Quality mindset
- [ ] I think about **safety** and **guardrails** as part of quality
- [ ] I understand why a hybrid approach uses automation, model judges, and human review
- [ ] I can describe the **Agent Quality Flywheel**

---

## 28. Short Glossary

**Agent quality** — the overall usefulness, efficiency, reliability, and safety of an agent.

**Observability** — the ability to inspect internal behavior through logs, traces, and metrics.

**Evaluation** — the systematic measurement of agent behavior across benchmark scenarios.

**Trajectory** — the full sequence of planning, tool use, observations, and final output.

**Log** — a single timestamped record of an event.

**Trace** — a connected execution story across multiple steps.

**Metric** — a summary number used for monitoring or scoring.

**Regression** — a backward step where a previously good behavior becomes worse after a change.

**Eval case** — one benchmark conversation or scenario.

**Eval set** — a collection of benchmark cases.

**Response Match Score** — how closely the final response aligns with the expected response.

**Tool Trajectory Score** — how closely the tool path matches the expected tool behavior.

**LLM-as-a-Judge** — a model-based evaluator that scores or compares outputs.

**Human-in-the-Loop** — human review used for calibration, nuance, and high-stakes judgment.

**User simulation** — dynamic evaluation where an LLM generates user turns during the test.

**Guardrail** — a policy or safety mechanism that constrains agent behavior.

---

## 29. Resources Reviewed

### Official course resources
- [Kaggle — 5-Day AI Agents Intensive Course with Google](https://www.kaggle.com/learn-guide/5-day-agents)
- [Kaggle — Agent Quality whitepaper](https://www.kaggle.com/whitepaper-agent-quality)
- [Kaggle Notebook — Day 4a: Agent Observability](https://www.kaggle.com/code/kaggle5daysofai/day-4a-agent-observability)
- [Kaggle Notebook — Day 4b: Agent Evaluation](https://www.kaggle.com/code/kaggle5daysofai/day-4b-agent-evaluation)
- [YouTube — Kaggle course podcasts and livestreams playlist](https://www.youtube.com/playlist?list=PLqFaTIg4myu9r7uRoNfbJhHUbLp-1t1YE)

### ADK documentation
- [ADK — Why Evaluate Agents](https://adk.dev/evaluate/)
- [ADK — Evaluation Criteria](https://adk.dev/evaluate/criteria/)
- [ADK — User Simulation](https://adk.dev/evaluate/user-sim/)
- [ADK — Logging](https://adk.dev/observability/logging/)
- [ADK — Callbacks: Design Patterns and Best Practices](https://adk.dev/callbacks/design-patterns-and-best-practices/)
- [ADK — Plugins](https://adk.dev/plugins/)

### Related references used to connect course ideas to implementation
- [ADK — Observability](https://adk.dev/observability/)
- [Google Cloud — Vertex AI evaluation criteria overview](https://docs.cloud.google.com/vertex-ai/generative-ai/docs/models/determine-eval)

---

## Final Summary

Day 4 teaches the discipline that turns agent building into agent engineering.

If Day 1 taught you what agents are, Day 2 taught you how they act, and Day 3 taught you how they remember, then Day 4 teaches you how to answer the question:

> **Can I trust this system enough to keep improving it?**

The complete Day 4 answer is:

- define what quality means
- instrument the system with logs, traces, and metrics
- evaluate both final outcomes and internal trajectories
- save good behaviors as benchmarks
- run regressions after every meaningful change
- combine automation, user simulation, and human judgment
- treat safety as part of quality, not a separate afterthought

That is the real lesson of Day 4.
