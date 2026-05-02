# Day 4 MCQs — Agent Quality

These questions are based on **Day 4** of the 5-Day AI Agents course.

- **Total Questions:** 12
- **Level:** Easy + Intermediate
- **Format:** Multiple Choice Questions (MCQs)
- **Answers:** Hidden by default; click to reveal

---

## Easy Level

### 1) What is the main focus of **Day 4** in the course?
A. Deployment only  
B. Agent quality, observability, and evaluation  
C. Only memory storage  
D. Database administration

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
Day 4 focuses on understanding how to observe, debug, and evaluate agent behavior so quality can be measured systematically.

</details>

---

### 2) What does **observability** mainly help a developer do?
A. Hide tool calls from the user  
B. See how the agent behaves internally during execution  
C. Increase model training speed  
D. Replace evaluation completely

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
Observability helps developers inspect internal behavior such as logs, tool usage, decisions, and execution flow.

</details>

---

### 3) Why would someone run ADK Web with a **DEBUG** log level?
A. To reduce all output  
B. To see more detailed execution information  
C. To permanently deploy the agent  
D. To disable logging plugins

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
A DEBUG log level provides more detailed information, which is useful for tracing agent behavior and finding problems.

</details>

---

### 4) In Day 4, what is an **evaluation case**?
A. A hardware device used to run notebooks  
B. A saved test scenario used to check agent behavior  
C. A memory database entry  
D. A Python package manager

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
An evaluation case is a structured test example used to check how well the agent performs on a given task.

</details>

---

### 5) Which of the following is the best reason to evaluate an agent?
A. To make the notebook look longer  
B. To move from guessing quality to measuring quality  
C. To avoid writing prompts  
D. To remove all tools from the system

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
Evaluation helps teams judge performance with evidence rather than intuition alone.

</details>

---

### 6) What does `run_debug()` help with?
A. It installs new Python packages  
B. It provides a programmatic way to inspect agent execution  
C. It replaces all test cases  
D. It shuts down the runtime

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
`run_debug()` is used to inspect execution details in code, making it useful for debugging and observability workflows.

</details>

---

## Intermediate Level

### 7) Which metric is most directly related to whether the agent's final answer matches the expected answer?
A. `memory_latency_score`  
B. `response_match_score`  
C. `session_merge_score`  
D. `tool_install_score`

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
`response_match_score` is intended to measure how closely the agent's final response matches the expected outcome.

</details>

---

### 8) Which metric is most useful for checking whether the agent followed the expected tool usage path?
A. `tool_trajectory_avg_score`  
B. `temperature_score`  
C. `prompt_length_score`  
D. `database_score`

<details>
<summary>Show Answer</summary>

**Correct Answer: A**  
`tool_trajectory_avg_score` is used to evaluate whether the agent took the expected sequence of tool-related actions.

</details>

---

### 9) What is the role of `test_config.json` in the evaluation workflow?
A. It stores notebook screenshots  
B. It defines how evaluation should be run and which criteria to use  
C. It deploys the model to production  
D. It replaces the agent definition

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
`test_config.json` is used to specify evaluation settings, criteria, and related test behavior.

</details>

---

### 10) What is the main purpose of `integration.evalset.json`?
A. To store the evaluation examples or test cases  
B. To save Python package versions  
C. To define SQL tables  
D. To cache model weights

<details>
<summary>Show Answer</summary>

**Correct Answer: A**  
`integration.evalset.json` contains the set of test examples used by the evaluation process.

</details>

---

### 11) Why can a custom `LoggingPlugin` be useful?
A. It allows developers to capture and inspect runtime events in a reusable way  
B. It removes the need for evaluation  
C. It converts memory into sessions  
D. It automatically deploys the notebook

<details>
<summary>Show Answer</summary>

**Correct Answer: A**  
A logging plugin can collect runtime details consistently across executions, which helps with debugging and observability.

</details>

---

### 12) Which statement best reflects the Day 4 view of **agent quality**?
A. Quality is only about having a smart model  
B. Quality is a system-level concern involving behavior, tools, evaluation, and safety  
C. Quality only matters after deployment  
D. Quality means using more agents

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
Day 4 treats quality as a broader system problem that includes observability, testing, tool behavior, and reliable outputs.

</details>

---

## Suggested Use

You can use these MCQs for:
- self preparation quizzes
- GitHub README learning checks
- self-assessment after Day 4
- certificate-oriented revision practice
