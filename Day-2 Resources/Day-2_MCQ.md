# Day 2 MCQs — Agent Tools & Interoperability with MCP

This quiz contains **12 multiple-choice questions** based on **Day 2** of the course.

- **Questions 1–6:** Easy
- **Questions 7–12:** Intermediate
- Click **Show answer** under each question to reveal the correct answer and a short explanation.

---

## Easy Level

### 1) What is the main purpose of a tool in an agent system?
A. To change the model architecture automatically  
B. To let the agent perform actions or fetch external information  
C. To replace the need for prompts  
D. To remove the need for memory  

<details>
<summary><strong>Show answer</strong></summary>

**Correct answer: B**

**Explanation:** Tools let an agent go beyond text generation by calling functions, external services, APIs, or other capabilities.
</details>

---

### 2) In Day 2a, what kind of Python code is most commonly turned into a basic tool?
A. A class constructor  
B. A Python function  
C. A SQL query  
D. A markdown cell  

<details>
<summary><strong>Show answer</strong></summary>

**Correct answer: B**

**Explanation:** Day 2a emphasizes turning ordinary Python functions into agent tools.
</details>

---

### 3) What does **MCP** stand for?
A. Model Control Pipeline  
B. Multi-Channel Processing  
C. Model Context Protocol  
D. Managed Context Platform  

<details>
<summary><strong>Show answer</strong></summary>

**Correct answer: C**

**Explanation:** MCP stands for **Model Context Protocol**, a standard way for models and applications to connect to tools and data sources.
</details>

---

### 4) What is the main idea behind using an **AgentTool**?
A. It deletes unused tools automatically  
B. It allows one agent to be used by another agent as a tool  
C. It converts every tool into a database  
D. It stores tool outputs permanently  

<details>
<summary><strong>Show answer</strong></summary>

**Correct answer: B**

**Explanation:** An **AgentTool** lets one agent call another agent in a tool-like way, which is useful for specialist behavior.
</details>

---

### 5) Why would a course notebook use a separate code-execution specialist agent?
A. To make the notebook longer  
B. To isolate specialized or sensitive computation from the main agent  
C. To avoid using prompts  
D. To disable tool usage  

<details>
<summary><strong>Show answer</strong></summary>

**Correct answer: B**

**Explanation:** A separate specialist agent helps isolate code execution and keeps the main agent focused on orchestration and user interaction.
</details>

---

### 6) Which of the following is the best description of a **good tool design**?
A. A tool with many unrelated tasks in one function  
B. A tool with vague inputs and unpredictable output  
C. A narrow, clear tool with a well-defined purpose  
D. A tool that always asks five follow-up questions  

<details>
<summary><strong>Show answer</strong></summary>

**Correct answer: C**

**Explanation:** Good tools are focused, easy to understand, and have clear inputs, outputs, and behavior.
</details>

---

## Intermediate Level

### 7) What is the role of **ToolContext** in Day 2b?
A. It replaces the model  
B. It provides access to runtime context such as state and tool interaction helpers  
C. It stores all agent memory permanently  
D. It creates new notebooks automatically  

<details>
<summary><strong>Show answer</strong></summary>

**Correct answer: B**

**Explanation:** **ToolContext** gives a tool access to useful runtime information such as state and confirmation-related helpers.
</details>

---

### 8) Why would a tool call `request_confirmation()` before completing an action?
A. To improve token generation speed  
B. To ask for human approval before a sensitive or high-impact action  
C. To convert JSON into SQL  
D. To disable resumability  

<details>
<summary><strong>Show answer</strong></summary>

**Correct answer: B**

**Explanation:** Confirmation is a safety step used when an action should not proceed automatically without user approval.
</details>

---

### 9) What is the main purpose of **`McpToolset`**?
A. To replace Python functions completely  
B. To connect ADK agents to tools exposed by an MCP server  
C. To convert agents into PDFs  
D. To evaluate tool quality automatically without test cases  

<details>
<summary><strong>Show answer</strong></summary>

**Correct answer: B**

**Explanation:** **`McpToolset`** is used to bring MCP-exposed tools into the ADK agent workflow.
</details>

---

### 10) In a resumable workflow, why is **`invocation_id`** important?
A. It changes the model temperature dynamically  
B. It helps match the resumed response to the original paused tool invocation  
C. It stores the agent’s final evaluation score  
D. It replaces the need for session state  

<details>
<summary><strong>Show answer</strong></summary>

**Correct answer: B**

**Explanation:** The same **`invocation_id`** is used so the system knows which paused action is being resumed.
</details>

---

### 11) Which option best describes the difference between **FunctionTool** and **AgentTool**?
A. FunctionTool is for math only; AgentTool is for text only  
B. FunctionTool wraps a Python function; AgentTool wraps another agent  
C. FunctionTool is only for MCP; AgentTool is only for SQLite  
D. There is no difference  

<details>
<summary><strong>Show answer</strong></summary>

**Correct answer: B**

**Explanation:** A **FunctionTool** exposes a Python function as a tool, while an **AgentTool** exposes an agent as a callable tool.
</details>

---

### 12) Which Day 2 design choice is the **best practice** for reliable agent behavior?
A. Let the agent call any tool for any purpose without restrictions  
B. Combine calculation, approval, shipping, and file editing into one giant tool  
C. Use focused tools, clear tool descriptions, and approval for risky actions  
D. Hide tool behavior from the user and developer  

<details>
<summary><strong>Show answer</strong></summary>

**Correct answer: C**

**Explanation:** Day 2 emphasizes well-scoped tools, clear descriptions, safety controls, and human approval where needed.
</details>

---

## Suggested Use

- Use this quiz for **classroom discussion**, **self-check**, or **GitHub-based revision**.
- A simple scoring rule is **1 mark per correct answer**.
- Recommended benchmark:
  - **10–12 correct:** Strong Day 2 understanding
  - **7–9 correct:** Good progress, revise MCP and approval flow
  - **0–6 correct:** Review Day 2a and Day 2b again
