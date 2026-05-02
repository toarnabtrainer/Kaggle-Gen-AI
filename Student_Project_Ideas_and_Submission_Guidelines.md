# Student Project Ideas and Submission Guidelines

In the public Kaggle capstone materials, Kaggle says capstone submissions should solve a **real-world** or **everyday productivity** problem and should demonstrate **at least three course concepts**. The public snippets also say capstone participants receive a **badge and certificate of participation**, and mention sharing the code via **GitHub or Kaggle Notebooks** plus a short **write-up** explaining the agent’s value. ([Kaggle][1])

A good student project for this course should therefore be **small, runnable, and clearly mapped to the course notebooks**: build an agent or multi-agent system, use tools, optionally add sessions/memory, and include simple evaluation. Those themes are directly reflected across the official notebooks: first agents and agent architectures, turning Python functions into tools and using agents as tools, sessions and long-term memory, observability/evaluation, and A2A/deployment. ([Kaggle][2])

## 1) StudyFlow Lite: Personal Study Planner Agent

**Level:** Easy

**What students build:**  
An agent that takes subjects, available hours, and exam dates, then creates a daily or weekly study plan. It can also remember simple preferences such as preferred study time, weak subjects, or target exam scores. This aligns naturally with the course’s first-agent, tool, session, memory, and evaluation concepts. ([Kaggle][2])

**Minimum features to require:**

- one main planner agent
- 2–3 Python function tools such as:
  - `create_schedule()`
  - `split_hours_by_subject()`
  - `recommend_breaks()`
- remember at least one user preference across turns
- 3 demo prompts
- 3 evaluation cases

**Student guidance:**  
Keep the input simple: subject list, hours available, and exam dates. Do not build a huge system. A polished planner that works reliably is better than a complex but unfinished assistant.

---

## 2) BudgetBuddy: Personal Expense & Savings Coach

**Level:** Easy to Intermediate

**What students build:**  
An agent that records expenses, summarizes spending by category, and gives simple savings advice. It is a good fit because it clearly demonstrates agent tools, persistent state or sessions, and evaluation, while staying easy to demo in a notebook without external dependencies. The course specifically covers tool-based agents, sessions for short-term memory, memory for longer-term retrieval, and evaluation as systematic testing. ([Kaggle][3])

**Minimum features to require:**

- one coach agent
- 3–4 tools such as:
  - `add_expense()`
  - `category_summary()`
  - `monthly_budget_check()`
  - `savings_tip()`
- store category limits such as food, travel, shopping
- show one multi-turn example where the agent remembers the user’s budget
- 4 evaluation cases, including one failure case

**Student guidance:**  
Ask students to use **dummy data only**. A local CSV, JSON, or SQLite file is enough. This project is strong because it shows practical usefulness and makes evaluation easy.

---

## 3) Campus Concierge: Multi-Agent Student Helpdesk

**Level:** Intermediate

**What students build:**  
A small multi-agent student support system for common campus questions such as class schedule, fees, deadlines, and office contacts. This matches the course especially well because it can demonstrate specialized collaborating agents, tool usage, sessions/memory, evaluation, and optionally A2A-style extension for advanced students. The official materials explicitly cover multi-agent systems, tools, sessions/memory, evaluation, and Agent2Agent communication. ([Kaggle][4])

**Minimum features to require:**

- one router/coordinator agent
- 2 specialist agents, for example:
  - FAQ agent
  - timetable/deadline agent
- one local knowledge source such as JSON/CSV/manual dictionary
- clear fallback: if the answer is not in the local knowledge source, say so
- 5 evaluation cases covering correct routing and incorrect/unknown queries

**Student guidance:**  
This is the best certificate-oriented intermediate project because it looks impressive without needing a large backend. For most students, keep A2A optional; a standard multi-agent notebook is already enough if it is well demonstrated and clearly explained.

## What Students Should Submit

To make these projects submission-ready, require the same 5 items for every team:

1. **One runnable Kaggle notebook** with all code in one place. Kaggle publicly mentions publishing code via GitHub or Kaggle Notebooks. ([Kaggle][5])
2. **A short write-up** with:
   - problem statement
   - why the project is useful
   - architecture
   - which 3+ course concepts were used  
   Kaggle’s public capstone snippet explicitly mentions articulating the value of the agent in a write-up. ([Kaggle][5])
3. **A concept mapping section** such as:
   - Agent / Multi-agent
   - Tools
   - Sessions or Memory
   - Evaluation  
   This helps satisfy the “at least three concepts” requirement. ([Kaggle][5])
4. **Demo prompts and outputs** showing the system actually works.
5. **At least 3–5 evaluation cases**, because the official Day 4 material frames evaluation as systematic testing across scenarios and quality criteria. ([Kaggle][6])

## Recommendation

For most students, the safest choices are:

- **StudyFlow Lite** if you want the easiest successful submission
- **BudgetBuddy** if you want a practical but still manageable intermediate project
- **Campus Concierge** if you want the strongest showcase project for training demos

The key is not originality alone. The key is **clear scope + 3 or more course concepts + runnable notebook + short write-up**. That is the most reliable way to make a capstone-style submission fit the public guidance. ([Kaggle][1])

[1]: https://www.kaggle.com/competitions/agents-intensive-capstone-project/overview?utm_source=chatgpt.com "Agents Intensive - Capstone Project"
[2]: https://www.kaggle.com/code/kaggle5daysofai/day-1a-from-prompt-to-action?utm_source=chatgpt.com "Day 1a - From Prompt to Action"
[3]: https://www.kaggle.com/code/kaggle5daysofai/day-2a-agent-tools?utm_source=chatgpt.com "Day 2a - Agent Tools"
[4]: https://www.kaggle.com/code/kaggle5daysofai/day-1b-agent-architectures?utm_source=chatgpt.com "Day 1b - Agent Architectures"
[5]: https://www.kaggle.com/competitions/agents-intensive-capstone-project?utm_source=chatgpt.com "Agents Intensive - Capstone Project"
[6]: https://www.kaggle.com/code/kaggle5daysofai/day-4b-agent-evaluation?utm_source=chatgpt.com "Day 4b - Agent Evaluation"
