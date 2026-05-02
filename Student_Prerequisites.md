## Student Prerequisites

The earlier prerequisite note is a **good quick checklist**, but for actual training delivery it is usually **not enough by itself**.

For this 5-day agents course, a student who knows only standard Python basics may still struggle with:
- asynchronous code (`async` / `await`)
- framework-style coding using agents, tools, runners, and apps
- notebook commands, JSON files, and environment setup

So a better rule is:

> A student is ready if they know normal Python **plus a small set of modern practical features** used in these notebooks.

---

## What students should already know

Students should already be comfortable with:
- variables and data types
- `if` statements
- loops
- functions
- lists and dictionaries
- imports
- basic string formatting

If they are shaky on these, they should revise them first.

---

## What students should additionally know for this course

### 1. Asynchronous Python (`async`, `await`, `async for`)

This is the **most important extra topic**.

Many notebooks run agents asynchronously. That means some functions do not return a final result immediately; they return something that must be **awaited**.

Think of it like this:
- normal function → gives result now
- async function → gives result later

### Example

```python
import asyncio

async def fetch_message() -> str:
    await asyncio.sleep(1)
    return "Hello from async code"

async def main():
    message = await fetch_message()
    print(message)

asyncio.run(main())
```

### Streaming example with `async for`

```python
import asyncio

async def event_stream():
    for i in range(3):
        await asyncio.sleep(0.2)
        yield f"event-{i}"

async def main():
    async for item in event_stream():
        print(item)

asyncio.run(main())
```

Students do not need advanced `asyncio` internals. They only need to understand how to **read and follow async code**.

---

### 2. Type hints

The notebooks often use modern type hints. Students should be able to read them without fear.

Examples:
- `list[str]` → a list of strings
- `dict[str, float]` → a dictionary with string keys and float values
- `-> None` → function returns nothing
- `-> dict[str, str]` → function returns a dictionary

### Example

```python
def create_user(name: str, age: int) -> dict[str, int | str]:
    return {"name": name, "age": age}

user = create_user("Asha", 24)
print(user)
```

Students do not need to become typing experts. They only need to **read signatures comfortably**.

---

### 3. Functions used as tools or callbacks

In this course, functions are not always called directly. Sometimes they are **passed into another object** as tools or callbacks.

That is a common pattern in agent frameworks.

### Example

```python
def multiply(a: int, b: int) -> int:
    return a * b


def run_tool(tool_function, x: int, y: int):
    result = tool_function(x, y)
    print("Tool output:", result)

run_tool(multiply, 4, 5)
```

Students should understand this idea:
- a function can be stored in a variable
- a function can be passed as input
- another piece of code can call that function later

---

### 4. Classes, objects, and method calls

The notebooks use objects such as agents, runners, apps, session services, and memory services.

Students do not need deep OOP. They only need to understand:
- how to create an object
- how to call its methods
- that objects store data and behavior together

### Example

```python
class SupportAgent:
    def __init__(self, name: str):
        self.name = name

    def respond(self, message: str) -> str:
        return f"{self.name} says: I received '{message}'"

agent = SupportAgent("HelpBot")
print(agent.respond("How do I reset my password?"))
```

---

### 5. Simple class inheritance

A few notebook patterns become easier when students understand that one class can **inherit** from another.

### Example

```python
class Logger:
    def log(self, text: str):
        print("LOG:", text)

class DebugLogger(Logger):
    def log(self, text: str):
        print("DEBUG:", text)

logger = DebugLogger()
logger.log("Agent started")
```

Students only need the basic idea that a child class can **reuse or override** parent behavior.

---

### 6. `try` / `except` for handling errors

These notebooks deal with API keys, files, sessions, servers, and tool calls. So students should understand basic error handling.

### Example

```python
try:
    number = int("123")
    print(number)
except ValueError:
    print("Could not convert value")
```

Another simple example:

```python
try:
    print(10 / 0)
except ZeroDivisionError:
    print("You cannot divide by zero")
```

They do not need advanced exception design. Basic reading and writing of `try` / `except` is enough.

---

### 7. File handling and JSON

Several notebooks create config files, eval files, and small data artifacts. Students should know how to read and write files.

### Example: text file

```python
with open("notes.txt", "w", encoding="utf-8") as f:
    f.write("Agent course notes")

with open("notes.txt", "r", encoding="utf-8") as f:
    print(f.read())
```

### Example: JSON file

```python
import json

config = {
    "model": "gemini-2.0-flash",
    "temperature": 0.2
}

with open("config.json", "w", encoding="utf-8") as f:
    json.dump(config, f, indent=2)

with open("config.json", "r", encoding="utf-8") as f:
    loaded = json.load(f)

print(loaded["model"])
```

Students should clearly understand:
- `with open(...)` safely opens files
- `json.dump()` writes JSON
- `json.load()` reads JSON

---

### 8. Environment variables

API-based notebooks often use environment variables for keys and settings.

### Example

```python
import os

os.environ["API_KEY"] = "demo-key"
print(os.environ["API_KEY"])
```

Students only need a simple understanding: environment variables store settings outside normal code variables.

---

### 9. Notebook commands in Kaggle / Jupyter

This is not advanced Python, but it is important.

Students should recognize commands like:

```bash
!pip install package_name
!adk create my_agent
!adk web
%%writefile app.py
```

These are notebook features, not normal Python syntax.

A simple explanation:
- `!` runs a shell command
- `%%writefile` writes the cell content into a file

---

## Helpful topics (good to know, but not mandatory before day 1)

These appear in some notebooks and help students feel more confident:

### HTTP requests

```python
import requests

response = requests.get("https://example.com")
print(response.status_code)
```

### Logging

```python
import logging

logging.basicConfig(level=logging.INFO)
logging.info("Application started")
```

### SQLite basics

Students do not need SQL expertise, but they should know that SQLite is a lightweight database used to store structured data.

### Base64 decoding

Useful in one notebook for image data.

```python
import base64

encoded = base64.b64encode(b"hello")
print(encoded)
print(base64.b64decode(encoded).decode())
```

---

## What students do **not** need before starting

Students do **not** need to know:
- metaclasses
- multiprocessing
- threading internals
- advanced decorators
- advanced generators
- packaging internals
- deep `asyncio` architecture

Those topics are unnecessary for understanding these course notebooks.

---

## Minimum readiness checklist

A student is ready if they can:
- read an `async def` function
- understand `await`
- follow `async for`
- read type hints like `list[str]` and `dict[str, float]`
- pass a function into another function
- create an object and call its methods
- understand a simple child class inheriting from a parent class
- use `try` / `except`
- read and write JSON files
- understand `with open(...)`
- recognize notebook commands like `!pip install` and `%%writefile`

---

## Final recommendation for training delivery

For this course, the safest prerequisite statement is:

> Students should know core Python already, and before starting the course they should revise **async Python, basic OOP, file/JSON handling, exception handling, and notebook command usage**.

If students are comfortable with those topics, they should be able to follow all 9 notebooks much more smoothly.
