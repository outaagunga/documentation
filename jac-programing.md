
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

Do you want me to now **turn this into a study plan** (like Week 1 → Week 2, with exercises for each), or keep it as a reference outline?

# Module 1 — Foundations (Code-Along)

Welcome — this module gets you from “nothing” to running simple Jac code and understanding the language basics. Follow each step in order and copy/paste the code snippets into your editor / terminal. I’ll show commands, **Jac** files, `jsctl` usage, and small exercises with expected output so you can verify you’re on track.

---

## Learning objectives (what you’ll be able to do after Module 1)

* Install Jaseci and start the `jsctl` shell.
* Create and run a `.jac` file from `jsctl`.
* Read and write basic Jac constructs: `walker`, `def`, `print`, variables, types, conditionals, loops, and simple functions.
* Understand the relationship between Jac and Python (why Jac feels familiar).
* Debug common installation/runtime issues.

---

# 1. Environment & installation

### Step 1.1 — Create a project folder and virtual environment

Open a terminal and run:

```bash
mkdir jac-module1
cd jac-module1

# create & activate venv
python3 -m venv .venv
# macOS / Linux
source .venv/bin/activate
# Windows (PowerShell)
# .venv\Scripts\Activate.ps1
```

### Step 1.2 — Install Jaseci

Install Jaseci and the LLM adapter (we’ll only use LLM later modules; installing now avoids interruptions):

```bash
pip install jaseci jaseci-llm
```

### Step 1.3 — Verify `jsctl`

Start the Jaseci control shell:

```bash
jsctl
```

You should see a prompt like:

```
jsctl>
```

If `jsctl` is not found: ensure the virtual environment is active and `pip install` succeeded. If install fails, copy the error and ask me — I’ll help debug.

---

# 2. Hello World — first Jac file (code-along)

### Step 2.1 — Create `hello.jac`

Create a file named `hello.jac` with this content:

```jac
# hello.jac
walker hello_world {
    can run {
        print("Hello, World!");
    }
}
```

Save it.

### Step 2.2 — Run it in `jsctl`

At the `jsctl>` prompt run:

```
jsctl> jac run hello.jac -walk hello_world
```

Expected output:

```
Hello, World!
```

If that prints, congrats — you ran your first Jac walker!

---

# 3. Basic syntax and primitives

Jac syntax will feel familiar if you know Python. Below are the minimal building blocks.

## 3.1 Variables and types

Create `basic_types.jac`:

```jac
walker show_types {
    can run {
        s = "a string";
        n = 42;
        f = 3.14;
        b = true;      # boolean
        lst = [1,2,3]; # list

        print("string: " + s);
        print("int: " + str(n));
        print("float: " + str(f));
        print("bool: " + str(b));
        print("list: " + str(lst));
    }
}
```

Run:

```
jsctl> jac run basic_types.jac -walk show_types
```

Notes:

* `str()` converts to string (like Python).
* Comments use `#`.

## 3.2 Conditionals (`if` / `else`)

Create `conditionals.jac`:

```jac
walker check_number {
    can run(x: int) {
        if (x > 0) {
            print("positive");
        } elif (x == 0) {
            print("zero");
        } else {
            print("negative");
        }
    }
}
```

Run with argument (json args):

```
jsctl> jac run conditionals.jac -walk check_number -args '{"x": -5}'
# Expected: negative
```

## 3.3 Loops (`for`)

Create `loops.jac`:

```jac
walker loop_demo {
    can run {
        sum = 0;
        for i in [1,2,3,4,5] {
            sum = sum + i;
        }
        print("sum=" + str(sum));  # sum=15
    }
}
```

Run:

```
jsctl> jac run loops.jac -walk loop_demo
```

## 3.4 Functions (`def`) and return values

Create `functions.jac`:

```jac
def add(a: int, b: int) -> int {
    return a + b;
}

walker use_add {
    can run {
        r = add(10, 7);
        print("10 + 7 = " + str(r));
    }
}
```

Run:

```
jsctl> jac run functions.jac -walk use_add
# Expected: 10 + 7 = 17
```

---

# 4. Intro to nodes/edges/walkers (preview — full coverage in Module 2)

We’ll only touch the basics here because nodes/edges are central and deserve a full module.

Create `mini_graph.jac`:

```jac
node person {
    has name: str;
    has age: int;
}

walker graph_demo {
    can run {
        p = person.create(name="Alice", age=30);
        print("Person created: " + p.name + ", age " + str(p.age));
    }
}
```

Run:

```
jsctl> jac run mini_graph.jac -walk graph_demo
# Expected: Person created: Alice, age 30
```

This shows how to declare a `node` type and instantiate it. We'll explore edges and traversal next module.

---

# 5. I/O and running with arguments

### Passing arguments to walkers

We used `-args` earlier. Example with string:

```jac
# greet.jac
walker greet {
    can run(user_name: str) {
        print("Hello, " + user_name + "!");
    }
}
```

Run:

```
jsctl> jac run greet.jac -walk greet -args '{"user_name":"Outa"}'
# Expected: Hello, Outa!
```

### Alternative: hard-code values for quick testing

You can temporarily hard-code values in a walker when experimenting.

---

# 6. Small exercises (do these now)

Do these exercises in new files, run them, and verify output.

### Exercise 1 — Palindrome checker

Create `palindrome.jac` that has a walker `check_pal` which accepts `s: str` and prints `true` if `s` is a palindrome ignoring case/spaces.

**Hint:** use `lower()` and `replace()` if available; if not, implement simple logic.

Expected behavior:

```
input: "Racecar" -> prints true
```

### Exercise 2 — Factorial (loop)

Create `fact.jac` with a function `fact(n)` that returns factorial using a loop (no recursion required). Test with `n=5` -> prints 120.

### Exercise 3 — List filter

Create `filter_even.jac` that loops through `[1..10]` or a fixed list and prints only even numbers.

---

# 7. Debugging tips & common errors

* `jsctl` command not found: ensure virtualenv activated and `pip install jaseci` succeeded. Run `pip show jaseci`.
* `Syntax` errors: check braces `{ }` and parentheses; Jac is whitespace-insensitive but braces matter.
* Passing args: ensure your JSON string is valid (double quotes). Example: `-args '{"x": 3}'`.
* If Walker fails silently: check `jsctl` logs, and add `print()` statements to trace.

---

# 8. Quick reference cheatsheet (copy this)

* Run file and walker:

  ```
  jac run file.jac -walk walker_name -args '{"param":value}'
  ```
* Define walker:

  ```jac
  walker name {
      can run(...) { ... }
  }
  ```
* Define function:

  ```jac
  def foo(a: int) -> int { return a * 2; }
  ```
* Create node:

  ```jac
  node item { has name: str; }
  i = item.create(name="X");
  ```

---

# 9. What I expect you to do next (no waiting, do this now)

1. Follow steps 1–3 above, run `hello.jac`.
2. Work through the basic snippets (types, conditionals, loops, functions).
3. Complete the three exercises and paste any errors or unexpected outputs here — I’ll debug with you.

If you want, I can also:

* Provide **solutions** to the exercises (ready to paste).
* Walk you through **Module 2** immediately (nodes, edges, walkers in depth) once you confirm Module 1 runs.

Which would you like: exercise solutions now, or proceed to Module 2?


Perfect 🚀 Let’s move on to **Module 2 — Core Jac Concepts**.
Here we dive into the *heart* of Jac: **nodes, edges, and walkers**. These are what make Jac different from normal languages — they let you model data as a graph and move intelligent agents (“walkers”) through that graph.

---

# Module 2 — Core Jac Concepts (Code-Along)

## Learning Objectives

By the end of this module, you’ll be able to:

* Define **nodes** and **edges** with attributes.
* Instantiate nodes and connect them with edges.
* Write **walkers** that traverse and modify graphs.
* Add **abilities** (methods) to nodes and walkers.
* Understand persistent state in Jaseci graphs.

---

# 1. Nodes and Edges

In Jac, you declare **nodes** and **edges** similar to classes in OOP.

### Step 1.1 — Define a simple node

Create `person_graph.jac`:

```jac
node person {
    has name: str;
    has age: int;
}
```

* `node` declares a node type.
* `has` declares attributes.

### Step 1.2 — Create nodes inside a walker

Extend `person_graph.jac`:

```jac
walker make_people {
    can run {
        alice = person.create(name="Alice", age=30);
        bob = person.create(name="Bob", age=25);

        print("Created " + alice.name + " and " + bob.name);
    }
}
```

Run:

```
jsctl> jac run person_graph.jac -walk make_people
# Expected: Created Alice and Bob
```

---

### Step 1.3 — Define an edge

Edges connect nodes.

Add to `person_graph.jac`:

```jac
edge friend {
    has since: int;
}
```

### Step 1.4 — Connect nodes with edges

Inside the walker:

```jac
walker connect_people {
    can run {
        alice = person.create(name="Alice", age=30);
        bob = person.create(name="Bob", age=25);

        alice -[friend(since=2020)]-> bob;

        print(alice.name + " is friends with " + bob.name);
    }
}
```

Run:

```
jsctl> jac run person_graph.jac -walk connect_people
# Expected: Alice is friends with Bob
```

---

# 2. Walkers

Walkers are mobile programs that move through your graph.

### Step 2.1 — Traverse an edge

Add a walker:

```jac
walker traverse_friend {
    can run {
        alice = person.create(name="Alice", age=30);
        bob = person.create(name="Bob", age=25);
        alice -[friend(since=2020)]-> bob;

        with entry { alice };
        take --> friend;     # move along the 'friend' edge
        print("Walker at: " + here().name);
    }
}
```

Run:

```
jsctl> jac run person_graph.jac -walk traverse_friend
# Expected: Walker at: Bob
```

**Concepts:**

* `with entry { ... }` sets starting node(s).
* `take --> friend;` moves walker along edge type `friend`.
* `here()` returns the current node.

---

# 3. Abilities

Abilities are like methods tied to nodes, edges, or walkers.

### Step 3.1 — Ability on a node

Add to `person_graph.jac`:

```jac
node person {
    has name: str;
    has age: int;

    ability introduce {
        print("Hi, I'm " + name + ", age " + str(age));
    }
}
```

### Step 3.2 — Call the ability from a walker

```jac
walker test_ability {
    can run {
        alice = person.create(name="Alice", age=30);
        alice.introduce();
    }
}
```

Run:

```
jsctl> jac run person_graph.jac -walk test_ability
# Expected: Hi, I'm Alice, age 30
```

---

# 4. Actions (preview)

Actions let you call external (Python) functions. For now, use built-in `print`, but remember this because in later modules we’ll integrate LLMs and libraries as actions.

---

# 5. Memory & State

One of Jac’s powers: your graph persists between runs if you don’t reset it.

### Step 5.1 — Create persistent nodes

Run:

```
jsctl> jac run person_graph.jac -walk make_people
```

Then run:

```
jsctl> jac run person_graph.jac -walk make_people
```

You may notice duplicate people unless you handle uniqueness. That’s because the graph keeps state.

We’ll later learn how to **search graph state** and avoid duplication.

---

# 6. Exercises (try these now)

### Exercise 1 — Family graph

* Create node types: `parent`, `child`.
* Create edge type: `has_child`.
* Build a walker that creates a small family (e.g. Alice → Bob → Carol).
* Traverse the graph to print family relationships.

### Exercise 2 — Social network

* Create `person` nodes with `friend` edges.
* Write a walker that, given a person, prints all their friends.
* Bonus: traverse friends-of-friends.

### Exercise 3 — Node ability

* Add an ability `birthday` to `person` that increments `age`.
* Write a walker that creates a person, calls `.birthday()`, and prints the new age.

---

# 7. Debugging tips

* If walker doesn’t move: check your `take -->` statement matches the edge type.
* If attribute missing: ensure you declared `has` correctly.
* If graph keeps growing unexpectedly: reset state with `graph reset;` in `jsctl`.

---

✅ That’s Module 2 complete.
You now know how to **define entities as nodes**, **connect them with edges**, and **traverse them with walkers**.

---

Do you want me to **(a)** give you full worked solutions for the exercises, or **(b)** move to Module 3 (Programming Patterns) and build more complex graphs step by step?

Excellent 👌 Time for **Module 3 — Programming Patterns in Jac**.
Now that you know nodes, edges, and walkers, this module shows you how to **organize real programs**, query graphs, and apply Jac’s graph-first mindset (OSP: Object-Spatial Programming).

---

# Module 3 — Programming Patterns (Code-Along)

## 🎯 Learning Objectives

By the end of this module, you’ll be able to:

* Model data as **graphs** with nodes and edges.
* Use **walkers** to query and traverse graphs.
* Apply **Object-Spatial Programming (OSP)** principles.
* Organize Jac code into **modules**.
* Handle errors and debug walkers effectively.

---

# 1. Graph Programming in Practice

Let’s model a **mini social network**.

### Step 1.1 — Define node and edge types

Create `social.jac`:

```jac
node person {
    has name: str;
    has age: int;

    ability introduce {
        print("Hi, I'm " + name + " (" + str(age) + ")");
    }
}

edge friend {
    has since: int;
}
```

### Step 1.2 — Create people and connect them

Add a walker:

```jac
walker build_network {
    can run {
        alice = person.create(name="Alice", age=30);
        bob = person.create(name="Bob", age=25);
        carol = person.create(name="Carol", age=28);

        alice -[friend(since=2018)]-> bob;
        bob -[friend(since=2019)]-> carol;
        alice -[friend(since=2020)]-> carol;

        print("Network built!");
    }
}
```

Run:

```
jsctl> jac run social.jac -walk build_network
```

---

# 2. Traversing and Querying Graphs

### Step 2.1 — Print friends of a person

```jac
walker show_friends {
    can run(start_name: str) {
        # Find the starting person
        with entry { spawn_here(person where name == start_name); }
        
        take --> friend;
        print(here().name + " is a friend of " + start_name);
    }
}
```

Run:

```
jsctl> jac run social.jac -walk show_friends -args '{"start_name":"Alice"}'
# Expected: Bob, Carol
```

---

# 3. Object-Spatial Programming (OSP)

OSP means:

* **Nodes/edges = objects** (with attributes & abilities).
* **Walkers = agents** that *move through space* (graph).

👉 Unlike OOP (methods live in classes), in OSP, *abilities live inside nodes* and walkers can trigger them as they traverse.

### Step 3.1 — Node with multiple abilities

```jac
node person {
    has name: str;
    has age: int;

    ability introduce {
        print("Hi, I'm " + name);
    }

    ability birthday {
        age = age + 1;
        print(name + " just turned " + str(age));
    }
}
```

### Step 3.2 — Walker that calls abilities

```jac
walker demo_abilities {
    can run {
        dave = person.create(name="Dave", age=40);
        dave.introduce();
        dave.birthday();
    }
}
```

Run:

```
jsctl> jac run social.jac -walk demo_abilities
```

---

# 4. Organizing Programs

Jac lets you split code into modules.

### Step 4.1 — Create `people.jac`

```jac
node person {
    has name: str;
    has age: int;
}
```

### Step 4.2 — Create `relations.jac`

```jac
edge friend { has since: int; }
```

### Step 4.3 — Use imports in `main.jac`

```jac
use "people.jac";
use "relations.jac";

walker build {
    can run {
        alice = person.create(name="Alice", age=30);
        bob = person.create(name="Bob", age=25);
        alice -[friend(since=2020)]-> bob;
        print("Built Alice and Bob with friendship.");
    }
}
```

Run:

```
jsctl> jac run main.jac -walk build
```

This way, your code is modular and easier to scale.

---

# 5. Error Handling

Jac has `try`/`catch` blocks for safe execution.

### Example — safe division

```jac
def safe_div(a: int, b: int) -> int {
    try {
        return a / b;
    }
    catch {
        print("Error: division by zero!");
        return 0;
    }
}

walker test_div {
    can run {
        print(str(safe_div(10, 2)));  # prints 5
        print(str(safe_div(10, 0)));  # prints error + 0
    }
}
```

---

# 6. Exercises (try these)

### Exercise 1 — Friends-of-friends

* Extend `show_friends` so that after showing direct friends, it also shows *friends of friends*.

### Exercise 2 — Family tree

* Define nodes: `person`.
* Define edges: `parent_of`.
* Write a walker that, given a child’s name, prints their parents.

### Exercise 3 — Error handling

* Write a function `get_age(name: str)` that returns a person’s age.
* If person not found, handle error gracefully.

---

# 7. Debugging Tips

* If `spawn_here(...)` finds nothing → check your conditions (`where`).
* If walker prints nothing → ensure you used `with entry { ... }` to start traversal.
* If edges aren’t traversed → check direction (`-->` vs `<--`).

---

✅ That’s Module 3 complete!
You now know how to:

* **Model full graphs** (social network).
* **Traverse/query** using walkers.
* Apply **OSP** (nodes with abilities, walkers as agents).
* **Organize code** with modules.
* Handle errors safely.

---

Would you like me to next:

1. Give **worked solutions** for Module 3 exercises, or
2. Move on to **Module 4 (AI Integration)**, where we plug LLMs directly into Jac?

🔥 Great — now we’re moving into the really exciting part: **AI Integration with Jac**.
This is where Jac shines: AI is a **first-class citizen** in the language. You’ll learn to integrate **LLMs**, craft prompts, and even build memory-aware assistants.

---

# Module 4 — AI Integration (Code-Along)

## 🎯 Learning Objectives

By the end of this module, you’ll:

* Write Jac functions powered by **LLMs**.
* Pass **prompts and arguments** to LLM-backed functions.
* Build **prompt templates** in Jac.
* Use **embeddings** to add memory and context.
* Switch between providers (OpenAI, HuggingFace, etc.) easily.

---

# 1. Setup for LLMs

### Step 1.1 — Install LLM adapter

If you haven’t already:

```bash
pip install jaseci-llm
```

### Step 1.2 — Register LLM service

Inside `jsctl`:

```bash
jsctl> actions load module jaseci_ai_kit.llm
```

This makes the `by llm` decorator usable.

---

# 2. Your First LLM Function

Create `ai_demo.jac`:

```jac
by llm def simple_chat(msg: str) -> str:
    """A very simple chatbot powered by LLM"""
    return f"You said: {msg}"
```

Add a walker to call it:

```jac
walker test_chat {
    can run {
        reply = simple_chat("Hello Jac!");
        print(reply);
    }
}
```

Run:

```
jsctl> jac run ai_demo.jac -walk test_chat
```

Expected:
An LLM-generated response (may vary depending on backend).

---

# 3. Prompt Engineering in Jac

You can **design prompts inside Jac** to control LLM behavior.

### Example: polite assistant

```jac
by llm def polite_reply(user_msg: str) -> str:
    return f"""You are a polite assistant.
User said: {user_msg}
Reply in a friendly tone:"""
```

Walker:

```jac
walker demo_polite {
    can run {
        r = polite_reply("What's the time?");
        print(r);
    }
}
```

Run:

```
jsctl> jac run ai_demo.jac -walk demo_polite
```

---

# 4. Combining Graph Data with Prompts

Imagine your graph has **persons**, and you want the AI to describe them.

Extend `ai_demo.jac`:

```jac
node person {
    has name: str;
    has age: int;
}

walker describe_person {
    can run {
        alice = person.create(name="Alice", age=30);
        prompt = "Describe this person: " + alice.name + ", age " + str(alice.age);
        r = simple_chat(prompt);
        print(r);
    }
}
```

Run:

```
jsctl> jac run ai_demo.jac -walk describe_person
```

---

# 5. Embeddings and Semantic Memory

Embeddings let you **store and search context**.

### Step 5.1 — Define embedding-backed function

```jac
by llm def embed_text(text: str) -> vector:
    """Return vector embedding of text"""
```

### Step 5.2 — Store embeddings in graph

```jac
node memory {
    has content: str;
    has embedding: vector;
}

walker save_memory {
    can run(text: str) {
        m = memory.create(content=text, embedding=embed_text(text));
        print("Stored: " + m.content);
    }
}
```

### Step 5.3 — Query nearest memory

```jac
walker recall {
    can run(query: str) {
        qvec = embed_text(query);
        take --> memory;   # traverse memory nodes
        best = here();     # placeholder, later add similarity search
        print("Closest memory: " + best.content);
    }
}
```

*(Later we’ll refine this with cosine similarity searches — for now, just setting the stage.)*

---

# 6. Switching AI Providers

Jac makes swapping providers easy.
Inside `jsctl`, you can bind the LLM service to different backends (`openai`, `huggingface`, etc.).
This means your Jac code doesn’t change — only config does.

---

# 7. Exercises (try these)

### Exercise 1 — Motivational coach

* Write `by llm def coach(msg: str)` that replies with a motivational response.
* Test with different messages.

### Exercise 2 — Graph-powered assistant

* Create `person` nodes with names.
* Write a walker that picks a random name and asks the LLM to “write a short poem about [name]”.

### Exercise 3 — Memory recall

* Extend the `memory` node to hold multiple notes.
* Save three different user inputs.
* Write a walker that retrieves the most relevant memory when queried.

---

# 8. Debugging Tips

* If `actions load module jaseci_ai_kit.llm` fails → ensure `jaseci-llm` is installed.
* If LLM calls fail → check API key is set for chosen provider.
* If output looks odd → refine your prompt (Jac passes it directly).

---

✅ That’s Module 4 complete!
You can now:

* Write **LLM functions** in Jac.
* Craft **prompts** dynamically.
* Store **embeddings** in nodes.
* Begin building **memory-aware assistants**.

---

Do you want me to:

1. Give **worked solutions** for the exercises, or
2. Jump into **Module 5 (Jaseci Runtime & Cloud)** where we expose Jac walkers as **REST APIs** and scale them?

Perfect 👌 Let’s keep going — now that you can write Jac and use AI inside it, you’ll want to **run your code beyond `jsctl`**.
This is where **Jaseci Runtime & Cloud** come in.

We’ll learn how to expose Jac walkers as **APIs**, run them in a service, and even scale them in the cloud.

---

# Module 5 — Jaseci Runtime & Cloud (Code-Along)

## 🎯 Learning Objectives

By the end of this module, you’ll be able to:

* Understand the **Jaseci runtime architecture**.
* Run Jac programs through the **Jaseci runtime (`jsserv`)**.
* Expose **walkers as REST APIs**.
* Deploy and test your Jac bot via HTTP requests.
* Prepare for **cloud deployment** (Docker + scaling).

---

# 1. Jaseci Runtime Basics

Jac code doesn’t just run in `jsctl`.
You can run a **runtime server** (`jsserv`) that exposes your walkers as **REST API endpoints**.

### Step 1.1 — Install runtime package

```bash
pip install jaseci-serv
```

### Step 1.2 — Start a runtime

In your project directory:

```bash
jsserv start
```

You should see logs like:

```
[INFO] Starting Jaseci server...
[INFO] Listening on http://127.0.0.1:8000
```

---

# 2. Loading Your Jac Code

Create `chatbot.jac` (if you don’t already have one):

```jac
node user {
    has name: str;
    has memory: list;
}

by llm def ai_reply(msg: str) -> str:
    return f"""You are a helpful assistant. User said: {msg}"""

walker chat {
    can run(user_msg: str) {
        me = user.get_or_create(name="guest");
        me.memory.append(user_msg);
        response = ai_reply(user_msg);
        print("Bot:", response);
    }
}
```

---

# 3. Expose Walkers as APIs

By default, `jsserv` allows API calls.
When you load `chatbot.jac`, each walker becomes an **API endpoint**.

### Step 3.1 — Load code into runtime

Open a new terminal and run:

```bash
jsctl> jac run chatbot.jac
```

### Step 3.2 — Find available APIs

Check the docs at:

```
http://127.0.0.1:8000/docs
```

This will show a **Swagger UI** with all your walker endpoints.

---

# 4. Calling Walkers via REST

### Step 4.1 — Test with `curl`

```bash
curl -X POST http://127.0.0.1:8000/walker/chat \
    -H "Content-Type: application/json" \
    -d '{"ctx": {}, "user_msg": "Hello bot!"}'
```

Expected output:

```json
{
  "success": true,
  "report": ["Bot: Hello, how can I help you today?"]
}
```

### Step 4.2 — Test with Python

```python
import requests

url = "http://127.0.0.1:8000/walker/chat"
data = {"ctx": {}, "user_msg": "What’s the weather today?"}
r = requests.post(url, json=data)
print(r.json())
```

---

# 5. Deployment Options

### Option 1 — Local Docker

Create a `Dockerfile`:

```dockerfile
FROM python:3.10-slim

WORKDIR /app
COPY . /app

RUN pip install jaseci-serv jaseci-llm

CMD ["jsserv", "start", "--host", "0.0.0.0", "--port", "8000"]
```

Build & run:

```bash
docker build -t jac-chatbot .
docker run -p 8000:8000 jac-chatbot
```

### Option 2 — Cloud Deploy

* Push your Docker image to DockerHub or GitHub Container Registry.
* Deploy on **Heroku**, **AWS ECS**, or **Render**.
* Expose `8000` as public port.

---

# 6. Exercises (try these)

### Exercise 1 — API wrapper

Write a small **Python frontend** (CLI or Flask) that calls your Jac chatbot API.

### Exercise 2 — Multiple users

Modify the `user` node so that each user (by name) gets their own memory. Test via different `user_msg`.

### Exercise 3 — Cloud test

Deploy your Dockerized chatbot to a free platform (like Render) and test from your phone.

---

# 7. Debugging Tips

* If your walker doesn’t show in `/docs`: check if you loaded the Jac file into `jsserv`.
* If API calls fail: confirm server is running on `127.0.0.1:8000`.
* If LLM isn’t responding: ensure API keys are set (OpenAI, HuggingFace, etc.).

---

✅ You’ve completed **Module 5**!
Now your Jac bots are **exposed as real APIs**.

Up next in **Module 6**, we’ll learn about **advanced graph operations & scaling** — traversals, large-scale graphs, and optimization.

---

Would you like me to:

1. Give you a **worked example of deploying to Docker + Render**, or
2. Move straight into **Module 6 (Advanced Graph Operations)**?

Awesome 🚀 You’re now ready for **Module 6**, which dives deeper into **graphs and scaling** in Jac.
This is where Jac really sets itself apart — unlike Python or JavaScript, Jac treats **graphs as first-class citizens**, letting you model, traverse, and scale complex data structures natively.

---

# Module 6 — Advanced Graph Operations & Scaling (Code-Along)

## 🎯 Learning Objectives

By the end of this module, you’ll:

* Build and traverse **large graphs** with nodes and edges.
* Use **walkers** efficiently for big traversals.
* Implement **search algorithms** (DFS, BFS, shortest path) in Jac.
* Scale graph workloads by running **multiple walkers concurrently**.
* Optimize performance with **graph design best practices**.

---

# 1. Review: Graph Core Concepts

In Jac:

* **Nodes** = objects/entities (`person`, `document`, `server`).
* **Edges** = relationships between nodes (`friend`, `connected_to`).
* **Walkers** = “agents” that move across the graph, carrying logic.

Think of it like this:
🧩 Nodes = Data → 🔗 Edges = Relationships → 🚶 Walkers = Algorithms

---

# 2. Creating a Bigger Graph

Let’s model a **social network graph**.

Create `social_graph.jac`:

```jac
node person {
    has name: str;
}

edge friend;

walker build_network {
    can run {
        alice = person.create(name="Alice");
        bob = person.create(name="Bob");
        carol = person.create(name="Carol");
        dave = person.create(name="Dave");

        alice -> friend -> bob;
        bob -> friend -> carol;
        carol -> friend -> dave;
        alice -> friend -> dave;

        print("Graph built!");
    }
}
```

Run:

```bash
jsctl> jac run social_graph.jac -walk build_network
```

---

# 3. Traversals (Walking the Graph)

### Step 3.1 — Depth-First Search (DFS)

```jac
walker dfs {
    can run(start: person) {
        take start;
    }

    can enter {
        print("Visited: " + here().name);
    }

    can edge friend {
        take -->;
    }
}
```

Run:

```bash
jsctl> jac run social_graph.jac -walk dfs --start Alice
```

Expected: visits Alice → Bob → Carol → Dave.

---

### Step 3.2 — Breadth-First Search (BFS)

Jac has **queue-based walkers** too.

```jac
walker bfs {
    has seen: list;

    can run(start: person) {
        take start;
    }

    can enter {
        if here() not in seen {
            print("Visited: " + here().name);
            seen.append(here());
            take -->;   # traverse all neighbors
        }
    }
}
```

Run:

```bash
jsctl> jac run social_graph.jac -walk bfs --start Alice
```

Expected: Alice → Bob → Dave → Carol.

---

# 4. Pathfinding (Shortest Path)

Let’s find a path between two people.

```jac
walker shortest_path {
    has visited: dict;
    has queue: list;

    can run(start: person, goal: str) {
        visited = {};
        queue = [(start, [start.name])];

        while queue {
            (node, path) = queue.pop(0);
            if node.name == goal {
                print("Shortest path: " + str(path));
                return;
            }

            for neighbor in node.out_edges(friend).to_nodes() {
                if neighbor.name not in visited {
                    visited[neighbor.name] = true;
                    queue.append((neighbor, path + [neighbor.name]));
                }
            }
        }
        print("No path found");
    }
}
```

Run:

```bash
jsctl> jac run social_graph.jac -walk shortest_path --start Alice --goal Carol
```

Expected: `["Alice", "Bob", "Carol"]`.

---

# 5. Scaling with Multiple Walkers

Jac supports **parallel walkers**.
You can send multiple walkers into the graph at once — useful for **large graphs**.

Example:

```jac
walker multi_traverse {
    can run {
        roots = [n for n in nodes(person)];
        for r in roots {
            spawn dfs(r);
        }
    }
}
```

This will launch **multiple DFS walkers in parallel** starting from each person.

---

# 6. Graph Optimization Best Practices

* **Indexing**: Store attributes in nodes for quick lookup (e.g., `id`, `email`).
* **Edge direction**: Use `->` consistently (helps with traversals).
* **Memory control**: Limit walker state (`has seen: list`) to avoid large memory usage.
* **Batch walkers**: Use `spawn` for concurrency but don’t flood runtime.

---

# 7. Exercises (try these)

### Exercise 1 — Social Suggestions

Write a walker `suggest_friends` that suggests friends-of-friends for a given person.

### Exercise 2 — Influence Spread

Simulate a “rumor spread” in the graph: starting from Alice, spread a message through friends until everyone knows it.

### Exercise 3 — Weighted Graphs

Modify `friend` edges to have `strength: int`.
Write a walker that finds the **strongest connection path** between two people.

---

# 8. Debugging Tips

* If walker doesn’t traverse: check edge type in `can edge`.
* If path search loops infinitely: add a **visited set** to prevent cycles.
* For large graphs (>10k nodes): optimize by **breaking early** once the target is found.

---

✅ That’s Module 6 complete!
You now know how to:

* Build **complex graphs**.
* Traverse them with DFS & BFS.
* Implement **shortest path**.
* Scale traversals with **multi-walker concurrency**.

---

👉 Next, in **Module 7**, we’ll dive into **advanced AI features**: hybrid reasoning, chaining LLM + graph, and building **retrieval-augmented generation (RAG)** in Jac.

Would you like me to:

1. First give you **worked solutions for Module 6 exercises**, or
2. Jump straight into **Module 7 (Advanced AI Workflows in Jac)**?

Nice ⚡ You’ve mastered graph building and traversal — now let’s take Jac to the **next level with AI + Graph integration**.
This is where you’ll see how Jac can act as a **full Retrieval-Augmented Generation (RAG) framework**, combining **graph knowledge + LLMs** in one place.

---

# Module 7 — Advanced AI Workflows in Jac (Code-Along)

## 🎯 Learning Objectives

By the end of this module, you’ll:

* Chain **LLM + Graph reasoning** together.
* Build **RAG pipelines** inside Jac (embedding + search + LLM).
* Use walkers to orchestrate **multi-step AI flows**.
* Store and recall knowledge dynamically.
* Lay the foundation for **intelligent agents with memory**.

---

# 1. Hybrid Reasoning (Graph + LLM)

Let’s start simple: combine a **graph query** with an **LLM-generated answer**.

```jac
node person {
    has name: str;
    has age: int;
}

by llm def describe_person(name: str, age: int) -> str:
    return f"Write a fun fact about {name}, who is {age} years old."

walker demo_hybrid {
    can run {
        alice = person.create(name="Alice", age=30);
        fact = describe_person(alice.name, alice.age);
        print(fact);
    }
}
```

➡️ The LLM takes structured graph data and generates a natural-language response.

---

# 2. Building an Embedding Store (RAG Setup)

We’ll create a **memory node** that stores text + embeddings.

```jac
node memory {
    has content: str;
    has embedding: vector;
}

by llm def embed_text(text: str) -> vector:
    """Get embedding for a given text"""
```

---

# 3. Saving Knowledge to Graph

```jac
walker save_knowledge {
    can run(info: str) {
        m = memory.create(content=info, embedding=embed_text(info));
        print("Stored: " + m.content);
    }
}
```

Run:

```bash
jsctl> jac run ai_rag.jac -walk save_knowledge --info "Python is great for data science"
```

---

# 4. Retrieving Relevant Knowledge

We’ll use embeddings to **find the closest memory**.

```jac
walker retrieve_knowledge {
    can run(query: str) {
        qvec = embed_text(query);
        best = null;
        best_score = -1;

        take --> memory;
        for m in nodes(memory) {
            # cosine similarity (simplified)
            score = dot(m.embedding, qvec) / (|m.embedding| * |qvec|);
            if score > best_score {
                best_score = score;
                best = m;
            }
        }

        if best != null {
            print("Best match: " + best.content);
        } else {
            print("No relevant memory found.");
        }
    }
}
```

---

# 5. Retrieval-Augmented Generation (RAG)

Now let’s combine **retrieval + LLM answering**.

```jac
by llm def answer_with_context(query: str, context: str) -> str:
    return f"""
You are a helpful assistant. Use the following context to answer:
{context}

Question: {query}
Answer:
"""
```

Walker:

```jac
walker rag_chat {
    can run(query: str) {
        # Step 1: retrieve best memory
        qvec = embed_text(query);
        best = null;
        best_score = -1;
        for m in nodes(memory) {
            score = dot(m.embedding, qvec) / (|m.embedding| * |qvec|);
            if score > best_score {
                best_score = score;
                best = m;
            }
        }

        # Step 2: send query + memory to LLM
        if best != null {
            response = answer_with_context(query, best.content);
            print("Bot:", response);
        } else {
            print("Bot: Sorry, I don’t know yet.");
        }
    }
}
```

Run:

```bash
jsctl> jac run ai_rag.jac -walk rag_chat --query "Which language is good for data science?"
```

Expected: Bot uses stored knowledge (“Python is great for data science”).

---

# 6. Multi-Step Reasoning with Walkers

You can chain multiple walkers for complex workflows.

```jac
walker research_and_answer {
    can run(query: str) {
        spawn save_knowledge("Jac is a graph-based AI language.");
        spawn rag_chat(query);
    }
}
```

---

# 7. Exercises (try these)

### Exercise 1 — FAQ Bot

* Store 3 facts (e.g., “Kenya is in East Africa”, “Nairobi is Kenya’s capital”).
* Ask the bot “What’s the capital of Kenya?” → it should recall correctly.

### Exercise 2 — Study Notes Assistant

* Save course notes into memory nodes.
* Ask questions → bot should retrieve relevant note and answer.

### Exercise 3 — Multi-hop Reasoning

* Create `company` nodes (with name + industry).
* Save them as memories.
* Write a walker that finds a company and asks the LLM:
  *“Explain how this industry impacts the economy.”*

---

# 8. Debugging Tips

* If retrieval is random: normalize vectors before similarity.
* If answers sound off: improve `answer_with_context` prompt.
* If memory grows large: periodically prune old nodes.

---

✅ That’s Module 7 complete!
Now you can:

* Build **RAG pipelines inside Jac**.
* Mix **graph data + AI reasoning**.
* Create **memory-aware agents**.

---

👉 Next, in **Module 8**, we’ll tackle **real-world applications**: building a **customer support bot**, an **AI tutor**, and an **analytics assistant** in Jac.

Do you want me to:

1. Give you **worked solutions for Module 7 exercises**, or
2. Jump directly into **Module 8 (Applied AI Projects in Jac)**?

Perfect 🌟 You’re now ready for **Module 8**, where we take everything you’ve learned — nodes, edges, walkers, LLMs, RAG — and build **real-world projects** in Jac.
This is where theory becomes **practical applications** 🚀.

---

# Module 8 — Applied AI Projects in Jac (Code-Along)

## 🎯 Learning Objectives

By the end of this module, you’ll be able to:

* Build **domain-specific AI assistants** using Jac.
* Apply **graph + AI workflows** to real-world contexts.
* Expose projects as **APIs** for integration into apps.
* Understand how to design **scalable Jac-based solutions**.

We’ll walk through **3 mini-projects**.

---

# 1. Project 1 — Customer Support Bot

### Goal

A bot that stores FAQs, retrieves relevant ones, and answers in a polite tone.

### Step 1.1 — Setup

```jac
node faq {
    has question: str;
    has answer: str;
    has embedding: vector;
}

by llm def embed_text(text: str) -> vector:
    """Generate embedding for search"""

by llm def polite_answer(query: str, context: str) -> str:
    return f"""
You are a polite support assistant. Use this context:
{context}
Question: {query}
Answer politely:
"""
```

### Step 1.2 — Store FAQs

```jac
walker add_faq {
    can run(q: str, a: str) {
        f = faq.create(question=q, answer=a, embedding=embed_text(q));
        print("FAQ stored: " + f.question);
    }
}
```

### Step 1.3 — Query FAQ

```jac
walker support_chat {
    can run(query: str) {
        qvec = embed_text(query);
        best = null;
        best_score = -1;

        for f in nodes(faq) {
            score = dot(f.embedding, qvec) / (|f.embedding| * |qvec|);
            if score > best_score {
                best_score = score;
                best = f;
            }
        }

        if best != null {
            reply = polite_answer(query, best.answer);
            print("Bot:", reply);
        } else {
            print("Bot: I’m not sure, let me connect you to an agent.");
        }
    }
}
```

➡️ You now have a **FAQ chatbot**.

---

# 2. Project 2 — AI Tutor Assistant

### Goal

A tutoring bot that stores study notes and explains them when asked.

### Step 2.1 — Define memory node

```jac
node note {
    has subject: str;
    has content: str;
    has embedding: vector;
}

by llm def explain_with_context(query: str, context: str) -> str:
    return f"""
You are a tutor. Explain clearly using this note:
{context}
Student question: {query}
Answer:
"""
```

### Step 2.2 — Save notes

```jac
walker save_note {
    can run(subject: str, content: str) {
        n = note.create(subject=subject, content=content, embedding=embed_text(content));
        print("Saved note on " + n.subject);
    }
}
```

### Step 2.3 — Tutor Q&A

```jac
walker tutor_chat {
    can run(query: str) {
        qvec = embed_text(query);
        best = null;
        best_score = -1;

        for n in nodes(note) {
            score = dot(n.embedding, qvec) / (|n.embedding| * |qvec|);
            if score > best_score {
                best_score = score;
                best = n;
            }
        }

        if best != null {
            reply = explain_with_context(query, best.content);
            print("Tutor:", reply);
        } else {
            print("Tutor: I don’t have notes on that yet.");
        }
    }
}
```

➡️ Now you have an **AI tutor** that recalls stored notes.

---

# 3. Project 3 — Analytics Assistant

### Goal

An AI assistant that summarizes reports or datasets.

### Step 3.1 — Store reports

```jac
node report {
    has title: str;
    has content: str;
}
```

### Step 3.2 — Summarizer function

```jac
by llm def summarize_report(title: str, content: str) -> str:
    return f"""
Summarize this report titled '{title}':
{content}
Provide a clear and concise summary:
"""
```

### Step 3.3 — Walker

```jac
walker summarize_all {
    can run {
        for r in nodes(report) {
            summary = summarize_report(r.title, r.content);
            print("Summary of " + r.title + ": " + summary);
        }
    }
}
```

➡️ Now you have a **report summarizer assistant**.

---

# 4. Exposing Projects as APIs

Once you run these Jac files inside **`jsserv`**, each walker becomes a **REST endpoint** automatically.
You can call:

```bash
curl -X POST http://127.0.0.1:8000/walker/support_chat \
     -H "Content-Type: application/json" \
     -d '{"query": "What is your refund policy?"}'
```

---

# 5. Exercises (for practice)

### Exercise 1 — Hybrid Bot

Combine the **FAQ bot + Tutor bot**:

* If question matches FAQ, answer politely.
* If not, check study notes.

### Exercise 2 — Context-aware Tutor

Enhance tutor bot with a **memory of past questions**, so it adapts its answers.

### Exercise 3 — Analytics Chat

Turn the analytics bot into a **chat interface** where a user can ask:
*“Summarize report X in 3 bullet points”* and get exactly that.

---

# 6. Debugging Tips

* If walker doesn’t respond: confirm it’s loaded in runtime.
* If retrieval seems wrong: store more diverse embeddings.
* If LLM output is too verbose: adjust prompt (“Answer in 2 sentences”).

---

✅ That’s Module 8 complete!
You can now:

* Build **customer support bots**.
* Create **AI tutors**.
* Make **analytics assistants**.
* Expose them as **real APIs**.

---

👉 Next in **Module 9**, we’ll focus on **testing, debugging, and performance tuning** in Jac (profiling walkers, managing memory, scaling AI workloads).

Would you like me to:

1. First give you **worked solutions to Module 8 exercises**, or
2. Jump straight into **Module 9 (Testing & Performance in Jac)**?

Great 👍 You now have practical AI assistants running in Jac.
Next up: **Module 9**, where we focus on **testing, debugging, and performance tuning**.
This is essential when your Jac projects grow larger or move into production.

---

# Module 9 — Testing, Debugging, and Performance in Jac (Code-Along)

## 🎯 Learning Objectives

By the end of this module, you’ll:

* Write **tests for Jac walkers**.
* Debug walkers step by step using **prints & tracing**.
* Profile performance for **large graphs & AI calls**.
* Apply optimization techniques (caching, pruning, batching).
* Ensure your Jac bots run **reliably at scale**.

---

# 1. Testing Walkers

Jac doesn’t have a separate `pytest` framework — instead, you can write **test walkers** that call other walkers.

### Example: Testing an FAQ bot

```jac
walker test_support_chat {
    can run {
        resp = support_chat("What is your refund policy?");
        assert resp != null;
        print("✅ Test passed: support_chat works");
    }
}
```

Run:

```bash
jsctl> jac run chatbot.jac -walk test_support_chat
```

➡️ If the assertion fails, the test walker will throw an error.

---

# 2. Debugging Walkers

### Step 2.1 — Use `print()` generously

```jac
walker debug_example {
    can run {
        print("Walker started");
        alice = person.create(name="Alice");
        print("Created node:", alice);
    }
}
```

### Step 2.2 — Trace walker execution

You can log entry/exit points:

```jac
walker trace_friendship {
    can run(start: person) {
        take start;
    }

    can enter {
        print("Entering node:", here().name);
    }

    can edge friend {
        print("Crossing edge from", here().name, "to", edge.to_node().name);
        take -->;
    }
}
```

---

# 3. Performance Profiling

### Step 3.1 — Measure execution time

```jac
walker timed_traversal {
    has start_time: int;
    has end_time: int;

    can run(start: person) {
        start_time = now();
        take start;
        end_time = now();
        print("Execution time:", str(end_time - start_time), "ms");
    }

    can edge friend {
        take -->;
    }
}
```

### Step 3.2 — Large graph test

Try creating **10,000 nodes**:

```jac
walker build_big_graph {
    can run {
        for i in range(10000) {
            _ = person.create(name="P" + str(i));
        }
        print("Created 10,000 nodes");
    }
}
```

Run timed traversal and see how Jac scales.

---

# 4. Optimization Techniques

### Technique 1 — Limit graph size

Instead of storing every user message forever, prune:

```jac
walker save_message {
    can run(msg: str) {
        u = user.get_or_create(name="guest");
        if len(u.memory) > 50 {
            u.memory.pop(0);  # remove oldest
        }
        u.memory.append(msg);
    }
}
```

### Technique 2 — Caching AI calls

If same input repeats, reuse:

```jac
node cache {
    has query: str;
    has response: str;
}

walker cached_ai_reply {
    can run(msg: str) {
        for c in nodes(cache) {
            if c.query == msg {
                print("Cached:", c.response);
                return;
            }
        }
        r = ai_reply(msg);
        cache.create(query=msg, response=r);
        print("New:", r);
    }
}
```

### Technique 3 — Batch processing

Group multiple messages into one AI call:

```jac
by llm def batch_reply(msgs: list) -> list:
    return f"Reply to these messages: {msgs}"
```

---

# 5. Error Handling

### Step 5.1 — Try/catch equivalent

Jac doesn’t have full `try/except`, but you can guard:

```jac
walker safe_ai_call {
    can run(msg: str) {
        if msg == null or msg == "":
            print("⚠️ Empty input");
            return;
        r = ai_reply(msg);
        print(r);
    }
}
```

### Step 5.2 — Validate inputs

Always check parameters in walkers.

---

# 6. Exercises (for practice)

### Exercise 1 — Test Suite

Write 3 test walkers that confirm your tutor bot:

1. Retrieves notes correctly.
2. Responds politely.
3. Handles unknown questions gracefully.

### Exercise 2 — Performance

Build a graph with **50,000 nodes**. Measure traversal time with DFS vs BFS. Compare results.

### Exercise 3 — Optimization

Implement a **caching layer** for your RAG bot so repeated queries don’t re-call the LLM.

---

# 7. Debugging Checklist

✅ If walker doesn’t run → confirm it’s loaded with `jac run file.jac`.
✅ If graph traversal hangs → add a `visited` set.
✅ If runtime is slow → prune nodes or batch LLM calls.
✅ If API fails → check `/docs` to confirm walker is exposed.

---

✅ That’s Module 9 complete!
You now know how to:

* Write **test walkers**.
* Debug graph traversals.
* Profile performance.
* Optimize with caching, pruning, batching.

---

👉 In **Module 10 (Final Capstone)**, we’ll put everything together to build a **full-scale intelligent assistant** with:

* Graph memory
* RAG pipeline
* API interface
* Testing + performance tuning

Do you want me to:

1. Show **worked solutions for Module 9 exercises**, or
2. Jump straight into **Module 10 (Capstone Project)**?

Perfect 🚀 — time for the **Capstone Project**.
This is where you combine **everything from Modules 1–9** into a real-world **Jac-based Intelligent Assistant**.

---

# Module 10 — Capstone Project: Building a Full-Scale Jac Assistant

## 🎯 What You’ll Build

A **Personal Study Assistant** that:

* Stores and retrieves notes (graph memory).
* Uses an **LLM for explanations**.
* Performs **RAG (Retrieval-Augmented Generation)** over stored notes.
* Runs as an **API endpoint** you can query.
* Includes **basic tests + optimizations**.

---

## 1. Project Setup

### Step 1.1 — Project directory

```bash
mkdir jac_capstone
cd jac_capstone
```

### Step 1.2 — Create Jac file

```bash
touch assistant.jac
```

---

## 2. Define Nodes & Graph

We need:

* `user` → represents the learner.
* `note` → represents saved study notes.
* `cache` → optional caching layer for repeated queries.

```jac
node user {
    has name: str;
    has memory: list;
}

node note {
    has topic: str;
    has content: str;
}

node cache {
    has query: str;
    has response: str;
}
```

---

## 3. LLM Integration

Attach an AI model (from Module 7).

```jac
by llm def explain(topic: str) -> str {
    return f"Explain the concept of {topic} in simple terms."
}

by llm def rag_answer(context: str, question: str) -> str {
    return f"Use the following notes:\n{context}\n\nAnswer: {question}"
}
```

---

## 4. Walkers

### 4.1 — Save Notes

```jac
walker save_note {
    can run(topic: str, content: str) {
        n = note.create(topic=topic, content=content);
        print("✅ Note saved:", n.topic);
    }
}
```

### 4.2 — Retrieve Notes

```jac
walker get_notes {
    can run(topic: str) {
        for n in nodes(note) {
            if n.topic == topic {
                print("📘", n.topic, ":", n.content);
            }
        }
    }
}
```

### 4.3 — Ask AI (Explanation)

```jac
walker ask_ai {
    can run(question: str) {
        answer = explain(question);
        print("🤖", answer);
    }
}
```

### 4.4 — Ask with RAG

```jac
walker ask_rag {
    can run(question: str) {
        context = "";
        for n in nodes(note) {
            context += n.topic + ": " + n.content + "\n";
        }
        answer = rag_answer(context, question);
        print("🧠", answer);
    }
}
```

### 4.5 — Cached AI Call

```jac
walker cached_ai {
    can run(question: str) {
        for c in nodes(cache) {
            if c.query == question {
                print("⚡ Cached:", c.response);
                return;
            }
        }
        resp = explain(question);
        cache.create(query=question, response=resp);
        print("✨ New:", resp);
    }
}
```

---

## 5. Expose API

Add to bottom of file:

```jac
walker api_chat {
    can run(msg: str) {
        resp = explain(msg);
        return resp;
    }
}
```

Run:

```bash
jsctl> jac build assistant.jac
jsctl> jac api run
```

Visit:

```
http://localhost:8000/docs
```

➡️ You’ll see an interactive Swagger UI with your chatbot API.

---

## 6. Testing

### Test Example

```jac
walker test_notes {
    can run {
        save_note("AI", "Artificial Intelligence is about smart machines.");
        get_notes("AI");
        resp = explain("AI");
        assert resp != null;
        print("✅ AI note and explanation test passed");
    }
}
```

Run:

```bash
jsctl> jac run assistant.jac -walk test_notes
```

---

## 7. Optimization

* **Prune notes** → keep only 50 latest.
* **Cache LLM outputs** → already built.
* **Batch AI calls** → for multiple questions in one session.

---

## 8. Putting It All Together

Sample session:

```bash
jsctl> jac run assistant.jac -walk save_note -ctx '{"topic":"Python","content":"Python is a programming language."}'
jsctl> jac run assistant.jac -walk save_note -ctx '{"topic":"Jac","content":"Jac is a graph-based programming language."}'
jsctl> jac run assistant.jac -walk ask_rag -ctx '{"question":"Explain Jac compared to Python"}'
```

Expected output:

```
🧠 Use the following notes:
Python: Python is a programming language.
Jac: Jac is a graph-based programming language.

Answer: Jac is specialized for graph-based AI systems, while Python is general-purpose.
```

---

## 9. Capstone Extensions (Optional)

* Add **multi-user support** (store notes per user).
* Add **semantic search** (use embeddings).
* Connect to **external APIs** (Wikipedia, YouTube, PDF parsing).
* Deploy as a **FastAPI microservice** with Docker.

---

✅ Congratulations! You’ve built a **Jac-based Study Assistant** with:

* Graph memory
* AI reasoning
* RAG pipeline
* API exposure
* Tests & caching

---

Would you like me to now package this into a **ready-to-run GitHub project** (with `assistant.jac`, `requirements.txt`, and a small `run_chat.py` script) so you can clone & run it locally?





node, edge, has, walker, with entry, spawn, root
Great request 👍 Let’s do both:

I’ll show you a **basic “Hello World” in Jac** (so you see the syntax), and then a **Jac example that integrates an LLM**.

---

## 1. Hello World in Jac

In Jac, you usually work with **nodes**, **edges**, and **walkers**.
A walker is like an “agent” that moves around your program/graph and executes code.

```jac
# Define a walker (like a main function)
walker hello_world {
    can run {
        # Print Hello World
        print("Hello, World!");
    }
}
```

👉 To run this, you’d execute the walker `hello_world` in your Jaseci runtime (similar to calling `main()` in other languages).

---

## 2. Jac + LLM Integration

One of Jac’s cool features is the **`by llm`** syntax.
This lets you define a function (or ability) whose body is implemented by an LLM automatically.

Example:

```jac
# A Jac function implemented by an LLM
def summarize_text(text: str) -> str by llm();

# Walker that uses the LLM-powered function
walker demo_llm {
    can run {
        input_text = """
        Artificial intelligence is transforming industries, enabling automation,
        enhancing decision-making, and creating new opportunities in every field.
        """;

        summary = summarize_text(input_text);

        print("Original Text:");
        print(input_text);
        print("\nGenerated Summary:");
        print(summary);
    }
}
```

### What happens here:

* `def summarize_text(...) by llm();` → Jac delegates this function to the configured LLM backend (e.g. OpenAI, HuggingFace, local model).
* When `demo_llm` runs, it feeds the input text into the LLM, gets a summary, and prints it.

---

⚡ In short:

* **Hello World** shows Jac’s basic walker syntax.
* **LLM integration** shows how you can offload logic directly to a model with **one line of code** (`by llm`).

---

Would you like me to also show you **how to actually run this** (install Jac, set up Jaseci runtime, and call the walker), so you can try it hands-on?


# Jac Chatbot Code-Along — Build a Jac-based Chatbot with Memory

This is a hands-on code-along that walks you through building a simple Jac-based chatbot that:

* stores conversation **memory** in a graph (nodes + edges),
* retrieves recent context, and
* uses an **LLM** via `by llm` to generate replies.

The goal is practical: after this you’ll be able to run the bot locally in `jsctl` and iterate on the Jac logic and prompting.

---

## Assumptions & Prerequisites

* You have Python 3.9+ installed.
* Basic comfort with command line and creating files.
* An LLM API key (e.g., OpenAI) if you plan to actually call a commercial model.

---

## Install & Prepare

```bash
# 1. Create a project directory
mkdir jac-chatbot
cd jac-chatbot

# 2. Create & activate venv
python3 -m venv .venv
source .venv/bin/activate   # macOS/Linux
# .venv\Scripts\activate   # Windows (PowerShell/CMD variant)

# 3. Install Jaseci and LLM integration
pip install jaseci jaseci-llm

# Optional: install extra tools for convenience
pip install rich
```

Set your LLM API key in the environment. Example for OpenAI:

```bash
export OPENAI_API_KEY="sk-..."
# Windows (PowerShell): setx OPENAI_API_KEY "sk-..."
```

---

## Project structure (what we'll create)

```
jac-chatbot/
├─ chatbot.jac       # Jac program: nodes, walkers, abilities
├─ README.md         # this doc (optional copy)
└─ .env (optional)   # for storing env vars locally (not committed)
```

---

## Step 1 — Design the graph model

We'll keep the graph model simple:

* `user` node: represents a chat user (username, optional meta)
* `message` node: stores message text, role (`user` or `bot`), timestamp
* `sent` edge: connects a `user` -> `message` for messages sent by that user
* `reply` edge: connects a `message` -> `message` for bot replies (linking response to the input)

This lets us query for the last N messages for context and keep long-term memory if desired.

---

## Step 2 — The Jac program (`chatbot.jac`)

Create `chatbot.jac` and paste the following content exactly. This file includes:

* node/edge declarations
* abilities to add and query messages
* `by llm` function for generation
* a `chat_session` walker that accepts `user` and `text` and returns the bot reply

```jac
# chatbot.jac

# Node types
node user {
    has username: str;
}

node message {
    has text: str;
    has role: str;    # "user" or "bot"
    has ts: str;      # timestamp string
}

# Edge types
edge sent {
}

edge reply {
}

# Ability: create a message node and attach it to a user
ability add_message(u: node, text: str, role: str) -> node {
    new_msg = message.create(text=text, role=role, ts=str(now()));
    u.add_edge(new_msg, sent);
    return new_msg;
}

# Ability: get the last N messages (ordered by ts descending)
ability get_recent_context(u: node, n: int) -> list {
    # Collect all messages sent by user (including bot replies connected)
    msgs = [];
    for m in u.out_edges(sent) {
        msgs.append(m);
    }
    # Also include messages that were replies pointing back to messages of this user
    # (This naive approach collects all directly connected messages.)

    # Sort by timestamp (naive lexical ordering works if timestamps are ISO)
    msgs = msgs.sort(func=(a,b) -> int(a.ts > b.ts));

    # return up to n most recent as a list
    result = [];
    count = 0;
    for m in msgs {
        if (count >= n) break;
        result.append(m);
        count = count + 1;
    }
    return result;
}

# LLM: delegate reply generation to an LLM. We will pass a prompt and trust the runtime
# to use configured backend. The model should return plain text (the reply).
# The signature is important; Jac will pass the args to model backend.

def generate_reply(prompt: str) -> str by llm();

# Main walker: accept user and text; store message; build prompt from recent context; call LLM; store bot reply; print result
walker chat_session {
    can run(user_name: str, text: str) {

        # Find or create user node
        u_nodes = find(user, username == user_name).nodes();
        if (u_nodes.len() == 0) {
            u = user.create(username=user_name);
        } else {
            u = u_nodes[0];
        }

        # Add the incoming user message
        user_msg = add_message(u, text, "user");

        # Build context — get the last 6 messages (user + bot)
        recent = get_recent_context(u, 6);
        # Build a text prompt combining recent messages in chronological order
        prompt_parts = [];
        for i in recent.rev() {  # rev() so we get chronological oldest->newest
            # each m is a message node
            role = i.role;
            t = i.text;
            prompt_parts.append(role + ": " + t + "\n");
        }

        # Append the new user input at the end
        prompt_parts.append("user: " + text + "\n");

        # Add instruction for the model
        prompt_parts.append("\nAssistant: Reply briefly, be helpful and friendly.\n");

        prompt_text = join(prompt_parts, "");

        # Call the LLM
        bot_reply_text = generate_reply(prompt_text);

        # Store bot reply as message node and connect it
        bot_msg = add_message(u, bot_reply_text, "bot");

        # Connect the user message -> bot reply so we can trace pairs
        user_msg.add_edge(bot_msg, reply);

        # Print the reply to stdout so jsctl shows it
        print("Bot reply:\n" + bot_reply_text);

        # Optionally return
        return bot_reply_text;
    }
}
```

**Notes on this Jac file**:

* `now()` is used to produce a timestamp string (this is a simple approach — for production, prefer ISO timestamps).
* `get_recent_context` uses a simple sorting approach. For larger graphs you'd use indices or a different storage strategy.
* `generate_reply` uses `by llm` — the runtime will call the configured LLM backend with the prompt we build.

---

## Step 3 — Run the walker in `jsctl`

1. Start the Jaseci shell:

```bash
jsctl
```

2. Run the walker with args. `jsctl` supports the `-walk` parameter and the `-args` JSON string to pass arguments. Example:

```text
jsctl> jac run chatbot.jac -walk chat_session -args '{"user_name":"alice","text":"Hello, who are you?"}'
```

You should see the `Bot reply:` printed with the generated text.

> If your `jsctl` version does not accept `-args` as JSON, you can also create a tiny Jac driver walker that hard-codes values while testing, or run programmatically via the Python API.

---

## Alternate: Run from Python (call the walker programmatically)

If you prefer invoking from Python (gives more control and easier testing), use the Jaseci runtime API. Create `run_chat.py`:

```python
from jaseci.jsorc.jsorc import JsOrc
from jaseci.svc.kv import default_kv

# Create runtime controller
orc = JsOrc()

# Load file
orc.load("chatbot.jac")

# Run walker
res = orc.run_walker_by_name(walk_name="chat_session", args={"user_name":"alice","text":"Hi there"})
print(res)
```

Run it inside the virtualenv. (Exact Python API names vary by version; if the above fails, use `jsctl` which is stable.)

---

## Improving prompts & context

* Include a short system instruction in the prompt (e.g., persona, response length).
* Limit context length: only pass the most relevant messages (e.g., last 6–10).
* Use role labels (`user:` / `assistant:`) as in the example for better LLM behavior.

Example extended instruction appended to the prompt:

```
You are a helpful assistant that replies in 1-2 short sentences. If the user asks for code, provide a short snippet and ask if they want more detail.
```

---

## Debugging tips

* If `by llm` fails: ensure `jaseci-llm` is installed and the environment variable for the provider (e.g., `OPENAI_API_KEY`) is set.
* If `generate_reply` returns `null` or empty string: examine runtime logs for errors from the LLM adapter and inspect the prompt length.
* If timestamp sorting behaves oddly, print `ts` values to inspect their format. Use ISO time strings for robust ordering.

---

## Enhancements you can add next

* **Long-term memory node**: create `memory` nodes that store facts (e.g., user likes coffee) and surface relevant memories before calling the LLM.
* **Intent detection**: add a small walker that classifies user intent (could be `by llm` or a small Python classifier) and route flows.
* **Rate limiting & cost control**: add heuristics to avoid calling LLM for trivial responses.
* **Tool calling**: have Jac trigger external actions (calendar lookup, DB queries) and synthesize final replies.
* **UI**: connect Jaseci to a web frontend (FastAPI or Streamlit) to build a chat interface.

---

## Security & Cost considerations

* LLM calls cost money; add logging and safeguards.
* Never log API keys in code.
* Sanitize user inputs if you later forward them to other services.

---

## Final notes

This code-along gives you a minimal but functional Jac chatbot with memory. The `by llm` glue is the most powerful shorthand here — it allows you to outsource creativity and language to a model while Jac manages state and graph relationships.

If you'd like, I can:

* Convert this into a ready-to-run Git repo structure with a `Makefile` and `requirements.txt`.
* Add a Python runner that interacts with the walker in a loop to simulate a chat session.
* Extend the Jac to add long-term memory retrieval (semantic search + embeddings) example.

What would you like next?



