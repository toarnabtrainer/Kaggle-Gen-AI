# Day 5 MCQs — Prototype to Production

These questions are based on **Day 5** of the 5-Day AI Agents course.

- **Total Questions:** 12
- **Level:** Easy + Intermediate
- **Format:** Multiple Choice Questions (MCQs)
- **Answers:** Hidden by default; click to reveal

---

## Easy Level

### 1) What is the main focus of **Day 5** in the course?
A. Only prompt writing  
B. Moving agents from prototype to production  
C. Only database design  
D. Only notebook formatting

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
Day 5 focuses on taking agent systems beyond local demos and understanding how to connect, deploy, test, and operate them in production.

</details>

---

### 2) What does **A2A** stand for?
A. App-to-App  
B. Agent-to-Agent  
C. API-to-Agent  
D. Async-to-Async

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
A2A means **Agent-to-Agent**, a protocol for agents to communicate with each other.

</details>

---

### 3) What is the purpose of `to_a2a()` in Day 5a?
A. It deletes an agent  
B. It exposes an agent through the A2A interface  
C. It stores long-term memory  
D. It evaluates the agent automatically

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
`to_a2a()` is used to expose an agent so that another system or agent can communicate with it using A2A.

</details>

---

### 4) What is an **agent card**?
A. A billing receipt for deployment  
B. A document that describes how another agent can discover and use a remote agent  
C. A notebook theme file  
D. A database backup file

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
An agent card describes important information about a remote agent, such as its capabilities and how it can be accessed.

</details>

---

### 5) What does `RemoteA2aAgent` mainly do?
A. It creates a new local model from scratch  
B. It acts as a local wrapper for talking to a remote A2A agent  
C. It replaces all tools with memory  
D. It compiles Python code to SQL

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
`RemoteA2aAgent` lets one agent system interact with another agent exposed remotely through A2A.

</details>

---

### 6) In Day 5b, what is **Vertex AI Agent Engine** mainly used for?
A. Editing notebook markdown  
B. Deploying and operating agents in production  
C. Replacing Python with Java  
D. Storing CSV files only

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
Vertex AI Agent Engine is the production runtime discussed on Day 5 for deploying, managing, and operating agents.

</details>

---

## Intermediate Level

### 7) Which statement best explains the relationship between **A2A** and **MCP**?
A. They are the same thing  
B. A2A replaces MCP completely  
C. MCP is mainly for tools/data access, while A2A is for agent-to-agent collaboration  
D. MCP is only for deployment and A2A is only for logging

<details>
<summary>Show Answer</summary>

**Correct Answer: C**  
A useful Day 5 rule is: **MCP helps agents use external tools and data**, while **A2A helps agents communicate with other agents**.

</details>

---

### 8) Why is **evaluation-gated deployment** important?
A. It makes deployment slower on purpose  
B. It ensures agents are tested before promotion to production  
C. It removes the need for monitoring  
D. It prevents any future updates

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
Evaluation-gated deployment means an agent should meet defined quality checks before it is allowed into production.

</details>

---

### 9) Which option best describes the difference between **sessions** and **Memory Bank** in production?
A. Sessions are long-term knowledge and Memory Bank is temporary chat history  
B. Sessions hold conversation state, while Memory Bank stores longer-term reusable information  
C. They are exactly identical  
D. Sessions are only for deployment logs

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
Sessions track ongoing interaction context, while Memory Bank is for longer-lived information that can be reused across interactions.

</details>

---

### 10) Which rollout approach sends a small portion of traffic to a new version first?
A. Full cutover only  
B. Canary release  
C. Random prompt injection  
D. LoopAgent

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
A canary release exposes only a small share of traffic to a new version first, reducing risk during rollout.

</details>

---

### 11) What does the Day 5 operating loop **Observe → Act → Evolve** encourage teams to do?
A. Deploy once and never revisit the system  
B. Continuously monitor, improve, and adapt the agent system over time  
C. Focus only on prompts and ignore tooling  
D. Avoid production testing

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
The loop emphasizes ongoing operations: observe system behavior, act on findings, and evolve the system through improvements.

</details>

---

### 12) Which statement best matches the Day 5 production mindset?
A. Deployment is the finish line  
B. Production success depends only on model intelligence  
C. Deployment is one step in a larger system lifecycle involving testing, rollout, security, and operations  
D. A2A should be used for every sub-agent pattern

<details>
<summary>Show Answer</summary>

**Correct Answer: C**  
Day 5 treats production as an operating discipline that includes evaluation, deployment, security, rollout strategy, and post-deploy monitoring.

</details>

---

## Suggested Use

You can use these MCQs for:
- self preparation quizzes
- GitHub README learning checks
- self-assessment after Day 5
- certificate-oriented revision practice
