# ✅ 5-Day AI Agents Intensive Course Planner

A concise day-wise study planner for the Kaggle **5-Day AI Agents Intensive Course with Google**.

**GitHub Link:** https://github.com/toarnabtrainer/Kaggle-Gen-AI

## ☑️ Course Goal
Build a solid understanding of AI agents step by step: **agent basics → tools → memory/context → observability/evaluation → production deployment**.

## ☑️ Official Course Link
- [Kaggle Learn Guide](https://www.kaggle.com/learn-guide/5-day-agents)

## ☑️ Before You Start
- Verify your Kaggle account.
- Create your Gemini API key in Google AI Studio.
- Save the API key in **Kaggle Secrets**.
- Make your own copy of each notebook before running cells.
- Keep one notes file for daily takeaways.

---

## ☑️ Day 0 — Setup & Troubleshooting
**Suggested time:** 30–45 minutes  
**Focus:** Make your environment fully ready.

### 🔶 Tasks
- Open the official learn guide.
- Read the troubleshooting / FAQ notebook.
- Verify Kaggle account access.
- Add Gemini API key to Kaggle Secrets.
- Launch one notebook and confirm it runs.

### 🔶 Outcome
- Kaggle setup complete
- API key working
- No environment blockers

### 🔶 Checklist
- [ ] Kaggle account verified
- [ ] Gemini API key created
- [ ] Kaggle Secrets configured
- [ ] Test notebook opened successfully

---

## ☑️ Day 1 — Introduction to Agents
**Suggested time:** 2–3 hours  
**Modules:**
- **Day 1a:** From Prompt to Action
- **Day 1b:** Agent Architectures

### 🔶 Focus
Learn what makes an AI system an **agent**, how prompts become actions, and how single-agent vs multi-agent designs work.

### 🔶 Tasks
- Complete both Day 1 notebooks.
- Write down the basic agent loop: **input → reasoning → action/tool use → output**.
- Sketch one agent architecture from the notebook.
- Note the difference between a normal LLM app and an agentic workflow.

### 🔶 Outcome
- Clear understanding of agent fundamentals
- First hands-on exposure to ADK-based agent workflows
- Basic architecture notes for future projects

### 🔶 Deliverable
Create a short note answering:
> What makes an AI application an “agent” rather than just a chatbot?

### 🔶 Checklist
- [ ] Day 1a completed
- [ ] Day 1b completed
- [ ] Agent loop written in notes
- [ ] One architecture diagram created

---

## ☑️ Day 2 — Agent Tools & MCP
**Suggested time:** 2–3 hours  
**Modules:**
- **Day 2a:** Agent Tools
- **Day 2b:** Agent Tools Best Practices

### 🔶 Focus
Understand how agents use **tools**, and how interoperability works through **MCP (Model Context Protocol)**.

### 🔶 Tasks
- Complete both Day 2 notebooks.
- Identify how tools extend an agent beyond text generation.
- Note when to use a simple Python function vs a more structured tool interface.
- Write down best practices for tool naming, inputs, outputs, and safety.

### 🔶 Outcome
- Practical understanding of tool-enabled agents
- Better grasp of MCP-based interoperability
- A reusable checklist for designing tools

### 🔶 Deliverable
Create a short note with:
- 3 rules for designing good agent tools
- 1 example where MCP would be useful

### 🔶 Checklist
- [ ] Day 2a completed
- [ ] Day 2b completed
- [ ] Tool design notes written
- [ ] MCP use case documented

---

## ☑️ Day 3 — Context Engineering: Sessions & Memory
**Suggested time:** 2–3 hours  
**Modules:**
- **Day 3a:** Agent Sessions
- **Day 3b:** Agent Memory

### 🔶 Focus
Learn how agents become **stateful** using session history, context engineering, and longer-term memory.

### 🔶 Tasks
- Complete both Day 3 notebooks.
- Compare **sessions** vs **memory** in your notes.
- Record how short-term context differs from persistent memory.
- Note where context is stored and how it is retrieved.

### 🔶 Outcome
- Strong understanding of short-term vs long-term agent state
- Better reasoning about personalization and continuity
- Cleaner mental model for context engineering

### 🔶 Deliverable
Create a simple table:

| Concept | Meaning | Example |
|---|---|---|
| Session | Short-term conversation state | Current chat history |
| Memory | Long-term stored information | User preferences |

### 🔶 Checklist
- [ ] Day 3a completed
- [ ] Day 3b completed
- [ ] Session vs memory table created
- [ ] Context engineering notes written

---

## ☑️ Day 4 — Agent Quality
**Suggested time:** 2–3 hours  
**Modules:**
- **Day 4a:** Agent Observability
- **Day 4b:** Agent Evaluation

### 🔶 Focus
Learn how to inspect agent behavior with **logs, traces, and metrics**, and how to evaluate whether an agent is actually working well.

### 🔶 Tasks
- Complete both Day 4 notebooks.
- Review traces and logs from an agent run.
- List the main evaluation criteria you want to use in future projects.
- Define what success and failure look like for one agent task.

### 🔶 Outcome
- Better visibility into agent behavior
- A basic evaluation mindset
- Less reliance on “it seems to work” testing

### 🔶 Deliverable
Create a checklist with at least 5 evaluation points, for example:
- correctness
- tool use
- groundedness
- latency
- reliability

### 🔶 Checklist
- [ ] Day 4a completed
- [ ] Day 4b completed
- [ ] Observability notes written
- [ ] Evaluation checklist created

---

## ☑️ Day 5 — Prototype to Production
**Suggested time:** 2–4 hours  
**Modules:**
- **Day 5a:** Agent2Agent Communication
- **Day 5b:** Agent Deployment

### 🔶 Focus
Understand how agents collaborate through **A2A (Agent2Agent)** and how a prototype moves toward real deployment.

### 🔶 Tasks
- Complete both Day 5 notebooks.
- Note the difference between **MCP** and **A2A**.
- Summarize how multiple agents can communicate and coordinate.
- Review the deployment flow and capture the production steps in your notes.

### 🔶 Outcome
- Clear view of multi-agent communication
- Basic production deployment understanding
- A stronger end-to-end view of the agent stack

### 🔶 Deliverable
Write a short summary answering:
> How would I move my agent from notebook prototype to a production-ready system?

### 🔶 Checklist
- [ ] Day 5a completed
- [ ] Day 5b completed
- [ ] MCP vs A2A notes written
- [ ] Production deployment summary created

---

## ☑️ End-of-Course Completion Checklist
- [ ] Day 0 setup finished
- [ ] Day 1 fundamentals understood
- [ ] Day 2 tools and MCP understood
- [ ] Day 3 sessions and memory understood
- [ ] Day 4 observability and evaluation understood
- [ ] Day 5 A2A and deployment understood
- [ ] Notes saved for all 6 days

## ☑️ Final Outputs to Keep in This Repository
- `README.md` → course planner
- `notes/day-1.md` → agent basics
- `notes/day-2.md` → tools and MCP
- `notes/day-3.md` → sessions and memory
- `notes/day-4.md` → observability and evaluation
- `notes/day-5.md` → A2A and deployment
- `project-ideas.md` → ideas to build after the course

## ☑️ Suggested Success Criteria
By the end of this course, I should be able to:
- explain what an AI agent is
- build an agent with tools
- understand sessions vs memory
- inspect agent behavior with observability
- evaluate agent quality systematically
- describe the path from prototype to production

## ☑️ References
- [Official Kaggle Learn Guide](https://www.kaggle.com/learn-guide/5-day-agents)
- [Day 0 — Troubleshooting and FAQs](https://www.kaggle.com/code/kaggle5daysofai/day-0-troubleshooting-and-faqs)
- [Day 1a — From Prompt to Action](https://www.kaggle.com/code/kaggle5daysofai/day-1a-from-prompt-to-action)
- [Day 1b — Agent Architectures](https://www.kaggle.com/code/kaggle5daysofai/day-1b-agent-architectures)
- [Day 2a — Agent Tools](https://www.kaggle.com/code/kaggle5daysofai/day-2a-agent-tools)
- [Day 2b — Agent Tools Best Practices](https://www.kaggle.com/code/kaggle5daysofai/day-2b-agent-tools-best-practices)
- [Day 3a — Agent Sessions](https://www.kaggle.com/code/kaggle5daysofai/day-3a-agent-sessions)
- [Day 3b — Agent Memory](https://www.kaggle.com/code/kaggle5daysofai/day-3b-agent-memory)
- [Day 4a — Agent Observability](https://www.kaggle.com/code/kaggle5daysofai/day-4a-agent-observability)
- [Day 4b — Agent Evaluation](https://www.kaggle.com/code/kaggle5daysofai/day-4b-agent-evaluation)
- [Day 5a — Agent2Agent Communication](https://www.kaggle.com/code/kaggle5daysofai/day-5a-agent2agent-communication)
- [Day 5b — Agent Deployment](https://www.kaggle.com/code/kaggle5daysofai/day-5b-agent-deployment)
