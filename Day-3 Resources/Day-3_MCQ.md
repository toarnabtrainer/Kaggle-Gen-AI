# Day 3 MCQs — Context Engineering: Sessions & Memory

These questions are based on **Day 3** of the 5-Day AI Agents course.

- **Total Questions:** 12
- **Level:** Easy + Intermediate
- **Format:** Multiple Choice Questions (MCQs)
- **Answers:** Hidden by default; click to reveal

---

## Easy Level

### 1) What is the main purpose of a **session** in an agent system?
A. To permanently deploy the agent to production  
B. To store conversation history and short-term interaction context  
C. To replace all tools used by the agent  
D. To train the base model from scratch

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
A session is mainly used to maintain the interaction history and short-term context for a conversation.

</details>

---

### 2) Which session service keeps data only while the program is running?
A. `DatabaseSessionService`  
B. `InMemorySessionService`  
C. `MemoryBankService`  
D. `FileTransferService`

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
`InMemorySessionService` stores session data temporarily in memory, so it is lost when the process stops.

</details>

---

### 3) Why would a developer use `DatabaseSessionService` instead of `InMemorySessionService`?
A. To make the agent speak faster  
B. To avoid using state entirely  
C. To persist session data across runs  
D. To remove conversation history

<details>
<summary>Show Answer</summary>

**Correct Answer: C**  
A database-backed session service is useful when you want sessions to remain available even after the application stops and restarts.

</details>

---

### 4) In Day 3, what does **state** usually represent?
A. Temporary or stored values associated with a user/session workflow  
B. The model checkpoint used for pretraining  
C. The operating system of the machine  
D. Only the final printed response

<details>
<summary>Show Answer</summary>

**Correct Answer: A**  
State is used to hold values that help the agent continue tasks, remember variables, and manage interaction flow.

</details>

---

### 5) What is meant by **session isolation**?
A. All users share one common history  
B. Each session keeps its own separate conversation data  
C. The model cannot use tools  
D. The agent must run offline only

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
Session isolation means one session’s history and state should not mix with another session’s data.

</details>

---

### 6) What is the main purpose of an agent **memory service**?
A. To replace sessions completely  
B. To store and retrieve useful information across interactions  
C. To install Python packages  
D. To start a web server

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
Memory services help the agent retain relevant information beyond a single short conversation turn.

</details>

---

## Intermediate Level

### 7) What is the role of `add_session_to_memory()`?
A. It clears all stored memory  
B. It converts a session into retrievable memory entries  
C. It deploys the agent to production  
D. It starts a new notebook kernel

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
`add_session_to_memory()` is used to save session content into memory so that it can be searched or reused later.

</details>

---

### 8) Which function is most directly used when you want to find relevant memory based on a new query?
A. `search_memory()`  
B. `sleep()`  
C. `print_memory()`  
D. `close_session()`

<details>
<summary>Show Answer</summary>

**Correct Answer: A**  
`search_memory()` is used to retrieve memory items related to a given prompt or topic.

</details>

---

### 9) Why is **context compaction** useful in long-running conversations?
A. It increases the keyboard speed  
B. It helps reduce prompt size while preserving useful context  
C. It removes all memory permanently  
D. It disables sessions

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
Context compaction helps keep the working context manageable by compressing or summarizing older events instead of repeatedly sending everything.

</details>

---

### 10) What does `preload_memory` help the agent do?
A. Load relevant stored memory before the main response step  
B. Delete all session events automatically  
C. Replace the LLM with a database  
D. Stop callbacks from running

<details>
<summary>Show Answer</summary>

**Correct Answer: A**  
`preload_memory` helps fetch relevant memory ahead of time so the agent can use it during response generation.

</details>

---

### 11) What is the purpose of an `after_agent_callback` in the memory notebook?
A. To shut down the notebook kernel  
B. To automatically save or process information after an agent step finishes  
C. To turn sessions into tools  
D. To remove all user preferences

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
An `after_agent_callback` can be used to perform post-processing, including writing useful information to memory automatically.

</details>

---

### 12) Which statement best describes the difference between **sessions** and **memory**?
A. Sessions and memory are exactly the same thing  
B. Sessions handle short-term conversation context, while memory is used for longer-term retrieval  
C. Sessions are only for deployment, while memory is only for debugging  
D. Memory is only used to store images

<details>
<summary>Show Answer</summary>

**Correct Answer: B**  
Sessions usually manage ongoing conversation history and state, while memory is used to retain and retrieve relevant information across broader interactions.

</details>

---

## Suggested Use

You can use these MCQs for:
- self preparation quizzes
- GitHub README learning checks
- self-assessment after Day 3
- certificate-oriented revision practice

