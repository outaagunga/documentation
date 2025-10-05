
**complete learning roadmap** for Jac programming, starting from beginner → intermediate → advanced, with *nothing left out*.

Here’s the structured **outline of what you should learn in order**, covering everything in Jac and the Jaseci stack:

---

# 🧭 Jac Programming Learning Roadmap

## **1. Foundations (Beginner)**

Learn the basics of Jac and how it relates to Python.

* **1.1 What is Jac and Jaseci**

  * Why Jac was created (AI-first, graph-based, scale-out).
  * Jaseci runtime and stack (execution engine, cloud support).
  * Relation to Python (Jac is a Python superset).

* **1.2 Setup & Tools**

  * Installing Jaseci (`pip install jaseci`).
  * Using `jsctl` (Jaseci command-line shell).
  * Writing Jac files (`.jac`).
  * Running walkers (`jac run …`).
  * Installing `jaseci-llm` for LLM features.

* **1.3 Syntax Basics**

  * Variables and types (`str`, `int`, `list`, `dict`).
  * Operators, loops, conditionals (`if`, `for`, `while`).
  * Functions (`def`) and return values.
  * Printing and debugging (`print`).

---

## **2. Core Jac Concepts (Essential)**

Jac’s unique graph-oriented and agent-based features.

* **2.1 Nodes and Edges**

  * Declaring node and edge types (`node`, `edge`).
  * Properties/attributes inside nodes and edges (`has`).
  * Creating and linking nodes/edges at runtime.

* **2.2 Walkers**

  * What a walker is (like a mobile agent).
  * Structure: `walker mywalker { can run { … } }`.
  * Running walkers with parameters.
  * Traversing graphs with walkers (moving across edges).

* **2.3 Abilities**

  * What abilities are (methods for nodes/edges/walkers).
  * Declaring and calling abilities (`ability do_something`).
  * Difference between abilities vs functions.

* **2.4 Actions**

  * External functions (e.g. Python) called inside Jac.
  * Extending Jac with libraries through actions.

* **2.5 Memory & State**

  * Persistent graph state in Jaseci runtime.
  * How data survives across runs.
  * Using attributes to store information.

---

## **3. Programming Patterns (Intermediate)**

How to solve bigger problems with Jac.

* **3.1 Graph Programming**

  * Modeling data as nodes + edges.
  * Using walkers to traverse and query the graph.
  * Example: social network, chatbot memory.

* **3.2 Object-Spatial Programming (OSP)**

  * The philosophy: code “lives” in the graph.
  * Nodes/edges represent entities; walkers represent flow.
  * Comparing OSP to OOP (Object-Oriented Programming).

* **3.3 Organizing Programs**

  * Splitting Jac code into modules.
  * Namespaces and imports.
  * Best practices for structuring projects.

* **3.4 Error Handling**

  * Try/catch-like constructs.
  * Debugging walker flows.

---

## **4. AI Integration (Applied)**

Making AI a first-class citizen in Jac.

* **4.1 `by llm` Functions**

  * Defining LLM-backed functions.
  * Passing prompts and arguments.
  * Handling responses.

* **4.2 Prompt Engineering in Jac**

  * Building prompts programmatically from graph data.
  * Combining user input + memory.
  * Adding system instructions.

* **4.3 Other AI Backends**

  * Using HuggingFace, OpenAI, or local models.
  * Switching providers without changing Jac code.

* **4.4 Embeddings & Search**

  * Creating vector embeddings of text.
  * Storing embeddings in graph nodes.
  * Performing semantic search for context retrieval.

---

## **5. Jaseci Runtime & Cloud (Advanced)**

Deploying and scaling Jac programs.

* **5.1 Jaseci Runtime**

  * How walkers execute in the runtime.
  * The diffuse execution engine (automatic scaling).
  * Persistence and graph storage.

* **5.2 Exposing APIs**

  * Converting Jac programs into REST APIs automatically.
  * Handling requests and responses.
  * Connecting to web frontends or mobile apps.

* **5.3 Microservices & Cloud**

  * How Jaseci splits walkers into microservices automatically.
  * Running Jac code at scale.
  * Cloud deployment patterns.

---

## **6. Advanced Jac Features**

Pushing beyond the basics.

* **6.1 Custom Actions**

  * Writing Python functions as Jac actions.
  * Importing external libraries (e.g., NumPy, Pandas).
  * Mixing AI models and Jac logic.

* **6.2 Concurrency & Parallelism**

  * Running multiple walkers in parallel.
  * Coordinating tasks across graph nodes.

* **6.3 Event-driven Programming**

  * Reacting to graph changes.
  * Triggering walkers when conditions are met.

* **6.4 Security & Governance**

  * Controlling access to nodes/edges.
  * User authentication and sandboxing.

---

## **7. Building Real Projects**

Capstone-style projects that consolidate everything.

* **7.1 Chatbot with Memory** (we already drafted this).
* **7.2 Knowledge Graph AI Assistant**

  * Store facts as nodes and edges.
  * Retrieve relevant facts when answering questions.
* **7.3 AI Workflow Automation**

  * Build a pipeline (text → LLM → decision → action).
* **7.4 Multi-Agent Simulation**

  * Walkers as agents interacting in a shared graph.
* **7.5 Deploying a Jac Web API**

  * Serve a chatbot or recommendation system via REST API.

---

## **8. Mastery**

Truly advanced skills.

* **8.1 Reading the Jac Specification**

  * Dive into formal grammar and language internals.
* **8.2 Debugging & Profiling**

  * Monitoring walker execution.
  * Optimizing graph traversals.
* **8.3 Contributing to Jaseci**

  * Extending the runtime.
  * Writing new backends/adapters.

---

👉 If you follow this outline step by step, you’ll cover **everything from beginner syntax → advanced cloud deployments → AI agents in Jac.**

---

# Module 1 — Introduction to Jac & Jaseci (Full lesson script)

**Role:** Instructor (Jac & Jaseci stack expert)
**Goal of the lesson:** By the end of this module the learner will be able to explain *what Jac is*, why it exists, how it relates to Python, run Jac locally, use the Playground & CLI, and create a tiny graph (node + edge) using `with entry` and `root`. All examples reference the official Jac/Jaseci docs you uploaded.

---

## 1. Learning outcomes (what you will be able to do)

* Explain Jac’s purpose and the Object-Spatial Programming (OSP) paradigm vs classic OOP.
* Install Jac and required tooling; run a `.jac` file from the CLI.
* Use `with entry` and understand the `root` node and how the graph is created.
* Create a simple node and connect it with an edge; inspect results with the Playground visualizer.
* Show Python interop with the `::py::` escape hatch.

---

## 2. Prerequisites & quick checks

* Python 3.12+ installed (recommended).
* A terminal (bash / PowerShell).
* Recommended: VS Code + Jac extension (install via Extensions marketplace).

---

## 3. Instructor notes — short conceptual lecture (5–10 minutes)

### What is Jac?

* Jac is a Python *superset* designed for graph-first, AI-friendly programs. It keeps all of Python’s ecosystem but adds first-class constructs for graphs (nodes, edges) and mobile computation (walkers). This makes building graph/agentic applications (chat memory, knowledge graphs, agentic workflows) much simpler.

### Why Jac / Jaseci stack?

* Traditional programs move data to code; Jac inverts that — computation can *travel* to data (OSP). Jaseci provides the runtime, persistence and cloud tooling that lets Jac programs scale and be served as APIs.

### Core mental model: OOP → OSP

* OOP: classes and instances in memory. OSP: nodes and edges live *in a graph* (spatial locations) and walkers move to act on them. Nodes/edges are first-class, typed, persistent objects.

---

## 4. Hands-on setup (step-by-step) — run this with the learner

### Step A — Create & activate a virtual environment

**Linux / macOS**

```bash
python -m venv jac-env
source jac-env/bin/activate
```

**Windows (PowerShell)**

```powershell
python -m venv jac-env
.\jac-env\Scripts\Activate.ps1
```

*(Recommended for dependency isolation.)*

### Step B — Install Jac (core) and optional extras

```bash
# core Jac language
pip install jaclang

# optional - tools you will encounter later (byLLM, cloud tools)
pip install jac-cloud byllm jac-streamlit
```

(Documentation shows `jaclang` as the core package and `jac-cloud` / `byllm` for cloud/LLM features).

### Step C — Verify install & CLI basics

```bash
jac --help
jac --version
```

Basic run command:

```bash
jac run hello.jac
```

(See CLI documentation: run / serve / test / tool commands).

---

## 5. First program — Hello Jac (live coding)

1. Create `hello.jac` with this content:

```jac
# hello.jac
with entry {
    print("Hello, Jac World!");
}
```

2. Run it:

```bash
jac run hello.jac
```

**Expected output**

```
Hello, Jac World!
```

*(This verifies your environment and introduces `with entry` — Jac's entrypoint.)*

---

## 6. `with entry`, `root`, and building the graph — step-by-step demo

**Concept:** Executing the program creates an initial `root` node which anchors the program graph. From `with entry` you create nodes and connect them into the graph.

**Code example — define a node, create instances, link to root**

```jac
# nodes.jac
node Person {
    has name: str;
}

with entry {
    # Create nodes but don't add to graph yet
    alice = Person(name="Alice");
    bob   = Person(name="Bob");

    # Add them into the root graph (root exists automatically)
    root ++> alice;
    root ++> bob;

    # Create a typed edge between them
    edge FriendsWith;
    alice <+:FriendsWith:+> bob;

    print("Alice created:", alice.name);
    print("Bob created:", bob.name);
}
```

**Run**

```bash
jac run nodes.jac
```

**Notes:**

* `root ++> node` attaches the new node into the active graph.
* The `<+:EdgeType:+>` form creates a typed edge (relationship) between nodes; typed edges encode semantics, not just pointers.

---

## 7. Quick demo: Play with the Playground & Graph Visualizer (guided)

* Open the Jac Playground (if available in your environment) — paste `nodes.jac` into the editor.
* Toggle **Debug Mode** to see the **Jaclang Graph Visualizer** on the right. Set a breakpoint inside `with entry` and step through execution to watch the graph get built node-by-node.

**Instructor tip:** use breakpoints and step controls to see `root` appear, nodes being created, and edges formed — this is the single clearest way to internalize OSP.

---

## 8. Jac <> Python interop (escape hatch)

**Why:** You can reuse Python libraries and gradually adopt Jac.

**Example (inline Python in `.jac` via `::py::`):**

```jac
with entry {
    print("Jac says hello");
}

::py::
def py_helper():
    print("Hello from Python!")

py_helper()
::py::
```

This runs Python directly inside a `.jac` file — great for migration or using a specific Python-only package.

---

## 9. Short conceptual checkpoint (quick quiz)

1. What does `with entry` do in Jac?
2. What is the `root` node?
3. Name the three OSP archetypes that extend OOP in Jac.
4. How do you attach a newly created node to the program graph?
5. True/False: Jac is incompatible with existing Python libraries.

**Answers**

1. It defines the program entrypoint (start of execution) and entry into the Jac graph.
2. The automatically-created anchor node for the program graph (where you can attach nodes/edges).
3. Object classes (obj), Node classes (node), Edge classes (edge); plus Walkers as computation agents.
4. Use `root ++> <node_instance>` to add to the root graph.
5. False — Jac is a Python superset and can interoperate with Python libraries (e.g. via `::py::`).

---

## 10. Hands-on exercises (do these now)

### Exercise 1 — Verify installation (5 min)

* Run `jac --version` and `jac run hello.jac`.
* **Deliverable:** paste the `jac --version` output and the `Hello, Jac World!` output.

### Exercise 2 — Build & print small graph (15–20 min)

* Create `mini_graph.jac` with:

  * A `node City { has name: str }`
  * Instantiate 3 cities (A, B, C) and connect A -> B -> C to the root.
  * Print city names after creation.
* **Deliverable:** `mini_graph.jac` file and run output.

**Solution (hint)**

```jac
node City { has name: str; }
with entry {
    a = City(name="A");
    b = City(name="B");
    c = City(name="C");

    root ++> a;
    a ++> b;
    b ++> c;

    print("Cities:", a.name, b.name, c.name);
}
```

(See node & edge examples in docs).

### Exercise 3 — Playground debugging (15 min)

* Load your `mini_graph.jac` into the Playground, set a breakpoint on the first `root ++>` line, step through, and screenshot the Graph Visualizer showing the root and one attached node.
* **Deliverable:** screenshot (or description) of the graph at the breakpoint.

### Exercise 4 — Convert a Python function to Jac (10–15 min)

* Take this Python function and convert to Jac (add types + semicolons):

```python
def square(x):
    return x * x
print(square(5))
```

* **Deliverable:** Jac version and output.

**Solution**

```jac
def square(x: int) -> int {
    return x * x;
}

with entry {
    print(square(5));
}
```

(Expect `25`.)

---

## 11. Mini project (Module 1 capstone) — short write + code (optional)

**Title:** Why Jac? — a 1-page explanation + demo program

**Requirements**

* A one-page writeup (bullet points) explaining why Jac is a good fit for graph/agentic apps (include 3 concrete use-cases). Reference docs.
* A short Jac demo `why_jac.jac` that:

  * Defines `Person` node, creates 2 people, links them, and prints a sentence like `"Alice is friends with Bob"`.
* **Deliverable:** `why_jac.jac` + one-page writeup.

---

## 12. Common beginner gotchas & instructor tips

* **Semicolons:** Jac requires semicolons at end of statements — missing `;` is a frequent syntax error.
* **Types:** Jac is typed; add types to function parameters/returns.
* **Entry blocks:** If you forget `with entry`, nothing will run. Use multiple `with entry` blocks if you need ordered startup tasks.
* **Use the Visualizer:** The Graph Visualizer + debug mode is the fastest way to understand OSP behavior.

---

## 13. References & where to go next (docs)

* Tour & introduction (language philosophy & examples).
* `with entry`, `root`, nodes/edges examples.
* Playground & Graph Visualizer (debugging).
* CLI / install / run / serve / test reference.
* Interop & escape hatch (`::py::`) and Python integration examples.

---

## 14. Suggested follow-up lesson (Module 2 preview)

Next we will:

* Deepen syntax (functions, `has`, `obj`, access modifiers),
* Fully implement node/edge types with properties, and
* Introduce Walkers (the mobile agents that traverse graphs).
  (You’ll see agentic code and the LittleX tutorial in later modules).

---

**If you want:** I can now convert this lesson into:

* a step-by-step slide deck (speaker notes + slides), **or**
* a timed workshop script with exact instructor prompts and expected student outputs, **or**
* an expanded `why_jac.jac` starter repo containing the examples + tests.

Which of those should I produce next?

# Module 2 — Setup, Tools & Workflow (Full Lesson Script)

**Role:** Instructor (Jac & Jaseci stack expert)
**Goal of the lesson:** By the end of this module the learner will have a working Jac development environment, know how to run Jac files locally, use the CLI effectively, explore Jac Playground (with Debug & Graph Visualizer), and serve simple APIs with Jac Cloud.

---

## 1. Learning Outcomes

After Module 2, you will be able to:

* Install and configure Jac tooling (`jaclang`, `jac-cloud`, `byllm`).
* Use the Jac CLI (`jac run`, `jac serve`, `jac test`, `jac tool`).
* Explore Jac Playground and Debugger (Graph Visualizer, breakpoints).
* Organize `.jac` files properly and run them from both CLI and Playground.
* Serve your Jac code as an API with auto-generated docs (Jac Cloud).

---

## 2. Prerequisites

* Completed **Module 1**: you know what Jac is, have Python 3.12+ installed, and ran `hello.jac`.
* Internet connection (for Playground and Jac Cloud setup).

---

## 3. Instructor Mini Lecture (Concepts Overview)

**Tooling Overview**

1. **Jac CLI**: the command-line entrypoint for Jac. Think of it like `python` or `npm`. Supports running `.jac` files, serving APIs, testing, etc.
2. **Jac Playground**: browser-based IDE with Debug Mode and Graph Visualizer — best for beginners to watch graph construction.
3. **Jac Cloud**: runtime + cloud services (API, auth, persistence, metrics, scheduling). Works locally or deployed.
4. **Editor Integration**: VS Code extension highlights syntax, runs `jac` directly from the editor.

**Workflow:**

* Write `.jac` code in editor → run locally with `jac run` → debug with Playground → when ready, `jac serve` to expose API → optionally deploy to Jac Cloud.

---

## 4. Hands-on Setup — Step-by-Step

### Step A — Confirm Environment

```bash
python --version
jac --version
```

*(Output should show Python 3.12+ and Jac installed.)*

### Step B — File organization

* Place Jac files in a project directory. Example:

```
my_project/
  ├── app.jac
  ├── app.impl.jac
  └── app.test.jac
```

Interface in `.jac`, implementation in `.impl.jac`, and tests in `.test.jac`.

---

## 5. Jac CLI Commands (Demo + Explanation)

**Basic run**

```bash
jac run app.jac
```

**Serve as an API**

```bash
jac serve app.jac
```

* Starts a local server (default `http://localhost:8000`).
* API docs automatically available at `/docs`. (Open in browser).

**Run tests**

```bash
jac test app.test.jac
```

* Unit and integration tests for Jac programs. (You’ll use this more in Module 7).

**Install/use tools**

```bash
jac tool install byllm
jac tool list
```

* Tools extend Jac runtime (LLMs, RAG search, etc).

---

## 6. Playground & Debug Mode

**Opening Playground**

* Navigate to Jac Playground in browser (or bundled version if local).
* Paste your `.jac` code.
* Toggle **Debug Mode** to activate the **Graph Visualizer**.

**Features**

* Breakpoints in code.
* Step execution line-by-line.
* Watch nodes/edges being created in real time.

**Instructor Demo**

* Load this code:

```jac
node City { has name: str; }

with entry {
    a = City(name="Nairobi");
    b = City(name="Mombasa");
    root ++> a;
    a ++> b;
    print("Created two cities:", a.name, "and", b.name);
}
```

* Set breakpoint at `root ++> a;`, step execution, and watch root + City nodes appear in Graph Visualizer.

---

## 7. Serving Your First Jac API (Step-by-Step)

1. Create `greet.jac`:

```jac
def greet(name: str) -> str {
    return "Hello, " + name + "!";
}

with entry {
    print(greet("Jac Learner"));
}
```

2. Run API server:

```bash
jac serve greet.jac
```

3. Open browser: `http://localhost:8000/docs`

   * Explore auto-generated OpenAPI documentation.
   * Try the `greet` endpoint with a name parameter.

**Concept Reinforcement:**

* Without writing a Flask/FastAPI app, Jac automatically turns functions into REST endpoints.

---

## 8. Quick Knowledge Check (Mini Quiz)

1. Which command runs a `.jac` file?
2. Which command serves a `.jac` file as an API?
3. Where can you find API docs for a served Jac program?
4. What does `jac test` do?
5. How can you visualize node/edge creation?

**Answers**

1. `jac run file.jac`
2. `jac serve file.jac`
3. At `/docs` endpoint (Swagger/OpenAPI docs).
4. Runs unit/integration tests for Jac programs.
5. By using Jac Playground Debug Mode with Graph Visualizer.

---

## 9. Exercises (Hands-on Practice)

### Exercise 1 — CLI Practice

* Write `math_ops.jac`:

```jac
def add(x: int, y: int) -> int { return x + y; }
def sub(x: int, y: int) -> int { return x - y; }

with entry {
    print("Add:", add(3, 2));
    print("Sub:", sub(5, 3));
}
```

* Run: `jac run math_ops.jac`
* Serve: `jac serve math_ops.jac`
* Open `/docs` and test the API.

---

### Exercise 2 — Playground Debug

* Load `math_ops.jac` in Playground.
* Add breakpoint at `print("Add:..."`.
* Step execution and confirm both outputs print sequentially.

---

### Exercise 3 — First Test

* Create `math_ops.test.jac`:

```jac
test "addition works" {
    assert add(2, 3) == 5;
}

test "subtraction works" {
    assert sub(7, 2) == 5;
}
```

* Run: `jac test math_ops.test.jac`
* Confirm both tests pass.

---

## 10. Module 2 Mini Project (Capstone)

**Title:** “City Service API”
**Requirements:**

* Define `node City { has name: str; has population: int; }`
* Implement `def describe_city(c: City) -> str` that returns `"CityName has X people"`.
* Use `with entry` to create 2 cities and print their description.
* Serve the program as an API (`jac serve city.jac`).
* Test API in browser `/docs`.

**Deliverable:** `city.jac` program + screenshot of API docs showing `describe_city` endpoint.

---

## 11. Instructor Tips & Gotchas

* Always end Jac statements with `;`. Many “it won’t run” errors are missing `;`.
* If `jac serve` fails on port 8000, use `--port` flag (`jac serve app.jac --port 8080`).
* Organize projects early into `.jac` + `.impl.jac` + `.test.jac`. Helps scaling later.
* Encourage students to always try code in Playground to “see” the graph.

---

## 12. Where to Go Next

In **Module 3**, we’ll go deeper into **Jac syntax**:

* Variables, types, loops, conditionals.
* Functions with return values.
* Common control-flow patterns.
* More debugging with Playground.

---

**End of Module 2 Lesson Script**
:::end writing

---

Would you like me to **continue with Module 3** in the same **lesson script style (step-by-step with code + exercises)**, or do you prefer I first generate a **week-by-week teaching schedule** that maps Modules 1–13 into a full curriculum plan?

# Module 3 — Jac Syntax & Basics (Full Lesson Script)

**Role:** Instructor (Jac & Jaseci stack expert)
**Goal of the lesson:** By the end of this module, learners will be fluent in Jac’s syntax, variables, data types, control flow, and functions. They will also learn debugging basics and common beginner pitfalls (semicolons, type annotations).

---

## 1. Learning Outcomes

After Module 3, you will be able to:

* Declare and use Jac variables with explicit types.
* Use primitive and collection data types (`int`, `float`, `str`, `bool`, `list`, `dict`).
* Apply operators (`+`, `-`, `*`, `/`, `%`, comparison, logical).
* Control flow with `if`, `else`, `for`, `while`.
* Write functions with type signatures and return values.
* Debug programs with `print` and Playground breakpoints.
* Recognize and fix common syntax errors.

---

## 2. Instructor Mini Lecture (Concepts Overview)

**Jac vs Python syntax**

* Jac is a typed superset of Python. Every variable and function parameter **must** have a type.
* Statements **must end with a semicolon**.
* Blocks are enclosed in `{ }` instead of indentation.
* Example difference:

**Python**

```python
def square(x):
    return x * x
```

**Jac**

```jac
def square(x: int) -> int {
    return x * x;
}
```

**Key Principle:** Jac enforces structure for graph runtime execution and LLM integration.

---

## 3. Variables & Data Types

### Code Demo

```jac
with entry {
    x: int = 10;
    y: float = 3.14;
    name: str = "Jac Learner";
    is_ready: bool = true;

    nums: list[int] = [1, 2, 3];
    person: dict = {"name": "Alice", "age": 25};

    print(x, y, name, is_ready, nums, person);
}
```

**Instructor Note:** Always specify the type on variable declaration. Collections can be typed (`list[int]`, `dict[str, int]`).

---

## 4. Operators

**Arithmetic**

```jac
with entry {
    a: int = 10;
    b: int = 3;
    print("Add:", a + b);
    print("Sub:", a - b);
    print("Mul:", a * b);
    print("Div:", a / b);
    print("Mod:", a % b);
}
```

**Comparison & logical**

```jac
with entry {
    a: int = 5;
    b: int = 7;
    print("a < b:", a < b);
    print("a == b:", a == b);
    print("a != b:", a != b);
    print("a > 2 and b > 2:", a > 2 and b > 2);
}
```

---

## 5. Control Flow

**If / Else**

```jac
with entry {
    score: int = 75;
    if score >= 50 {
        print("Pass");
    } else {
        print("Fail");
    }
}
```

**For loop**

```jac
with entry {
    nums: list[int] = [1, 2, 3, 4];
    for n in nums {
        print("Number:", n);
    }
}
```

**While loop**

```jac
with entry {
    count: int = 0;
    while count < 3 {
        print("Count:", count);
        count = count + 1;
    }
}
```

---

## 6. Functions

**Definition & usage**

```jac
def greet(name: str) -> str {
    return "Hello, " + name + "!";
}

with entry {
    print(greet("Jac Learner"));
}
```

**Multiple parameters**

```jac
def area_circle(radius: float) -> float {
    return 3.14159 * radius * radius;
}

with entry {
    print("Area:", area_circle(5.0));
}
```

**Instructor Tip:** Every parameter and return type must be typed.

---

## 7. Debugging & Print Statements

* Use `print(...)` inside functions and loops to trace variable values.
* In Playground, set breakpoints and step through to inspect variable states.

**Code Demo**

```jac
def factorial(n: int) -> int {
    if n == 0 {
        return 1;
    } else {
        result: int = n * factorial(n - 1);
        print("factorial(", n, ") =", result);
        return result;
    }
}

with entry {
    print("Final result:", factorial(5));
}
```

Run with debugger to watch recursive calls unfold.

---

## 8. Common Syntax Errors (Gotchas)

1. **Missing semicolon**

```jac
x: int = 10   # ❌ error
x: int = 10;  # ✅ correct
```

2. **Missing type**

```jac
x = 10;       # ❌ error
x: int = 10;  # ✅ correct
```

3. **Forgetting return type in function**

```jac
def square(x: int) { return x * x; }   # ❌ error
def square(x: int) -> int { return x * x; }  # ✅ correct
```

---

## 9. Exercises (Hands-On Practice)

### Exercise 1 — Variables & Operators

* Declare two integers, perform addition, subtraction, multiplication, and division.
* Print results.

### Exercise 2 — Loops & Conditionals

* Write a program that prints numbers 1–10.
* If number is even, print `"Even"`, else `"Odd"`.

### Exercise 3 — Functions

* Write a function `is_prime(n: int) -> bool`.
* Test with inputs 2, 7, 8, 15.

---

## 10. Mini Project (Module 3 Capstone)

**Title:** “Student Grade Evaluator”

**Requirements:**

* Create a `def calculate_grade(score: int) -> str` that:

  * Returns `"A"` for score ≥ 80,
  * `"B"` for 70–79,
  * `"C"` for 60–69,
  * `"D"` for 50–59,
  * `"F"` otherwise.
* In `with entry`, create a list of scores `[45, 67, 82, 90]` and print each with its grade.

**Expected Output Example**

```
Score 45 → Grade F
Score 67 → Grade C
Score 82 → Grade A
Score 90 → Grade A
```

---

## 11. Knowledge Check (Quick Quiz)

1. What must every statement in Jac end with?
2. True/False: Types are optional in Jac.
3. Which block defines where program execution starts?
4. Write the Jac function header for a function `add(x, y)` returning an `int`.
5. Which control structure would you use to repeat code until a condition is false?

**Answers**

1. Semicolon `;`.
2. False — types are required.
3. `with entry { ... }`.
4. `def add(x: int, y: int) -> int { ... }`
5. `while` loop.

---

## 12. Instructor Tips

* Encourage learners to type-check their variables early.
* Reinforce that semicolons are **non-negotiable** in Jac.
* Always run small snippets in Playground to visualize control flow.

---

## 13. Next Steps

In **Module 4**, learners will move into **Nodes & Edges**:

* Defining node and edge types with attributes.
* Creating, linking, and traversing nodes in a graph.
* Understanding persistence of graph state.

This is where Jac starts showing its *graph-native power*.

---

**End of Module 3 Lesson Script**
:::end writing

---

Would you like me to now **expand Module 4 (Nodes & Edges)** in the same style — with step-by-step lesson, code demos, exercises, and a mini-project?

# Module 4 — Nodes & Edges (Full Lesson Script)

**Role:** Instructor (Jac & Jaseci stack expert)
**Goal of the lesson:** By the end of this module, learners will understand how to define node and edge types, assign attributes, create and link instances, and build a simple graph. They will also learn about graph persistence via the `root` node and how to visualize graphs with the Playground.

---

## 1. Learning Outcomes

After Module 4, you will be able to:

* Define `node` and `edge` classes in Jac.
* Add attributes to nodes/edges using `has`.
* Instantiate nodes and edges at runtime.
* Link nodes using different edge operators (`++>`, `<+:+>`).
* Understand how the `root` node anchors persistence.
* Visualize created graphs in Playground Debug Mode.

---

## 2. Instructor Mini Lecture (Concepts Overview)

* **Node**: Represents an entity in a graph (e.g., Person, City).
* **Edge**: Represents a relationship between two nodes (e.g., FriendsWith, Road).
* **Attributes**: Defined inside a node/edge with `has`.
* **Graph building**: Nodes are attached to the graph via `root`.
* **Persistence**: Graph structure survives between runs when served in Jac Cloud.

**Key principle:** In Jac, your *data model is the graph itself*.

---

## 3. Defining Nodes

**Example: Person node**

```jac
node Person {
    has name: str;
    has age: int;
}
```

* `node` keyword declares a new node type.
* `has` defines attributes (like fields in a class).

---

## 4. Defining Edges

**Example: FriendsWith edge**

```jac
edge FriendsWith {
    has since: int;  # year when friendship started
}
```

* `edge` keyword declares relationship type.
* Attributes can store metadata about the connection.

---

## 5. Creating Instances & Linking

**Code Demo**

```jac
node Person { has name: str; has age: int; }
edge FriendsWith { has since: int; }

with entry {
    alice = Person(name="Alice", age=25);
    bob   = Person(name="Bob", age=30);

    # Attach to root graph
    root ++> alice;
    root ++> bob;

    # Create a typed edge between them
    alice <+:FriendsWith:+> bob (since=2020);

    print(alice.name, "is friends with", bob.name);
}
```

**Explanation**

* `root ++> node` → adds node into graph anchored at root.
* `<+:EdgeType:+>` → creates an edge of that type between two nodes.
* `(since=2020)` → assigns an attribute on the edge.

---

## 6. Playground Visualization

**Instructor Demo:**

* Paste above code into Playground.
* Turn on Debug Mode and step execution.
* Watch Alice and Bob nodes appear and get connected by a `FriendsWith` edge.
* Inspect edge attribute `since=2020`.

---

## 7. Edge Operators Cheat Sheet

* `a ++> b;` — untyped edge (default link).
* `a <+:EdgeType:+> b;` — typed edge.
* `a <-- b;` — reverse direction.
* `root ++> node;` — attach node to root graph.

---

## 8. Graph Persistence with `root`

* The `root` node is always created when Jac runs.
* Any node/edge connected to root will persist in memory across executions (in Jac Cloud).
* Detached nodes (not linked to root) are temporary and discarded.

---

## 9. Hands-On Exercises

### Exercise 1 — Family Graph

* Define `node Person { has name: str; has age: int; }`
* Define `edge ParentOf`.
* Create Alice (40), Bob (20), and link Alice → Bob with `ParentOf`.
* Print “Alice is parent of Bob”.

---

### Exercise 2 — City Map

* Define `node City { has name: str; }`.
* Define `edge Road { has distance: int; }`.
* Create Nairobi, Mombasa, Kisumu.
* Connect Nairobi → Mombasa (485 km), Mombasa → Kisumu (900 km).
* Print all connections.

---

### Exercise 3 — Playground Debug

* Load your City Map into Playground.
* Add breakpoint after the first `root ++>` line.
* Step and visualize graph creation.

---

## 10. Mini Project (Module 4 Capstone)

**Title:** “Social Graph Builder”

**Requirements:**

* Define a `Person` node with `name` and `age`.
* Define `Friend` and `Follows` edges.
* Create 3 people: Alice, Bob, Carol.
* Link Alice ↔ Bob (Friend, since=2015).
* Link Carol → Alice (Follows).
* Print out relationships.

**Expected Output Example**

```
Alice is friends with Bob since 2015
Carol follows Alice
```

---

## 11. Knowledge Check (Quick Quiz)

1. What keyword defines a node type?
2. What operator attaches a node to the root graph?
3. How do you create a typed edge between two nodes?
4. Where are persistent nodes stored?
5. Can edges have attributes like nodes?

**Answers**

1. `node`
2. `root ++> node;`
3. `a <+:EdgeType:+> b;`
4. In the `root`-anchored graph.
5. Yes, edges can have attributes (`has`).

---

## 12. Instructor Tips

* Reinforce that nodes/edges are **not created in the graph until linked to root**.
* Show graph builds visually in Playground to reinforce spatial model.
* Encourage students to think in terms of relationships, not just objects.

---

## 13. Next Steps

In **Module 5**, we’ll introduce **Walkers** — the active agents that traverse graphs.

* Walker structure (`walker MyWalker { … }`).
* Passing parameters.
* Traversing edges with `visit`.
* Spawning walkers and managing walker state.

This is where Jac’s agentic power truly comes alive.

---

**End of Module 4 Lesson Script**
:::end writing

---

Would you like me to now **expand Module 5 (Walkers)** with the same lesson-script structure, including traversal demos and agent-based exercises?

# Module 5 — Walkers (Full Lesson Script)

**Role:** Instructor (Jac & Jaseci stack expert)
**Goal of the lesson:** By the end of this module, learners will understand walkers — Jac’s mobile agents that traverse graphs. They’ll learn walker structure, parameters, traversal commands, and state, and build example programs where walkers move through a graph to collect or modify data.

---

## 1. Learning Outcomes

After Module 5, you will be able to:

* Define and run walkers in Jac.
* Understand the lifecycle of a walker (`entry`, `disengage`).
* Pass parameters into walkers.
* Traverse graphs with `visit`, `spawn`, `take`, `disengage`.
* Store state inside walkers using `has`.
* Write walkers that query and update graph nodes.

---

## 2. Instructor Mini Lecture (Concepts Overview)

* **What is a walker?**
  A walker is like an *active agent* that moves through the graph, executing code at each step.

* **How walkers differ from functions:**
  Functions = pure logic.
  Walkers = mobile programs that live on graph nodes, can spawn new walkers, and traverse edges.

* **Walker anatomy:**

```jac
walker MyWalker {
    has counter: int = 0;

    can entry {
        print("Walker starting at", here);
    }

    can disengage {
        print("Walker finished, counter =", counter);
    }
}
```

* **`here`:** Built-in reference to the node the walker is currently on.
* **Lifecycle hooks:** `entry` (start), `disengage` (end).

---

## 3. Defining a Simple Walker

**Code Demo**

```jac
node Person { has name: str; }
edge FriendsWith;

walker GreetWalker {
    can entry {
        print("Hello from walker! Starting at root.");
        visit;
    }

    can Person {
        print("Visiting", here.name);
    }
}

with entry {
    alice = Person(name="Alice");
    bob   = Person(name="Bob");

    root ++> alice;
    alice <+:FriendsWith:+> bob;

    spawn GreetWalker();
}
```

**Explanation**

* `spawn GreetWalker();` → starts the walker at `root`.
* `visit;` → moves walker from `root` to its connected nodes.
* `can Person { ... }` → defines behavior when walker arrives on a `Person` node.

---

## 4. Passing Parameters

**Code Demo**

```jac
walker SayHello {
    has greeting: str;

    can entry {
        print(greeting, "from the walker!");
    }
}

with entry {
    spawn SayHello(greeting="Hi Jac Learner!");
}
```

**Key Point:** Walkers accept arguments, just like functions.

---

## 5. Walker State

**Code Demo**

```jac
walker Counter {
    has steps: int = 0;

    can entry {
        print("Starting walk...");
        visit;
    }

    can Person {
        steps = steps + 1;
        print("Step", steps, "->", here.name);
        visit;
    }

    can disengage {
        print("Traversal finished. Total steps:", steps);
    }
}
```

* `has` inside walker defines state variables.
* State persists as walker moves through nodes.

---

## 6. Traversal Commands

* **`visit;`** → move to all connected nodes.
* **`take <edge>;`** → move along a specific edge type.
* **`disengage;`** → stop execution.
* **`spawn WalkerName();`** → launch another walker.

**Example:**

```jac
can Person {
    print("Currently on", here.name);
    take FriendsWith;   # traverse only FriendsWith edges
}
```

---

## 7. Mini Example: Graph Search Walker

**Code Demo**

```jac
node Person { has name: str; }
edge FriendsWith;

walker CollectNames {
    has names: list[str] = [];

    can entry {
        visit;   # start traversal
    }

    can Person {
        names.append(here.name);
        visit;   # continue traversal
    }

    can disengage {
        print("Collected:", names);
    }
}

with entry {
    a = Person(name="Alice");
    b = Person(name="Bob");
    c = Person(name="Carol");

    root ++> a;
    a <+:FriendsWith:+> b;
    b <+:FriendsWith:+> c;

    spawn CollectNames();
}
```

**Expected Output**

```
Collected: ["Alice", "Bob", "Carol"]
```

---

## 8. Hands-On Exercises

### Exercise 1 — Greeter Walker

* Define `Person` nodes (Alice, Bob, Carol).
* Create `walker Greeter` that prints `"Hello <name>"` for each Person visited.

---

### Exercise 2 — Path Counter

* Create a graph of 5 nodes connected in a chain.
* Write `walker PathCounter` that counts how many nodes it visits.

---

### Exercise 3 — Selective Traversal

* Add `edge Knows`.
* Write a walker that only traverses `Knows` edges and ignores others.

---

## 9. Mini Project (Module 5 Capstone)

**Title:** “Friendship Explorer”

**Requirements:**

* Define `Person` nodes with names.
* Define `FriendsWith` edges.
* Implement `walker FriendExplorer` that:

  * Starts at a given person.
  * Traverses all `FriendsWith` connections.
  * Collects and prints a list of all people connected within 2 steps.

**Expected Output Example**

```
Starting from Alice
Connected friends (2 steps): Bob, Carol
```

---

## 10. Knowledge Check (Quick Quiz)

1. What is a walker in Jac?
2. What keyword spawns a walker?
3. Which keyword moves a walker across edges?
4. Where is the current node stored inside a walker?
5. True/False: Walkers can maintain internal state.

**Answers**

1. A mobile agent that traverses the graph and executes code.
2. `spawn WalkerName();`
3. `visit;` (or `take <edge>;` for specific edge).
4. In the `here` variable.
5. True.

---

## 11. Instructor Tips

* Reinforce **difference between walkers vs functions**: walkers move, functions don’t.
* Always demo with Playground — learners must *see* walkers traveling across the graph.
* Encourage experimentation with `visit`, `take`, and selective traversal.

---

## 12. Next Steps

In **Module 6**, learners will move into **Abilities & Actions**:

* Adding methods (`ability`) to nodes/edges/walkers.
* Comparing `ability` vs `def`.
* Extending Jac with Python actions (`::py::`).
* Building custom actions with external libraries.

---

**End of Module 5 Lesson Script**
:::end writing

---

Would you like me to now **expand Module 6 (Abilities & Actions)** in the same structured lesson-script format with code demos and exercises?

# Module 6 — Abilities & Actions (Full Lesson Script)

**Role:** Instructor (Jac & Jaseci stack expert)
**Goal of the lesson:** By the end of this module, learners will know how to extend Jac programs using **abilities** (object-bound methods) and **actions** (external functions, including Python). They will write reusable behaviors for nodes/edges/walkers and integrate external Python libraries into Jac.

---

## 1. Learning Outcomes

After Module 6, you will be able to:

* Define and use **abilities** inside nodes, edges, and walkers.
* Understand the difference between `ability` vs `def`.
* Call abilities on node instances and walkers.
* Write custom **actions** to bring Python functions into Jac.
* Import external Python libraries via actions.
* Use Jac’s `::py::` escape hatch.

---

## 2. Instructor Mini Lecture (Concepts Overview)

* **Abilities**: Behaviors bound to Jac objects (like methods in OOP).

  * Declared with `ability`.
  * Available on nodes, edges, and walkers.

* **Functions (`def`)**: Pure logic, not bound to an object.

* **Actions**: Bridge to external world (Python or system).

  * Declared via `::py::` block.
  * Allow integration of existing Python ecosystem (NumPy, Pandas, etc).

**Key Principle:**

* Use `ability` when logic belongs to an object.
* Use `def` for general functions.
* Use `action`/`::py::` for external integrations.

---

## 3. Abilities in Nodes

**Code Demo**

```jac
node Person {
    has name: str;
    has age: int;

    ability introduce() {
        print("Hi, my name is", name, "and I am", age, "years old.");
    }
}

with entry {
    alice = Person(name="Alice", age=25);
    root ++> alice;
    alice.introduce();
}
```

**Expected Output**

```
Hi, my name is Alice and I am 25 years old.
```

---

## 4. Abilities in Walkers

**Code Demo**

```jac
walker Greeter {
    has greeting: str = "Hello";

    ability say_hi() {
        print(greeting, "from walker at", here);
    }

    can entry {
        say_hi();
    }
}

with entry {
    spawn Greeter();
}
```

---

## 5. Comparing `ability` vs `def`

**`def` example**

```jac
def add(x: int, y: int) -> int {
    return x + y;
}
```

**`ability` example**

```jac
node Calculator {
    ability add(x: int, y: int) -> int {
        return x + y;
    }
}
```

* `def`: standalone function, called directly.
* `ability`: method bound to an object (must be called on an instance).

---

## 6. Actions (Python Interop)

### Inline Python with `::py::`

```jac
::py::
def py_greet(name):
    return f"Hello from Python, {name}!"
::py::

def greet(name: str) -> str by py_greet;
```

**Usage**

```jac
with entry {
    print(greet("Jac Learner"));
}
```

**Expected Output**

```
Hello from Python, Jac Learner!
```

---

## 7. External Libraries via Actions

**Example: NumPy integration**

```jac
::py::
import numpy as np

def py_mean(numbers):
    return float(np.mean(numbers))
::py::

def mean(numbers: list[int]) -> float by py_mean;
```

**Usage**

```jac
with entry {
    nums: list[int] = [10, 20, 30];
    print("Mean:", mean(nums));
}
```

---

## 8. Hands-On Exercises

### Exercise 1 — Node Ability

* Create a `Book` node with attributes `title` and `author`.
* Add ability `describe()` that prints `"<title>" by <author>`.
* Create 2 books and call their abilities.

---

### Exercise 2 — Walker Ability

* Create `walker Counter` with an ability `increment()` that adds +1 to its `steps`.
* Spawn walker and confirm it counts correctly.

---

### Exercise 3 — Python Action

* Write a Python action `reverse_string` that reverses a given string.
* Wrap it as a Jac function and test with `"JacLang"`.

**Expected Output**

```
gnaLcaJ
```

---

## 9. Mini Project (Module 6 Capstone)

**Title:** “Math Toolkit in Jac”

**Requirements:**

* Create `node Calculator` with abilities:

  * `add(x, y)`
  * `multiply(x, y)`
* Add Python actions for:

  * `sqrt(x)` (use `math.sqrt`)
  * `pow(x, y)` (use `math.pow`)
* In `with entry`, demonstrate all operations.

**Expected Output Example**

```
Add: 15
Multiply: 50
Sqrt(16): 4.0
Pow(2,3): 8.0
```

---

## 10. Knowledge Check (Quick Quiz)

1. What keyword declares an ability in Jac?
2. How do you attach a Python function into Jac?
3. True/False: Abilities can only exist in nodes.
4. Which block lets you embed raw Python inside Jac files?
5. Which would you use for a shared helper: `def` or `ability`?

**Answers**

1. `ability`
2. Use `::py::` and map with `by function_name`
3. False — abilities can exist in nodes, edges, and walkers.
4. `::py:: ... ::py::`
5. `def` (abilities are tied to objects, defs are general).

---

## 11. Instructor Tips

* Emphasize the OOP analogy: abilities = methods.
* Show how actions unlock Python’s full ecosystem.
* Encourage students to start small (like `math.sqrt`) before trying advanced integrations (NumPy, Pandas, ML libraries).

---

## 12. Next Steps

In **Module 7**, learners will focus on **Organizing Jac Programs**:

* Interfaces (`.jac`) vs Implementations (`.impl.jac`).
* Writing tests (`.test.jac`).
* Importing modules.
* Best practices for structuring larger projects.

---

**End of Module 6 Lesson Script**
:::end writing

---

Do you want me to **continue with Module 7 (Organizing Jac Programs)** in the same style, or pause here so you can try out Modules 1–6 exercises first?

# Module 7 — Organizing Jac Programs (Full Lesson Script)

**Role:** Instructor (Jac & Jaseci stack expert)
**Goal of the lesson:** By the end of this module, learners will know how to structure Jac codebases into modules, separate interfaces from implementations, use imports, and write unit tests. They will also practice debugging and handling errors in organized projects.

---

## 1. Learning Outcomes

After Module 7, you will be able to:

* Separate Jac code into **interface (.jac)** and **implementation (.impl.jac)** files.
* Use **imports** and `include` to organize code.
* Write and run **tests** with `jac test`.
* Apply best practices for naming, structuring, and maintaining large Jac projects.
* Debug walker flows with systematic error handling.

---

## 2. Instructor Mini Lecture (Concepts Overview)

* **Why organize code?**
  As Jac projects grow (graphs, walkers, AI agents), separating files avoids clutter and improves readability.

* **Interface vs Implementation:**

  * `.jac`: contains **API definitions** (signatures, walker interfaces, node types).
  * `.impl.jac`: contains **actual code** (walker logic, abilities, functions).
  * Pattern ensures a clear boundary like header vs implementation in C++.

* **Tests:**

  * `.test.jac` files contain assertions.
  * Run with `jac test file.test.jac`.
  * Enables test-driven development in Jac.

* **Imports & Includes:**

  * `include "file.jac";` lets you bring in definitions.
  * Organize reusable code into modules.

---

## 3. File Structure Example

```
my_project/
  ├── app.jac        # interfaces (nodes, edges, walker signatures)
  ├── app.impl.jac   # implementations (walker logic, abilities)
  ├── app.test.jac   # unit tests
```

---

## 4. Interface vs Implementation Example

**app.jac**

```jac
node Person {
    has name: str;
    has age: int;
}

walker AgeChecker {
    has min_age: int;
}
```

**app.impl.jac**

```jac
walker AgeChecker {
    can Person {
        if here.age >= min_age {
            print(here.name, "is old enough!");
        } else {
            print(here.name, "is too young!");
        }
    }
}
```

**with entry block** (in `app.impl.jac` or another file)

```jac
with entry {
    alice = Person(name="Alice", age=20);
    bob   = Person(name="Bob", age=15);

    root ++> alice;
    root ++> bob;

    spawn AgeChecker(min_age=18);
}
```

---

## 5. Imports & Includes

**Example**

```jac
include "app.jac";
include "app.impl.jac";

with entry {
    alice = Person(name="Alice", age=22);
    root ++> alice;
    spawn AgeChecker(min_age=21);
}
```

---

## 6. Writing Tests

**app.test.jac**

```jac
include "app.jac";
include "app.impl.jac";

test "Alice is old enough" {
    alice = Person(name="Alice", age=25);
    root ++> alice;
    w = AgeChecker(min_age=18);
    spawn w;
    assert alice.age >= 18;
}

test "Bob is too young" {
    bob = Person(name="Bob", age=15);
    root ++> bob;
    w = AgeChecker(min_age=18);
    spawn w;
    assert bob.age < 18;
}
```

**Run tests**

```bash
jac test app.test.jac
```

**Expected Output**

```
✓ Alice is old enough
✓ Bob is too young
All tests passed.
```

---

## 7. Debugging & Error Handling

### Common issues

* Missing `include`: leads to “symbol not found” errors.
* Forgetting semicolons or types.
* Calling a walker not imported into current scope.

### Debug strategy

* Use **Playground Debug Mode**: set breakpoints in `.impl.jac` logic.
* Print variable states at critical points.
* Keep unit tests small and focused.

---

## 8. Hands-On Exercises

### Exercise 1 — Split Project

* Create `library.jac` with node `Book { has title: str; }`.
* Create `library.impl.jac` with walker `ShowBook` that prints titles.
* Create `library.test.jac` with test that checks a book is printed.

---

### Exercise 2 — Modular Walker

* Define `node City { has name: str; }` in `city.jac`.
* Implement `walker CityPrinter` in `city.impl.jac`.
* Test it in `city.test.jac`.

---

### Exercise 3 — Error Fix

* Deliberately remove a semicolon in `city.impl.jac`.
* Run `jac run` and capture the error.
* Add semicolon back to fix.

---

## 9. Mini Project (Module 7 Capstone)

**Title:** “Student Grading System (Organized Project)”

**Requirements:**

* **student.jac**:

  * Define `node Student { has name: str; has score: int; }`.
  * Define walker `Grader`.

* **student.impl.jac**:

  * Implement `Grader` walker that prints a grade based on score.

* **student.test.jac**:

  * Test that a student with score 85 gets grade `"A"`.
  * Test that a student with score 45 gets grade `"F"`.

**Deliverable:** Organized 3-file project with passing tests.

---

## 10. Knowledge Check (Quick Quiz)

1. Which file holds **interfaces** in Jac projects?
2. Which command runs test files?
3. How do you include another Jac file?
4. True/False: You can define implementations in the `.jac` interface file.
5. What’s the advantage of separating `.jac` and `.impl.jac`?

**Answers**

1. `.jac`
2. `jac test file.test.jac`
3. `include "filename.jac";`
4. False — implementations go in `.impl.jac`.
5. Clarity, modularity, easier maintenance, testing.

---

## 11. Instructor Tips

* Show students how large apps (like the **LittleX example**) use multiple files for clarity.
* Encourage TDD (test-driven development) with `.test.jac`.
* Reinforce debugging workflows with Playground + print + tests.

---

## 12. Next Steps

In **Module 8**, learners will dive into **AI Integration with byLLM**:

* Defining functions backed by LLMs (`def ... by llm`).
* Using Meaning-Typed Programming (MTP).
* Prompt engineering with Jac.
* Multi-model support (OpenAI, HuggingFace, Ollama).

This is where Jac’s AI-first design shines.

---

**End of Module 7 Lesson Script**
:::end writing

---

Would you like me to continue straight into **Module 8 (AI Integration with byLLM)**, or pause so I expand Modules 1–7 into a **consolidated workbook** with exercises + solutions?

# Module 8 — AI Integration with byLLM (Full Lesson Script)

**Role:** Instructor (Jac & Jaseci stack expert)
**Goal of the lesson:** By the end of this module, learners will understand how to make AI (Large Language Models) a first-class citizen in Jac using the `by llm` keyword and Meaning-Typed Programming (MTP). They will learn how to call models, engineer prompts, use typed outputs, and integrate AI directly into graph workflows.

---

## 1. Learning Outcomes

After Module 8, you will be able to:

* Define Jac functions that delegate execution to an LLM with `by llm`.
* Understand **Meaning-Typed Programming (MTP)**: how Jac uses types to structure LLM prompts.
* Pass parameters to LLM-backed functions and parse typed responses.
* Combine user input with graph memory to generate contextual prompts.
* Use different backends (OpenAI, HuggingFace, Gemini, Ollama, etc.).
* Apply prompt engineering techniques in Jac.

---

## 2. Instructor Mini Lecture (Concepts Overview)

* **Why byLLM?**
  Traditional code → logic is deterministic.
  LLMs → non-deterministic but powerful for unstructured text and reasoning.
  `by llm` lets you treat an AI function like any other typed function in Jac.

* **Meaning-Typed Programming (MTP):**

  * Jac auto-generates prompts from function signatures and type annotations.
  * Ensures structured responses (LLM output must conform to return type).
  * Reduces need for fragile string parsing.

* **Example:**

```jac
def summarize(text: str) -> str by llm;
```

This declares a function where the implementation is provided by an LLM, not by code you write.

---

## 3. Simple byLLM Function

**Code Demo**

```jac
def summarize(text: str) -> str by llm;

with entry {
    doc: str = "Jac is a graph-based language designed for AI-first applications.";
    print("Summary:", summarize(doc));
}
```

**Expected Behavior**
LLM returns a short summary of the text.

---

## 4. Multi-Parameter LLM Functions

**Code Demo**

```jac
def translate(text: str, target_lang: str) -> str by llm;

with entry {
    sentence: str = "Hello, how are you?";
    print("French:", translate(sentence, "French"));
    print("Swahili:", translate(sentence, "Swahili"));
}
```

**Explanation:**

* LLM uses both `text` and `target_lang` in its prompt.
* Jac ensures structured return (must be `str`).

---

## 5. Returning Complex Types

**Code Demo**

```jac
obj Person {
    has name: str;
    has age: int;
}

def extract_person(bio: str) -> Person by llm;

with entry {
    text: str = "Alice is 30 years old and works as a software engineer.";
    p: Person = extract_person(text);
    print("Name:", p.name, "| Age:", p.age);
}
```

**Why this matters:**

* Without extra code, Jac forces the LLM to return structured data (`Person`).
* Example of **MTP** in action.

---

## 6. Combining byLLM with Graph Memory

**Code Demo**

```jac
node Conversation { has text: str; }

def reply(context: list[str], user_input: str) -> str by llm;

with entry {
    # Simulate memory
    convo1 = Conversation(text="Hello!");
    convo2 = Conversation(text="How are you?");
    root ++> convo1;
    root ++> convo2;

    context: list[str] = [convo1.text, convo2.text];
    user: str = "I'm learning Jac!";
    print("AI Reply:", reply(context, user));
}
```

**Concept:**

* Memory stored in graph nodes feeds into LLM.
* Enables chatbots with persistent context.

---

## 7. Switching AI Backends

Jac supports multiple backends through `byllm`.

* **OpenAI** (GPT-3.5, GPT-4)
* **Gemini** (Google)
* **HuggingFace** models
* **Ollama** (local models)
* **LiteLLM Proxy**

**Configuration:**

* Set environment variables (e.g., `OPENAI_API_KEY`).
* Or specify model via `Model(...)` inside Jac Cloud config.

---

## 8. Prompt Engineering in Jac

**Strategies**

* Concatenate graph data into structured strings.
* Use typed return values for reliable structure.
* Pass system instructions as part of function description.

**Example**

```jac
def classify_message(msg: str) -> str by llm
    doc = "Classify message as positive, negative, or neutral.";
```

---

## 9. Hands-On Exercises

### Exercise 1 — Summarizer

* Write `def summarize(text: str) -> str by llm;`
* Test on a paragraph of text.

---

### Exercise 2 — Translator

* Write `def translate(text: str, lang: str) -> str by llm;`
* Translate `"Good morning"` into 3 languages.

---

### Exercise 3 — Entity Extractor

* Define `obj Movie { has title: str; has year: int; }`
* Write `def extract_movie(desc: str) -> Movie by llm;`
* Test on `"Inception is a 2010 science fiction film."`

---

## 10. Mini Project (Module 8 Capstone)

**Title:** “AI-Powered Study Assistant”

**Requirements:**

* Define `def summarize_notes(notes: list[str]) -> str by llm;`
* Define `def quiz_question(topic: str) -> str by llm;`
* Store study notes as `node Note { has text: str; }`
* Create a graph with 3 notes.
* Use the functions to:

  * Summarize all notes.
  * Generate a quiz question about one topic.

**Expected Output Example**

```
Summary: The notes cover AI concepts in Jac and graph programming.
Quiz Question: Explain what a walker is in Jac.
```

---

## 11. Knowledge Check (Quick Quiz)

1. What keyword enables LLM-backed functions in Jac?
2. What is Meaning-Typed Programming (MTP)?
3. Can byLLM functions return custom object types?
4. Name two AI backends supported by Jac.
5. Why is using typed returns safer than parsing raw LLM output?

**Answers**

1. `by llm`
2. A paradigm where type annotations guide LLM behavior and enforce structure.
3. Yes, objects like `Person` or `Movie`.
4. OpenAI, HuggingFace (others: Gemini, Ollama).
5. It enforces structure and reduces parsing errors.

---

## 12. Instructor Tips

* Stress that `by llm` functions behave *exactly like normal functions* — they just offload implementation to an AI.
* Use Playground to demo outputs live (students love watching structured returns like `Person`).
* Warn learners: **LLM outputs can be non-deterministic** — always validate.

---

## 13. Next Steps

In **Module 9**, learners will dive into **Advanced AI Patterns**:

* Embeddings and semantic search.
* Retrieval-Augmented Generation (RAG).
* Multi-agent workflows.
* Building recommendation and knowledge graph assistants.

---

**End of Module 8 Lesson Script**
:::end writing

---

Would you like me to **continue with Module 9 (Advanced AI Patterns)** in the same full script style, including embeddings + RAG chatbot exercises?

# Module 9 — Advanced AI Patterns (Embeddings, RAG & Multi-Agent Workflows)

**Role:** Instructor (Jac & Jaseci stack expert)
**Goal of the lesson:** By the end of this module, learners will understand how to integrate **embeddings**, perform **semantic search**, build **Retrieval-Augmented Generation (RAG)** pipelines, and design **multi-agent AI workflows** in Jac.

---

## 1. Learning Outcomes

After Module 9, you will be able to:

* Generate and store **embeddings** in Jac graph nodes.
* Perform semantic similarity search across nodes.
* Build a **RAG pipeline** where an LLM uses graph-stored context to answer queries.
* Create multi-agent systems where walkers (agents) collaborate or compete.
* Apply these techniques to recommendation engines, assistants, and simulations.

---

## 2. Instructor Mini Lecture (Concepts Overview)

* **Embeddings:**
  Numeric vector representation of text. Used for similarity search.

* **Semantic Search:**
  Instead of keyword search, embeddings measure closeness in meaning.

* **Retrieval-Augmented Generation (RAG):**
  Combines semantic search + LLM response.

  * Step 1: Retrieve relevant facts from graph via embeddings.
  * Step 2: Pass facts into LLM prompt.

* **Multi-Agent Workflows:**

  * Walkers as **agents** with goals.
  * Can exchange messages via graph nodes.
  * Enables simulations of negotiation, collaboration, or competition.

---

## 3. Embeddings in Jac

**Code Demo**

```jac
def embed_text(text: str) -> list[float] by llm;

node Document {
    has text: str;
    has embedding: list[float];
}

with entry {
    doc1 = Document(text="Jac is a graph-based AI language.");
    doc1.embedding = embed_text(doc1.text);

    print("Embedding length:", len(doc1.embedding));
}
```

**Concept:**

* Each document gets a numerical vector.
* Vectors are stored directly in node attributes.

---

## 4. Semantic Search Example

**Code Demo**

```jac
def similarity(v1: list[float], v2: list[float]) -> float by llm;

walker Search {
    has query: str;

    can root {
        qvec = embed_text(query);

        best_doc = None;
        best_score = -1.0;

        for d in neighbors {
            score = similarity(qvec, d.embedding);
            if score > best_score {
                best_score = score;
                best_doc = d;
            }
        }

        print("Best match:", best_doc.text);
    }
}

with entry {
    d1 = Document(text="Jac is for AI-first programming.");
    d1.embedding = embed_text(d1.text);

    d2 = Document(text="Python is a general-purpose language.");
    d2.embedding = embed_text(d2.text);

    root ++> d1;
    root ++> d2;

    spawn Search(query="Which language is AI-native?");
}
```

---

## 5. RAG Pipeline in Jac

**Code Demo**

```jac
def answer_with_context(question: str, context: list[str]) -> str by llm;

walker RAG {
    has question: str;

    can root {
        # Step 1: Embed question
        qvec = embed_text(question);

        # Step 2: Retrieve top documents
        docs: list[str] = [];
        for d in neighbors {
            score = similarity(qvec, d.embedding);
            if score > 0.75 {  # simple threshold
                docs.append(d.text);
            }
        }

        # Step 3: Generate answer using context
        print("Answer:", answer_with_context(question, docs));
    }
}

with entry {
    d1 = Document(text="Jac uses walkers for graph traversal.");
    d2 = Document(text="Jaseci provides scalable AI runtime.");
    root ++> d1;
    root ++> d2;

    d1.embedding = embed_text(d1.text);
    d2.embedding = embed_text(d2.text);

    spawn RAG(question="How does Jac move across graphs?");
}
```

---

## 6. Multi-Agent Workflows

**Code Demo**

```jac
node Agent {
    has name: str;
    has role: str;
}

walker AgentTalker {
    has message: str;

    can Agent {
        print(here.name, "(", here.role, ") says:", message);

        # Pass message to next connected agent
        for n in neighbors {
            spawn AgentTalker(message=message) on n;
        }
    }
}

with entry {
    a1 = Agent(name="Alice", role="Negotiator");
    a2 = Agent(name="Bob", role="Supplier");
    a3 = Agent(name="Charlie", role="Customer");

    root ++> a1;
    a1 ++> a2;
    a2 ++> a3;

    spawn AgentTalker(message="Let's make a deal!");
}
```

**Expected Output**

```
Alice (Negotiator) says: Let's make a deal!
Bob (Supplier) says: Let's make a deal!
Charlie (Customer) says: Let's make a deal!
```

---

## 7. Hands-On Exercises

### Exercise 1 — Embedding Storage

* Create a `Quote` node with `text` and `embedding`.
* Embed 3 motivational quotes.

---

### Exercise 2 — Semantic Match

* Write a walker `FindQuote` that takes a user query and returns the most semantically similar quote.

---

### Exercise 3 — Mini-RAG

* Store 3 documents about AI.
* Ask: *“What is Jac used for?”*
* Retrieve and generate answer using context.

---

### Exercise 4 — Agent Simulation

* Create `Agent` nodes representing “Teacher”, “Student”, and “Assistant”.
* Have them pass a question through the graph, with each adding their own reply.

---

## 8. Mini Project (Module 9 Capstone)

**Title:** “AI Research Assistant with RAG”

**Requirements:**

* Store research papers as `node Paper { has title: str; has content: str; has embedding: list[float]; }`
* Build `walker AskPaper(question: str)` that:

  * Embeds the question.
  * Finds the top 2 relevant papers.
  * Calls `answer_with_context` to generate a final answer.

**Expected Output Example**

```
Question: How does Jaseci scale AI applications?
Answer: Jaseci uses a diffuse runtime engine to scale walkers across microservices automatically.
```

---

## 9. Knowledge Check (Quick Quiz)

1. What is an embedding in Jac AI workflows?
2. Which step comes *before* generating answers in RAG?
3. True/False: Walkers can simulate agents exchanging messages.
4. Which Jac keyword lets LLM return structured vectors?
5. Why is semantic search better than keyword search?

**Answers**

1. A vector representation of text for semantic similarity.
2. Retrieving relevant context using embeddings.
3. True.
4. `by llm` (with return type `list[float]`).
5. It matches meaning, not just exact words.

---

## 10. Instructor Tips

* Emphasize that **RAG makes LLMs more reliable** by grounding them in graph-stored facts.
* Show how embeddings unlock semantic memory inside Jac graphs.
* Encourage learners to experiment with **agents + embeddings** to simulate intelligent ecosystems.

---

## 11. Next Steps

In **Module 10**, learners will move into **Jaseci Runtime & Cloud Scaling**:

* How walkers execute across the diffuse runtime.
* Converting Jac programs into APIs.
* Scaling with microservices.
* Deploying to the cloud.

---

**End of Module 9 Lesson Script**
:::end writing

---

Would you like me to continue with **Module 10 (Jaseci Runtime & Cloud Scaling)** next, or do you prefer I first consolidate Modules 1–9 into a **student workbook with exercises + solutions**?


# Module 10 — Jaseci Runtime & Cloud Scaling (Full Lesson Script)

**Role:** Instructor (Jac & Jaseci stack expert)
**Goal of the lesson:** By the end of this module, learners will understand how Jac programs execute within the **Jaseci runtime**, how to expose Jac code as **APIs**, and how Jaseci supports **microservices** and **cloud scaling**. They will practice deploying their own Jac applications beyond local execution.

---

## 1. Learning Outcomes

After Module 10, you will be able to:

* Explain the **Jaseci runtime model** (graph persistence, walker execution, diffuse engine).
* Expose Jac programs as **REST APIs**.
* Understand how Jaseci auto-scales walkers into microservices.
* Deploy Jac projects in **cloud environments**.
* Connect Jac APIs to frontends and external services.

---

## 2. Instructor Mini Lecture (Concepts Overview)

* **Runtime Basics:**

  * Jaseci maintains a **persistent graph state**.
  * Walkers execute within this runtime, traversing and modifying the graph.
  * The **diffuse engine** distributes walkers for scalability.

* **Exposing APIs:**

  * Walkers can be turned into REST endpoints.
  * Makes Jac code callable from web apps, mobile apps, or other services.

* **Cloud & Microservices:**

  * Walkers = functions → Jaseci auto-manages them as microservices.
  * Scale horizontally in the cloud without rewriting Jac code.

---

## 3. Jaseci Runtime in Action

**Code Demo**

```jac
node User {
    has name: str;
    has email: str;
}

walker Register {
    has name: str;
    has email: str;

    can root {
        u = User(name=name, email=email);
        root ++> u;
        print("Registered:", u.name, u.email);
    }
}

with entry {
    spawn Register(name="Alice", email="alice@mail.com");
}
```

**Concept:**

* Persistent runtime stores the `User` node.
* State survives across runs.

---

## 4. Exposing Walkers as APIs

In Jac Cloud or local Jaseci runtime, you can **expose walkers** directly:

**Example:**

```jac
walker Greet {
    has user: str;

    can entry {
        return "Hello " + user;
    }
}
```

**Expose as API (YAML config):**

```yaml
apis:
  greet:
    walker: Greet
    method: POST
    args:
      - user
```

Now accessible at `/api/greet` with POST data:

```json
{ "user": "Alice" }
```

**Response:**

```json
"Hello Alice"
```

---

## 5. Jaseci Microservices & Diffuse Engine

* Each walker = a potential **service endpoint**.
* The runtime’s **diffuse engine** distributes walkers across resources.
* Benefits:

  * Automatic load balancing.
  * Parallel execution.
  * Cloud-native scaling without extra configuration.

**Diagram (conceptual):**

```
Client → API → Walker → Graph Runtime
                        → Diffuse Engine → Scaled Workers
```

---

## 6. Deploying to Cloud

### Local Development

* Run with `jsctl` or Playground.
* Use SQLite graph storage (default).

### Cloud Deployment

* Deploy to **Jac Cloud** or containerize with **Docker**.
* Use Postgres for persistent graph storage.
* Bind APIs to external clients (React app, mobile app).

**Deployment Example:**

```bash
docker run -p 8000:8000 jaseci/jaseci
```

API now available at `http://localhost:8000/api/`.

---

## 7. Connecting Jac APIs to Frontends

**Frontend (React) Example**

```javascript
fetch("http://localhost:8000/api/greet", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ user: "Alice" })
})
.then(res => res.json())
.then(data => console.log(data));
```

**Expected Output**

```
Hello Alice
```

---

## 8. Hands-On Exercises

### Exercise 1 — Persistent Runtime

* Create a `Product` node with `name` and `price`.
* Write `walker AddProduct` that adds a product to the graph.
* Run it twice → confirm runtime stores multiple products.

---

### Exercise 2 — API Exposure

* Expose a `walker AddProduct` as an API endpoint `/api/add_product`.
* Test with a POST request.

---

### Exercise 3 — Scaling Concept

* Write a walker `Compute` that squares a number.
* Imagine running it for 10,000 inputs → explain how Jaseci’s diffuse engine scales this.

---

## 9. Mini Project (Module 10 Capstone)

**Title:** “Cloud-Based To-Do App”

**Requirements:**

* Define `node Task { has title: str; has done: bool; }`.
* Define walkers:

  * `AddTask(title: str)`
  * `CompleteTask(title: str)`
  * `ListTasks()`
* Expose all walkers as REST APIs.
* Deploy locally (via Docker or Jac Cloud).
* Connect with a small frontend (optional).

**Expected Usage**

```json
POST /api/add_task { "title": "Buy milk" }
→ "Task added."

POST /api/complete_task { "title": "Buy milk" }
→ "Task marked as done."

GET /api/list_tasks
→ [ { "title": "Buy milk", "done": true } ]
```

---

## 10. Knowledge Check (Quick Quiz)

1. What is the diffuse engine in Jaseci?
2. Which config exposes a walker as a REST API?
3. True/False: Jaseci graphs are not persistent by default.
4. What command runs Jaseci in Docker locally?
5. How does Jaseci scale microservices automatically?

**Answers**

1. The component that distributes walker execution for scaling.
2. API config in `yaml` linking walker to endpoint.
3. False — graphs are persistent in runtime.
4. `docker run -p 8000:8000 jaseci/jaseci`
5. By treating walkers as microservices and balancing them automatically.

---

## 11. Instructor Tips

* Emphasize that **developers don’t need to manually build APIs** — Jac + Jaseci do it automatically.
* Show how persistence enables stateful applications (chatbots, todo apps, recommendation engines).
* Encourage students to experiment with both **local Docker deployment** and **Jac Cloud**.

---

## 12. Next Steps

In **Module 11**, learners will go deeper into **Advanced Jac Features**:

* Custom actions with Python integration.
* Concurrency & parallelism in walkers.
* Event-driven programming.
* Security & governance in Jac Cloud.

---

**End of Module 10 Lesson Script**
:::end writing

---

Would you like me to continue with **Module 11 (Advanced Jac Features: concurrency, events, security)** in the same full lesson script style?

# Module 11 — Advanced Jac Features (Concurrency, Events, Security)

**Role:** Instructor (Jac & Jaseci stack expert)
**Goal of the lesson:** By the end of this module, learners will master **advanced Jac features** including concurrency, event-driven programming, custom actions, and security/governance. These features allow for building robust, scalable, and secure real-world applications.

---

## 1. Learning Outcomes

After Module 11, you will be able to:

* Run multiple walkers in **parallel** using Jac concurrency.
* Trigger **event-driven workflows** based on graph changes.
* Write **custom actions** that integrate external Python libraries.
* Understand **security, permissions, and governance** in Jac Cloud.

---

## 2. Instructor Mini Lecture (Concepts Overview)

* **Concurrency & Parallelism**:

  * Walkers can run in parallel across the graph.
  * Useful for simulations, multi-agent systems, or batch processing.

* **Event-driven Programming**:

  * Walkers can be triggered automatically when certain graph conditions are met (e.g., new node creation).

* **Custom Actions**:

  * Python functions integrated into Jac to extend functionality.
  * Example: connecting Jac with ML/DL libraries.

* **Security & Governance**:

  * Control **who** can access graph nodes.
  * Use authentication, sandboxing, and governance rules in Jac Cloud.

---

## 3. Concurrency & Parallelism

**Code Demo**

```jac
walker Worker {
    has id: int;

    can root {
        print("Worker", id, "started at", now());
        # Simulate work
        sleep(2);
        print("Worker", id, "finished at", now());
    }
}

with entry {
    # Launch 3 workers in parallel
    spawn Worker(id=1);
    spawn Worker(id=2);
    spawn Worker(id=3);
}
```

**Expected Behavior**

* All 3 workers run simultaneously.
* Output interleaved depending on scheduling.

---

## 4. Event-driven Programming

**Code Demo**

```jac
node Order {
    has item: str;
    has status: str = "pending";
}

walker OrderHandler {
    can Order {
        if here.status == "pending" {
            print("Processing order for:", here.item);
            here.status = "completed";
        }
    }
}

# Event trigger: when a new Order is created, run OrderHandler
event on_create Order {
    spawn OrderHandler() on here;
}

with entry {
    o1 = Order(item="Laptop");
    o2 = Order(item="Phone");
    root ++> o1;
    root ++> o2;
}
```

**Expected Output**

```
Processing order for: Laptop
Processing order for: Phone
```

---

## 5. Custom Actions (Python Integration)

**Code Demo**

```jac
::py::
import math

def py_factorial(n):
    return math.factorial(n)
::py::

def factorial(n: int) -> int by py_factorial;

with entry {
    print("5! =", factorial(5));
}
```

**Expected Output**

```
5! = 120
```

---

## 6. Security & Governance

* **Access Control**:
  Restrict which walkers can modify certain nodes.

* **Authentication**:
  Use Jac Cloud’s API key/token system to authenticate requests.

* **Sandboxing**:
  Python actions run within a controlled environment to prevent unsafe operations.

* **Example: Role-based Walker**

```jac
node User {
    has name: str;
    has role: str;  # e.g., "admin", "guest"
}

walker AdminTask {
    can User {
        if here.role == "admin" {
            print(here.name, "performed admin task.");
        } else {
            print("Access denied for", here.name);
        }
    }
}

with entry {
    alice = User(name="Alice", role="admin");
    bob   = User(name="Bob", role="guest");
    root ++> alice;
    root ++> bob;

    spawn AdminTask() on alice;
    spawn AdminTask() on bob;
}
```

---

## 7. Hands-On Exercises

### Exercise 1 — Parallel Walkers

* Create `walker NumberDoubler` that doubles numbers in a list.
* Run 5 walkers in parallel on 5 different numbers.

---

### Exercise 2 — Event Trigger

* Create `node Task { has title: str; has done: bool; }`.
* When a task is created, auto-run a walker that prints `Task created: <title>`.

---

### Exercise 3 — Custom Action

* Write Python action `reverse_words` that reverses each word in a string.
* Wrap it in Jac and test with `"Jac makes AI simple"`.

---

### Exercise 4 — Security Rule

* Extend the `User` example: only `teacher` role can grade students.
* Deny access for other roles.

---

## 8. Mini Project (Module 11 Capstone)

**Title:** “Event-Driven Inventory System”

**Requirements:**

* Define `node Product { has name: str; has stock: int; }`.
* Define walker `RestockAlert` that prints `"Low stock: <name>"` if stock < 5.
* Create event `on_update Product` → auto-run `RestockAlert`.
* Add security: only `admin` users can change stock levels.

**Expected Flow**

```
Added product: Laptop (stock 3)
→ Event triggered
Low stock: Laptop
```

---

## 9. Knowledge Check (Quick Quiz)

1. How do you run walkers concurrently in Jac?
2. What keyword defines an event trigger?
3. Which syntax lets you write Python inside Jac?
4. What’s the purpose of sandboxing in Jac Cloud?
5. True/False: Any user can run admin-level walkers.

**Answers**

1. `spawn WalkerName()` multiple times.
2. `event on_create` / `event on_update`.
3. `::py:: ... ::py::`
4. To prevent unsafe or malicious Python execution.
5. False — must implement role checks.

---

## 10. Instructor Tips

* Show concurrency by running workers with different sleep times.
* Demonstrate how **event-driven workflows** feel like automation.
* Highlight security as **non-optional** for production systems.
* Encourage students to use custom actions for **ML, NLP, or math libraries**.

---

## 11. Next Steps

In **Module 12**, learners will finish with **Building Real Projects**:

* Chatbot with memory.
* Knowledge graph AI assistant.
* AI workflow automation.
* Multi-agent simulation.
* Deploying Jac APIs into production.

---

**End of Module 11 Lesson Script**
:::end writing

---

Do you want me to continue with **Module 12 (Building Real Projects)** in the same detailed step-by-step format, with multiple capstone examples?

# Module 12 — Building Real Projects (Capstone Applications)

**Role:** Instructor (Jac & Jaseci stack expert)
**Goal of the lesson:** By the end of this module, learners will consolidate all their skills by building **real-world projects** in Jac. These projects combine graph programming, walkers, abilities, actions, AI integration, runtime scaling, and security into practical applications.

---

## 1. Learning Outcomes

After Module 12, you will be able to:

* Build **end-to-end applications** in Jac.
* Apply **graph modeling** for real problems (chatbots, knowledge graphs, automation).
* Expose Jac programs as **APIs** for frontend/mobile integration.
* Deploy and manage applications in Jac Cloud or Docker.
* Demonstrate mastery through **capstone projects**.

---

## 2. Instructor Mini Lecture (Capstone Concepts)

* **Why Projects?**
  Learning Jac’s syntax is one thing — applying it to solve problems is where mastery happens.

* **Key Design Steps for Projects:**

  1. **Model data** as nodes/edges.
  2. **Define behavior** with walkers and abilities.
  3. **Extend functionality** with actions/AI.
  4. **Integrate** APIs or events.
  5. **Secure and scale** in Jaseci runtime.

---

## 3. Project 1 — Chatbot with Memory

**Concept:** A chatbot that remembers past conversations.

**Code Demo**

```jac
node Message { has text: str; }

def reply(context: list[str], user_input: str) -> str by llm;

walker Chatbot {
    has user_input: str;

    can root {
        # Retrieve memory
        history: list[str] = [];
        for m in neighbors {
            history.append(m.text);
        }

        # Add new message
        msg = Message(text=user_input);
        root ++> msg;

        # AI reply
        print("AI:", reply(history, user_input));
    }
}

with entry {
    spawn Chatbot(user_input="Hello!");
    spawn Chatbot(user_input="What can you do?");
}
```

---

## 4. Project 2 — Knowledge Graph Assistant

**Concept:** Store facts as nodes and query them with RAG.

**Code Demo**

```jac
node Fact { has text: str; has embedding: list[float]; }

def answer_with_context(question: str, context: list[str]) -> str by llm;

walker KnowledgeAssistant {
    has question: str;

    can root {
        qvec = embed_text(question);

        # Retrieve relevant facts
        context: list[str] = [];
        for f in neighbors {
            if similarity(qvec, f.embedding) > 0.7 {
                context.append(f.text);
            }
        }

        print("Answer:", answer_with_context(question, context));
    }
}

with entry {
    f1 = Fact(text="Jac uses walkers to traverse graphs.");
    f1.embedding = embed_text(f1.text);

    f2 = Fact(text="Jaseci enables scaling AI applications.");
    f2.embedding = embed_text(f2.text);

    root ++> f1;
    root ++> f2;

    spawn KnowledgeAssistant(question="How does Jac move across graphs?");
}
```

---

## 5. Project 3 — AI Workflow Automation

**Concept:** Automate a pipeline: text → LLM analysis → decision → action.

**Workflow Example**

* Input: Email text.
* Step 1: Classify sentiment.
* Step 2: If negative → send alert.

**Code Demo**

```jac
def classify_email(email: str) -> str by llm;

walker EmailProcessor {
    has email: str;

    can root {
        sentiment = classify_email(email);

        if sentiment == "negative" {
            print("ALERT: Negative email detected!");
        } else {
            print("Email OK:", email);
        }
    }
}

with entry {
    spawn EmailProcessor(email="I am unhappy with this service!");
}
```

---

## 6. Project 4 — Multi-Agent Simulation

**Concept:** Multiple AI-driven agents interact in a graph.

**Code Demo**

```jac
node Agent { has name: str; has role: str; }

def generate_response(agent: str, msg: str) -> str by llm;

walker AgentTalk {
    has message: str;

    can Agent {
        response = generate_response(here.role, message);
        print(here.name, "(", here.role, ") says:", response);

        for n in neighbors {
            spawn AgentTalk(message=response) on n;
        }
    }
}

with entry {
    a1 = Agent(name="Alice", role="Negotiator");
    a2 = Agent(name="Bob", role="Supplier");

    root ++> a1;
    a1 ++> a2;

    spawn AgentTalk(message="Let's start the deal.");
}
```

---

## 7. Project 5 — Deploying Jac Web API

**Concept:** Expose Jac walkers as REST APIs.

**Example API Config**

```yaml
apis:
  add_task:
    walker: AddTask
    method: POST
    args: [title]
  list_tasks:
    walker: ListTasks
    method: GET
```

**Walker Example**

```jac
node Task { has title: str; has done: bool = false; }

walker AddTask {
    has title: str;
    can root {
        t = Task(title=title);
        root ++> t;
        return "Task added: " + title;
    }
}

walker ListTasks {
    can root {
        tasks: list[str] = [];
        for t in neighbors {
            tasks.append(t.title);
        }
        return tasks;
    }
}
```

Frontend/mobile can now consume Jac APIs just like any REST service.

---

## 8. Hands-On Exercises

### Exercise 1 — Chatbot Enhancement

* Modify chatbot to store **user name** in conversation nodes.
* Respond with personalized replies.

### Exercise 2 — Knowledge Graph Expansion

* Add 3 more facts.
* Query: “What does Jaseci provide for AI scaling?”

### Exercise 3 — Workflow Automation

* Extend `EmailProcessor` to also tag emails as `"support"`, `"sales"`, or `"other"`.

### Exercise 4 — Multi-Agent

* Add a third agent (Customer).
* Have negotiation flow through all 3 agents.

### Exercise 5 — API Integration

* Expose chatbot as `/api/chat`.
* Test with a POST request.

---

## 9. Capstone Challenge (Final)

**Title:** “AI-Powered Personal Assistant”

**Requirements:**

* Memory graph of conversations.
* Knowledge graph of tasks, notes, and facts.
* RAG for answering factual questions.
* Multi-agent for simulating assistants with different roles (planner, advisor, researcher).
* Expose API endpoints for:

  * `/api/chat` (chatbot)
  * `/api/notes` (knowledge graph queries)
  * `/api/tasks` (task management)

This combines **Modules 1–11** into a single intelligent system.

---

## 10. Knowledge Check (Quick Quiz)

1. What are the **5 example projects** in this module?
2. Which project demonstrates **RAG**?
3. Which project shows **workflow automation** with decision rules?
4. How can Jac programs be made accessible to frontend clients?
5. What’s the final capstone project about?

**Answers**

1. Chatbot with memory, Knowledge Graph Assistant, Workflow Automation, Multi-Agent Simulation, Web API Deployment.
2. Knowledge Graph Assistant.
3. Email Processor (Workflow Automation).
4. By exposing walkers as REST APIs.
5. AI-Powered Personal Assistant.

---

## 11. Instructor Tips

* Encourage learners to **choose one project** and expand it further.
* Emphasize **API exposure** as the bridge to real-world use.
* Show examples of **Jac Cloud deployment** so learners can showcase apps.

---

## 12. Wrap-Up

This module concludes the **Jac Programming Roadmap**:

* Learners have gone from **syntax basics → advanced AI → runtime scaling → production-ready projects**.
* Next steps:

  * Explore **Jac open-source community**.
  * Contribute to **Jaseci runtime**.
  * Build **portfolio-ready projects**.

---

**End of Module 12 Lesson Script**
:::end writing

---

✅ This was the **final module in your Jac & Jaseci roadmap**. Do you want me to now create a **consolidated workbook** (Modules 1–12 exercises + solutions) so learners can practice everything step by step?


# Jac & Jaseci Consolidated Workbook

**Modules 1–12 Exercises + Solutions**

This workbook brings together all **hands-on exercises** from Modules 1–12 with **step-by-step solutions**. It is designed as a practice companion for learners to apply Jac programming concepts progressively.

---

# 📘 Module 1 — Foundations

### Exercise 1 — Hello Jac

**Task:** Write a Jac program that prints `"Hello Jac"`.

**Solution:**

```jac
with entry {
    print("Hello Jac");
}
```

---

### Exercise 2 — Variables

**Task:** Create variables `x=5`, `y=3`, print their sum.

**Solution:**

```jac
with entry {
    x:int = 5;
    y:int = 3;
    print("Sum:", x+y);
}
```

---

# 📘 Module 2 — Nodes & Edges

### Exercise 1 — Simple Graph

**Task:** Create `Person` node with attribute `name`, link two people.

**Solution:**

```jac
node Person { has name: str; }

with entry {
    alice = Person(name="Alice");
    bob = Person(name="Bob");
    root ++> alice;
    alice ++> bob;
}
```

---

# 📘 Module 3 — Walkers

### Exercise 1 — Traversal

**Task:** Write a walker `Greeter` that prints node names.

**Solution:**

```jac
node Person { has name: str; }

walker Greeter {
    can Person {
        print("Hello", here.name);
    }
}

with entry {
    alice = Person(name="Alice");
    bob   = Person(name="Bob");
    root ++> alice;
    alice ++> bob;
    spawn Greeter() on root;
}
```

---

# 📘 Module 4 — Abilities

### Exercise 1 — Node Ability

**Task:** Add `introduce()` ability to `Person`.

**Solution:**

```jac
node Person {
    has name: str;
    has age: int;

    ability introduce() {
        print("Hi, I'm", name, "and I'm", age);
    }
}

with entry {
    alice = Person(name="Alice", age=25);
    alice.introduce();
}
```

---

# 📘 Module 5 — Memory & State

### Exercise — Persistent Graph

**Task:** Add 2 `Note` nodes. Run twice; confirm graph keeps both.

**Solution:**

```jac
node Note { has text: str; }

with entry {
    n = Note(text="Remember Jac!");
    root ++> n;
}
```

*Run multiple times — new nodes persist.*

---

# 📘 Module 6 — Abilities & Actions

### Exercise 1 — Book Node

**Solution:**

```jac
node Book {
    has title: str;
    has author: str;

    ability describe() {
        print("\"" + title + "\" by " + author);
    }
}

with entry {
    b1 = Book(title="1984", author="Orwell");
    root ++> b1;
    b1.describe();
}
```

---

### Exercise 3 — Python Action

**Solution:**

```jac
::py::
def reverse_string(s):
    return s[::-1]
::py::

def reverse_string_jac(s: str) -> str by reverse_string;

with entry {
    print(reverse_string_jac("JacLang"));
}
```

---

# 📘 Module 7 — Organizing Projects

### Exercise — Student Grading System

**student.jac**

```jac
node Student { has name: str; has score: int; }
walker Grader { has threshold:int; }
```

**student.impl.jac**

```jac
walker Grader {
    can Student {
        if here.score >= threshold {
            print(here.name, "passed");
        } else {
            print(here.name, "failed");
        }
    }
}
```

**student.test.jac**

```jac
include "student.jac";
include "student.impl.jac";

test "Alice passed" {
    alice = Student(name="Alice", score=85);
    root ++> alice;
    spawn Grader(threshold=50);
    assert alice.score >= 50;
}
```

---

# 📘 Module 8 — AI with byLLM

### Exercise — Translator

**Solution:**

```jac
def translate(text: str, lang: str) -> str by llm;

with entry {
    print("French:", translate("Good morning", "French"));
}
```

---

# 📘 Module 9 — Advanced AI (Embeddings, RAG)

### Exercise — Find Quote

**Solution:**

```jac
node Quote { has text: str; has embedding: list[float]; }

walker FindQuote {
    has query: str;
    can root {
        qvec = embed_text(query);
        best = None; score = -1.0;
        for q in neighbors {
            s = similarity(qvec, q.embedding);
            if s > score { score = s; best = q; }
        }
        print("Best Quote:", best.text);
    }
}
```

---

# 📘 Module 10 — Runtime & Cloud

### Exercise — Product API

**Task:** Expose `AddProduct` walker as `/api/add_product`.

**Solution (walker):**

```jac
node Product { has name: str; has price: int; }

walker AddProduct {
    has name: str; has price: int;
    can root {
        p = Product(name=name, price=price);
        root ++> p;
        return "Added product: " + name;
    }
}
```

**YAML API config**

```yaml
apis:
  add_product:
    walker: AddProduct
    method: POST
    args: [name, price]
```

---

# 📘 Module 11 — Advanced Features

### Exercise — Reverse Words Action

**Solution:**

```jac
::py::
def reverse_words(s):
    return " ".join([w[::-1] for w in s.split()])
::py::

def reverse_words_jac(s: str) -> str by reverse_words;

with entry {
    print(reverse_words_jac("Jac makes AI simple"));
}
```

---

# 📘 Module 12 — Projects

### Exercise — Multi-Agent with Customer

**Solution:**

```jac
node Agent { has name: str; has role: str; }

walker AgentTalk {
    has msg: str;
    can Agent {
        print(here.name, "(", here.role, ") says:", msg);
        for n in neighbors { spawn AgentTalk(msg=msg) on n; }
    }
}

with entry {
    teacher = Agent(name="Tina", role="Teacher");
    student = Agent(name="Sam", role="Student");
    customer = Agent(name="Chris", role="Customer");
    root ++> teacher; teacher ++> student; student ++> customer;
    spawn AgentTalk(msg="Hello class!");
}
```

---

# ✅ Capstone Challenge Solution Sketch

**AI-Powered Personal Assistant**

* Nodes: `Note`, `Task`, `Message`.
* Walkers:

  * `Chatbot` → memory-based LLM responses.
  * `TaskManager` → add/complete/list tasks.
  * `KnowledgeAssistant` → RAG-based answers.
* Exposed APIs: `/api/chat`, `/api/tasks`, `/api/notes`.

This integrates all concepts: graph persistence, byLLM, RAG, events, actions, APIs, and cloud deployment.

---

# 🎯 Final Tip

Work through each module step by step.

* **Run code snippets** in Playground or `jsctl`.
* **Test persistence** by running multiple times.
* **Modify exercises** into mini-projects for mastery.

This workbook = **your full practice guide for Jac & Jaseci**.

:::end writing


Perfect 🎯 Let’s take the **AI-Powered Personal Assistant** (from Module 12) and **refine it into a production-ready capstone project** you can deploy and showcase.

---

# 🚀 Capstone Project Plan: AI-Powered Personal Assistant

### 1. **Project Concept**

A personal assistant that can:

* Chat with memory (`Chatbot`).
* Manage tasks (`TaskManager`).
* Store and query notes (`KnowledgeAssistant` with RAG).
* Expose REST APIs for integration with frontends.

---

### 2. **System Design**

**Graph Model:**

* `Message` → Stores chat history.
* `Task` → Task manager (title, done flag).
* `Note` → Study/work notes with embeddings.

**Walkers:**

* `Chatbot` → Handles conversations (uses byLLM + memory).
* `TaskManager` → Add, complete, list tasks.
* `KnowledgeAssistant` → Embedding-based RAG for answering questions.

**APIs:**

* `/api/chat` → Chatbot.
* `/api/tasks/add`, `/api/tasks/complete`, `/api/tasks/list` → Task API.
* `/api/notes/query` → Knowledge assistant.

---

### 3. **Implementation Plan**

#### **Step 1 — Define Graph Nodes**

```jac
node Message { has text: str; }
node Task { has title: str; has done: bool = false; }
node Note { has text: str; has embedding: list[float]; }
```

---

#### **Step 2 — Chatbot Walker**

```jac
def reply(context: list[str], user_input: str) -> str by llm;

walker Chatbot {
    has user_input: str;

    can root {
        # Load memory
        history: list[str] = [];
        for m in neighbors { history.append(m.text); }

        # Save new message
        msg = Message(text=user_input);
        root ++> msg;

        # AI response
        return reply(history, user_input);
    }
}
```

---

#### **Step 3 — Task Manager Walkers**

```jac
walker AddTask {
    has title: str;
    can root {
        t = Task(title=title);
        root ++> t;
        return "Task added: " + title;
    }
}

walker CompleteTask {
    has title: str;
    can root {
        for t in neighbors {
            if t.title == title {
                t.done = true;
                return "Task completed: " + title;
            }
        }
        return "Task not found: " + title;
    }
}

walker ListTasks {
    can root {
        result: list[dict] = [];
        for t in neighbors {
            result.append({ "title": t.title, "done": t.done });
        }
        return result;
    }
}
```

---

#### **Step 4 — Knowledge Assistant (RAG)**

```jac
def embed_text(text: str) -> list[float] by llm;
def answer_with_context(q: str, ctx: list[str]) -> str by llm;

walker KnowledgeAssistant {
    has question: str;

    can root {
        qvec = embed_text(question);
        ctx: list[str] = [];

        for n in neighbors {
            if n.typename == "Note" {
                score = similarity(qvec, n.embedding);
                if score > 0.7 { ctx.append(n.text); }
            }
        }
        return answer_with_context(question, ctx);
    }
}
```

---

### 4. **Expose as APIs**

**YAML config:**

```yaml
apis:
  chat:
    walker: Chatbot
    method: POST
    args: [user_input]

  add_task:
    walker: AddTask
    method: POST
    args: [title]

  complete_task:
    walker: CompleteTask
    method: POST
    args: [title]

  list_tasks:
    walker: ListTasks
    method: GET

  query_notes:
    walker: KnowledgeAssistant
    method: POST
    args: [question]
```

---

### 5. **Deployment Options**

1. **Local (Docker):**

```bash
docker run -p 8000:8000 jaseci/jaseci
```

APIs available at `http://localhost:8000/api/`.

2. **Jac Cloud:**

* Upload your `.jac` + `.impl.jac`.
* Configure API routes.
* Test with Postman or frontend.

---

### 6. **Showcasing**

* **GitHub Repo:** Include:

  * `/src` with `.jac` files.
  * `/tests` with `.test.jac`.
  * `README.md` → Project description, API docs, screenshots.

* **Demo:**

  * Deploy to Jac Cloud.
  * Record a short Loom video showing chatbot + tasks + knowledge query.

* **Share:**

  * Post on LinkedIn / Portfolio: *“Built an AI-Powered Personal Assistant in Jac & Jaseci.”*
  * Include screenshots of API calls & responses.

---

### 7. **Stretch Goals (Optional)**

* Add **multi-agent simulation** (e.g., assistant roles: planner, researcher).
* Integrate **external Python libraries** (e.g., calendar API for reminders).
* Build a **simple React frontend** with `/api/chat` integration.

---

✅ If you follow this, you’ll have a **deployable AI Assistant** you can showcase as your **flagship Jac project**.

Would you like me to draft the **GitHub-ready README template** (with setup, usage, API docs, screenshots sections) for this project?










