# Day 1 MCQs — Click to Reveal Answers

> GitHub-friendly MCQs based on **Day 1: Introduction to Agents**.  
> The answers stay hidden until the learner clicks **Show answer**.

---

## Easy Level

### 1) Which option best describes an AI agent?
A. A model that only generates one reply from a prompt  
B. A system that can reason, use tools, and decide next steps  
C. A database that stores files  
D. A notebook that runs Python cells  

<details>
<summary><strong>Show answer</strong></summary>

**Correct answer: B**  
An agent is more than a plain LLM response system. It can reason, use tools, observe results, and continue toward a goal.

</details>

---

### 2) What are the three core parts of Day 1 agent architecture?
A. Model, tools, orchestration  
B. Browser, keyboard, monitor  
C. Prompt, dataset, dashboard  
D. Notebook, terminal, API key  

<details>
<summary><strong>Show answer</strong></summary>

**Correct answer: A**  
Day 1 frames agents around **model + tools + orchestration**.

</details>

---

### 3) In the Think → Act → Observe loop, what happens during **Observe**?
A. The user writes a new API key  
B. The system checks the result of an action or tool call  
C. The model is retrained  
D. The notebook is deleted  

<details>
<summary><strong>Show answer</strong></summary>

**Correct answer: B**  
After acting, the agent inspects what happened and uses that result to decide the next step.

</details>

---

### 4) Why is a search tool useful in Day 1a?
A. It helps the agent answer questions using up-to-date external information  
B. It permanently stores long-term memory  
C. It replaces the need for a model  
D. It deploys the agent to production  

<details>
<summary><strong>Show answer</strong></summary>

**Correct answer: A**  
A search tool lets the agent fetch current, grounded information instead of relying only on the model's built-in knowledge.

</details>

---

### 5) In a coordinator-and-specialist design, what does the coordinator mainly do?
A. It trains a new model from scratch  
B. It routes work to the right specialist agent  
C. It deletes the outputs of other agents  
D. It disables tool calling  

<details>
<summary><strong>Show answer</strong></summary>

**Correct answer: B**  
The coordinator decides which specialist should handle each part of the task.

</details>

---

### 6) Which workflow pattern is best when tasks must happen in a fixed order?
A. Parallel workflow  
B. Sequential workflow  
C. Random workflow  
D. Memory workflow  

<details>
<summary><strong>Show answer</strong></summary>

**Correct answer: B**  
A **SequentialAgent** is used when step 1 must finish before step 2 begins.

</details>

---

## Intermediate Level

### 7) When is a **Parallel** workflow most appropriate?
A. When one step depends on the previous step’s output  
B. When several independent subtasks can run at the same time  
C. When the agent must repeat until success  
D. When the system should avoid specialization  

<details>
<summary><strong>Show answer</strong></summary>

**Correct answer: B**  
Use **ParallelAgent** when tasks are independent and can be processed simultaneously.

</details>

---

### 8) What is the main purpose of `output_key` in a workflow?
A. It encrypts the agent’s model  
B. It stores an agent’s output in shared state for later steps  
C. It launches the ADK web server  
D. It creates a new Kaggle notebook  

<details>
<summary><strong>Show answer</strong></summary>

**Correct answer: B**  
`output_key` helps pass useful outputs from one step to later agents in the workflow.

</details>

---

### 9) Which statement correctly compares **LLM-managed orchestration** and **deterministic orchestration**?
A. Both always follow the exact same fixed path  
B. LLM-managed orchestration is dynamic, while deterministic orchestration follows predefined flow rules  
C. Deterministic orchestration never uses tools  
D. LLM-managed orchestration cannot involve more than one agent  

<details>
<summary><strong>Show answer</strong></summary>

**Correct answer: B**  
LLM-managed orchestration is flexible and model-driven, while deterministic workflows follow fixed logic such as sequential, parallel, or loop patterns.

</details>

---

### 10) Which Day 1 workflow pattern is best when an agent should keep refining its work until a condition is met?
A. SequentialAgent  
B. ParallelAgent  
C. LoopAgent  
D. SearchAgent  

<details>
<summary><strong>Show answer</strong></summary>

**Correct answer: C**  
A **LoopAgent** is used for repeated improvement, checking, or retrying until a goal or stopping rule is reached.

</details>

---

### 11) Why might one agent be wrapped and used as a tool by another agent?
A. To allow the main agent to call a specialist capability in a controlled way  
B. To stop the specialist from ever being used  
C. To remove orchestration from the system  
D. To turn the agent into a database table  

<details>
<summary><strong>Show answer</strong></summary>

**Correct answer: A**  
Using an **agent as a tool** lets a coordinator reuse specialist behavior without giving up the overall workflow structure.

</details>

---

### 12) Which design choice best matches Day 1’s governance and safety ideas?
A. Give agents unlimited permissions because autonomy is always better  
B. Use clear instructions, bounded tools, and human oversight for sensitive actions  
C. Avoid logging because it makes debugging slower  
D. Let every specialist call any tool without restrictions  

<details>
<summary><strong>Show answer</strong></summary>

**Correct answer: B**  
Day 1 emphasizes autonomy **with boundaries**: clear roles, limited powers, and observability or human control where needed.

</details>

---

## Suggested Use

- Use Questions **1–6** for a basic knowledge check.
- Use Questions **7–12** for deeper conceptual understanding.
