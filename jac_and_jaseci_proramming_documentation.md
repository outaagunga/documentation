# The Jac Programming Language and Jaseci Stack
```jac
https://www.jac-lang.org/learn/introduction/
```
<h1 style="color: orange; font-weight: bold; text-align: center;">Tour of Jac</h1>

## Python Superset Philosophy: All of Python Plus More

Jac is a drop-in replacement for Python and supersets Python, much like Typescript supersets Javascript or C++ supersets C. It extends Python's semantics while maintaining full interoperability with the Python ecosystem, introducing cutting-edge abstractions designed to minimize complexity and embrace AI-forward development.

<div class="code-block">
```jac
import math;
import from random { uniform }

def calc_distance(x1: float, y1: float, x2: float, y2: float) -> float {
return math.sqrt((x2 - x1) ** 2 + (y2 - y1) ** 2);
}

with entry { # Generate random points
(x1, y1) = (uniform(0, 10), uniform(0, 10));
(x2, y2) = (uniform(0, 10), uniform(0, 10));

    distance = calc_distance(x1, y1, x2, y2);
    area = math.pi * (distance / 2) ** 2;

    print("Distance:", round(distance, 2), ", Circle area:", round(area, 2));

}

```
</div>

This snippet natively imports Python packages `math` and `random` and runs identically to its Python counterpart. Jac targets Python bytecode, so all Python libraries work with Jac.


## Programming Abstractions for AI

Jac provides novel constructs for integrating LLMs into code. A function body can simply be replaced with a call to an LLM, removing the need for prompt engineering or extensive use of new libraries.

```jac
import from byllm { Model }
glob llm = Model(model_name="gpt-4o");

enum Personality {
    INTROVERT,
    EXTROVERT,
    AMBIVERT
}

def get_personality(name: str) -> Personality by llm();

with entry {
    name = "Albert Einstein";
    result = get_personality(name);
    print(f"{result} detected for {name}");
}
```

??? info "How To Run"
    1. Install the byLLM plugin by `pip install byllm`
    2. Get a free Gemini API key: Visit [Google AI Studio](https://aistudio.google.com/app/apikey)
    3. Save your Gemini API as an environment variable (`export GEMINI_API_KEY="xxxxxxxx"`).
    > **Note:** > > You can use OpenAI, Anthropic or other API services as well as host your own LLM using Ollama or Huggingface.
    4. Copy this code into `example.jac` file and run with `jac run example.jac`

??? example "Output"
    `   Introvert personality detected for Albert Einstein
    `

`by llm()` delegates execution to an LLM without any extra library code.


## Beyond OOP: An Agentic Programming Model

In addtion to traditional python classes (`class` or Jac's dataclass-like `obj`), Jac programmers can also use node classes (`node`), edge classes (`edge`), and walker classes (`walker`) for a new type of problem solving and agentic programming.

Instances of node and edge classes allow for assembling objects in a graph structure to express semantic relationships between objects. This goes beyond only modeling objects in memory as a disconnected soup of instances.

Walker classes inverts the traditional relationship between data and computation. Rather than moving data to computation with parameter passing, walkers enable moving computation to data as they represent computational units that moves through the topology of node and edge objects.

These new constructs gives rise to a new paradigm for problem solving and implementation we call Object-Spatial Programming (OSP).

In this example, nodes represent meaningful entities (like Weights, Cardio Machines), while walkers (agents) traverse these nodes, collect contextual information, and collaborate with an LLM to generate a personalized workout plan.

```jac
import from byllm.llm {Model}

glob llm = Model(model_name="gemini/gemini-2.5-flash");

node Equipment {}

node Weights(Equipment) {
    has available: bool = False;

    can check with FitnessAgent entry {
        visitor.gear["weights"] = self.available;
    }
}

node Cardio(Equipment) {
    has machine: str = "treadmill";

    can check with FitnessAgent entry {
        visitor.gear["cardio"] = self.machine;
    }
}

node Trainer {
    can plan with FitnessAgent entry {
        visitor.gear["workout"] = visitor.create_workout(visitor.gear);
    }
}

walker FitnessAgent {
    has gear: dict = {};

    can start with `root entry {
        visit [-->(`?Equipment)];
    }

    """Create a personalized workout plan based on available equipment and space."""
    def create_workout(gear: dict) -> str by llm();
}

walker CoachWalker(FitnessAgent) {
    can get_plan with `root entry {
        visit [-->(`?Trainer)];
    }
}

with entry {
    root ++> Weights();
    root ++> Cardio();
    root ++> Trainer();

    agent = CoachWalker() spawn root;
    print("Your Workout Plan:");
    print(agent.gear['workout']);
}
```

??? info "How To Run"
    1. Install the byLLM plugin by `pip install byllm`
    2. Save your OpenAI API as an environment variable (`export OPENAI_API_KEY="xxxxxxxx"`).
    > **Note:** > > You can use Gemini, Anthropic or other API services as well as host your own LLM using Ollama or Huggingface.
    4. Copy this code into `example.jac` file and run with `jac run example.jac`

??? example "Output"
    `   Your Workout Plan:
        **Personalized Workout Plan**

        **Duration:** 4 weeks
        **Frequency:** 5 days a week

        **Week 1-2: Building Strength and Endurance**

        **Day 1: Upper Body Strength**
        - Warm-up: 5 minutes treadmill walk
        - Dumbbell Bench Press: 3 sets of 10-12 reps
        - Dumbbell Rows: 3 sets of 10-12 reps
        - Shoulder Press: 3 sets of 10-12 reps
        - Bicep Curls: 3 sets of 12-15 reps
        - Tricep Extensions: 3 sets of 12-15 reps
        - Cool down: Stretching

        **Day 2: Cardio and Core**
        - Warm-up: 5 minutes treadmill walk
        - Treadmill Intervals: 20 minutes (1 min sprint, 2 min walk)
        - Plank: 3 sets of 30-45 seconds
        - Russian Twists: 3 sets of 15-20 reps
        - Bicycle Crunches: 3 sets of 15-20 reps
        - Cool down: Stretching

        **Day 3: Lower Body Strength**
        - Warm-up: 5 minutes treadmill walk
        - Squats: 3 sets of 10-12 reps
        - Lunges: 3 sets of 10-12 reps per leg
        - Deadlifts (dumbbells): 3 sets of 10-12 reps
        - Calf Raises: 3 sets of 15-20 reps
        - Glute Bridges: 3 sets of 12-15 reps
        - Cool down: Stretching

        **Day 4: Active Recovery**
        - 30-45 minutes light treadmill walk or yoga/stretching

        **Day 5: Full Body Strength**
        - Warm-up: 5 minutes treadmill walk
        - Circuit (repeat 3 times):
        - Push-ups: 10-15 reps
        - Dumbbell Squats: 10-12 reps
        - Bent-over Dumbbell Rows: 10-12 reps
        - Mountain Climbers: 30 seconds
        - Treadmill: 15 minutes steady pace
        - Cool down: Stretching

        **Week 3-4: Increasing Intensity**

        **Day 1: Upper Body Strength with Increased Weight**
        - Follow the same structure as weeks 1-2 but increase weights by 5-10%.

        **Day 2: Longer Cardio Session**
        - Warm-up: 5 minutes treadmill walk
        - Treadmill: 30 minutes at a steady pace
        - Core Exercises: Same as weeks 1-2, but add an additional set.

        **Day 3: Lower Body Strength with Increased Weight**
        - Increase weights for all exercises by 5-10%.
        - Add an extra set for each exercise.

        **Day 4: Active Recovery**
        - 30-60 minutes light treadmill walk or yoga/stretching

        **Day 5: Full Body Strength Circuit with Cardio Intervals**
        - Circuit (repeat 4 times):
        - Push-ups: 15 reps
        - Dumbbell Squats: 12-15 reps
        - Jumping Jacks: 30 seconds
        - Dumbbell Shoulder Press: 10-12 reps
        - Treadmill: 1 minute sprint after each circuit
        - Cool down: Stretching

        Ensure to hydrate and listen to your body throughout the program. Adjust weights and reps as needed based on your fitness level.
    `

This MTP example demonstrates how Jac seamlessly integrates LLMs with structured node-walker logic, enabling intelligent, context-aware agents with just a few lines of code.

## Zero to Infinite Scale without any Code Changes

Jac's cloud-native abstractions make persistence and user concepts part of the language so that simple programs can run unchanged locally or in the cloud. Much like every object instance has a self referencial `this` or `self` reference. Every instance of a Jac program invocation has a `root` node reference that is unique to every user and for which any ohter node or edge objeccts connected to `root` will persist across code invocations. Thats it. Using `root` to access presistant user state and data, Jac deployments can be scaled from local enviornments infinitely into to the cloud with no code changes..

```jac
node Post {
    has content: str;
    has author: str;
}

walker create_post {
    has content: str, author: str;

    can func_name with `root entry {
        new_post = Post(content=self.content, author=self.author);
        here ++> new_post;
        report {"id": new_post.id, "status": "posted"};
    }
}
```

??? info "How To Run"
    1. Install the Jac Cloud by `pip install jac-cloud`
    2. Copy this code into `example.jac` file and run with `jac serve example.jac`

??? example "Output"
    `   INFO:     Started server process [26286]
        INFO:     Waiting for application startup.
        INFO - DATABASE_HOST is not available! Using LocalDB...
        INFO - Scheduler started
        INFO:     Application startup complete.
        INFO:     Uvicorn running on http://0.0.0.0:8000 (Press CTRL+C to quit)
    `

![Fast API Server](../assets/jac_cloud_example.jpg)


## Better Organized and Well Typed Codebases

Jac focuses on type safety and readability. Type hints are required and the built-in typing system eliminates boilerplate imports. Code structure can be split across multiple files, allowing definitions and implementations to be organized separately while still being checked by Jac's native type system.

=== "tweet.jac"

    ```jac
    obj Tweet {
        has content: str, author: str, timestamp: str, likes: int = 0;

        def like() -> None;
        def unlike() -> None;
        def get_preview(max_length: int) -> str;
        def get_like_count() -> int;
    }
    ```

=== "tweet.impl.jac"

    ```jac
    impl Tweet.like() -> None {
        self.likes += 1;
    }

    impl Tweet.unlike() -> None {
        if self.likes > 0 {
            self.likes -= 1;
        }
    }

    impl Tweet.get_preview(max_length: int) -> str {
        return self.content[:max_length] + "..." if len(self.content) > max_length else self.content;
    }

    impl Tweet.get_like_count() -> int {
        return self.likes;
    }
    ```

    This shows how declarations and implementations can live in separate files for maintainable, typed codebases.

## Next Steps

<div class="grid cards" markdown>

-   __In The Works__

    ---

    *Roadmap Items*

    [In The Roadmap](bigfeatures.md){ .md-button .md-button--primary }

-   __In The Future__

    ---

    *Research in Jac/Jaseci*

    [In Research](research.md){ .md-button }

</div>

# Getting Started with Jac: Installation, Setup, and Your First Program
---
The first step is setting up your development environment. We'll cover how to install Jac, configure your code editor, and write and run your first program.


> To get started, you will need Python 3.12 or newer. While you can use any text editor, we recommend using VS Code with the official Jac extension for the best experience, as it provides helpful features like syntax highlighting and error checking.

## Installation and IDE Setup
---
### System Requirements
- Python 3.12 or higher
- pip package manager
- 4GB RAM minimum (8GB recommended)
- 500MB storage for Jac and dependencies


### Installing Jac
We recommend installing Jac in a virtual environment to keep your project's dependencies separate from your system's Python packages.

#### Quick Install via pip

```bash
# Install Jac from PyPI
$ pip install jaclang

# Verify installation
$ jac --version
```
<br />

#### Via Virtual Environment (Recommended)

For project isolation, consider using a virtual environment:

**Linux/MacOS**

```bash
# Create virtual environment
$ python -m venv jac-env

# Activate it (Linux/Mac)
$ source jac-env/bin/activate

# Install Jac
$ pip install jaclang
```
<br />

**Windows**
```powershell
# Create virtual environment
python -m venv jac-env

# Activate it (Windows)
jac-env\Scripts\activate

# Install Jac
pip install jaclang
```
<br />

### VS Code Extension
For the best development experience, install the Jac VS Code extension:

**For VS Code users:**


1. Open VS Code
2. Go to Extensions (Ctrl+Shift+X)
3. Search for "Jac"
4. Install the official Jac extension

**For Cursor users:**

1. Go to the [latest Jaseci release page](https://github.com/Jaseci-Labs/jaseci/releases/latest)
2. Download the latest `jaclang-extension-*.vsix` file from the release assets
3. Open Cursor
4. Press `Ctrl+Shift+P` (or `Cmd+Shift+P` on Mac)
5. Type `>install from vsix` and select the command
6. Select the downloaded VSIX file
7. The extension will be installed and ready to use

Alternatively, visit the [VS Code marketplace](https://marketplace.visualstudio.com/items?itemName=jaseci-labs.jaclang-extension) directly.

The extension provides:

- Color-coding for Jac's syntax to make it easier to read.
- Automatic detection of errors in your code.
- Tools for formatting your code consistently.
- Visualizations of your graph data structures.

### Basic CLI Commands
Jac provides a simple command-line interface (CLI) for running scripts and managing projects. This cli provides developers the ability to either run scripts locally for testing or [even serve them as web applications](../chapter_12). Here are the most common commands:
```bash
# Run a Jac file
$ jac run filename.jac

# Get help
$ jac --help

# Serve as web application (advanced)
$ jac serve filename.jac
```
<br />

## Hello World in Jac
---
Let's write and run your first Jac program.
1. Create a new file named hello.jac.
2. Add the following code to the file:

```jac
# hello.jac
with entry {
    print("Hello, Jac World!");
}
```
<br />

Run the program from your terminal.
```bash
$ jac run hello.jac
```
<br />

You will see the following output:

<br />

```bash
Hello, Jac World!
```
<br />

If you see this information, you have installed Jac successfully! You're ready to write your first program.

## Entry Blocks and Basic Execution
---
The `with entry` block is Jac's equivalent to Python's `if __name__ == "__main__":` - it defines where program execution begins. Any code inside this block is executed when you run the file.

### Single Entry Blocks
```jac
# Entry block - program starts here
with entry {
    print("Hello single entry block!");
}
```
<br />

### Multiple Entry Blocks

Jac allows multiple entry blocks that execute in order:

```jac
# First entry block
with entry {
    print("Hello first entry block!");
}

# Second entry block
with entry {
    print("Hello second entry block!");
}

# Third entry block
with entry {
    print("Hello third entry block!");
}
```
<br />

You have now successfully set up your development environment, written your first Jac program, and learned how the with entry block works.

From here, you can continue your journey in a way that best suits your learning style. You can go through the Learn Jac Language section for in-depth knowledge or learn using our hands-on Examples.

## Next Steps

<div class="grid cards" markdown>

-   __Jac in a Flash__

    ---

    *See Jac's Syntax with a Toy*

    [Jac in a Flash](jac_in_a_flash.md){ .md-button .md-button--primary }

-   __A Real World Example__

    ---

    *See somthing real in Jac*

    <!-- [:octicons-arrow-right-24: Getting started](#) -->

    [A Robust Example](examples/littleX/tutorial.md){ .md-button }

-   __Experience Jac in Browser__

    ---

    *Hit the Playground*

    [Jac Playground](../playground/index.html){ .md-button .md-button--primary }

</div>
# <span style="color: orange; font-weight: bold">Jac in a Flash</span>

This mini tutorial uses a single toy program to highlight the major pieces of
the Jac language.  We start with a small Python game and gradually evolve it
into a fully object‑spatial Jac implementation.  Each iteration introduces a new
Jac feature while keeping the overall behaviour identical.

## Step&nbsp;0 – The Python version

Our starting point is a regular Python program that implements a simple "guess
the number" game.  The player has several attempts to guess a randomly generated
number.

=== "guess_game.py"
    ```jac linenums="1"
    --8<-- "jac/examples/guess_game/guess_game.py"
    ```

## Step&nbsp;1 – A direct Jac translation

`guess_game1.jac` mirrors the Python code almost line for line.  Classes are
declared with `obj` and methods with `def`.  Statements end with a semicolon and
the parent initializer is invoked via `super.init`.  Program execution happens
inside a `with entry { ... }` block, which replaces Python's
`if __name__ == "__main__":` section.  This step shows how familiar Python
concepts map directly to Jac syntax.

=== "guess_game1.jac"
    ```jac linenums="1"
    --8<-- "jac/examples/guess_game/guess_game1.jac"
    ```
=== "Try it"
    <div class="code-block">
    ```jac
    --8<-- "jac/examples/guess_game/guess_game1.jac"
    ```
    </div>

## Step&nbsp;2 – Declaring fields with `has`

The second version moves attribute definitions into the class body using the
`has` keyword.  Fields may specify types and default values directly on the
declaration.  Methods that take no parameters omit parentheses in their
signature, making the object definition concise.

=== "guess_game2.jac"
    ```jac linenums="1"
    --8<-- "jac/examples/guess_game/guess_game2.jac"
    ```
=== "Try it"
    <div class="code-block">
    ```jac
    --8<-- "jac/examples/guess_game/guess_game2.jac"
    ```
    </div>

## Step&nbsp;3 – Separating implementation with `impl`

The fourth version splits object declarations from their implementations using
`impl`.  The object lists method signatures (`def init;`, `override def play;`),
and the actual bodies are provided later in `impl Class.method` blocks.  This
separation keeps the interface clean and helps organise larger codebases.

=== "guess_game3.jac"
    ```jac linenums="1"
    --8<-- "jac/examples/guess_game/guess_game3.jac"
    ```
=== "guess_game3.impl.jac"
    ```jac linenums="1"
    --8<-- "jac/examples/guess_game/guess_game3.impl.jac"
    ```
=== "Try it"
    <div class="code-block">
    ```jac
    --8<-- "jac/examples/guess_game/guess_game3.jac"
    --8<-- "jac/examples/guess_game/guess_game3.impl.jac"
    ```
    </div>

## Step&nbsp;4 – Walking the graph

Finally `guess_game4.jac` re‑imagines the game using Jac's object‑spatial
architecture.  A `walker` visits a chain of `turn` nodes created with `++>`
edges.  The walker moves with `visit [-->]` and stops via `disengage` when the
guess is correct.  The game is launched by `spawn`ing the walker at `root`.
This example shows how conventional logic can become graph traversal.

=== "guess_game4.jac"
    ```jac linenums="1"
    --8<-- "jac/examples/guess_game/guess_game4.jac"
    ```
=== "guess_game4.impl.jac"
    ```jac linenums="1"
    --8<-- "jac/examples/guess_game/guess_game4.impl.jac"
    ```
=== "Try it"
    <div class="code-block">
    ```jac
    --8<-- "jac/examples/guess_game/guess_game4.jac"
    --8<-- "jac/examples/guess_game/guess_game4.impl.jac"
    ```
    </div>

## Step&nbsp;5 – Scale Agnostic Approach

The fifth version demonstrates Jac's scale-agnostic design. The same code that runs locally can seamlessly scale to cloud deployment without modification. By running the command `jac serve filename.jac`, the walkers become API endpoints that can be called via HTTP requests. This shows how Jac applications are inherently cloud-ready.

=== "guess_game5.jac"
    ```jac linenums="1"
    --8<-- "jac/examples/guess_game/guess_game5.jac"
    ```
=== "guess_game5.impl.jac"
    ```jac linenums="1"
    --8<-- "jac/examples/guess_game/guess_game5.impl.jac"
    ```
=== "Try it"
    <div class="code-block">
    ```jac
    --8<-- "jac/examples/guess_game/guess_game5.jac"
    --8<-- "jac/examples/guess_game/guess_game5.impl.jac"
    ```
    </div>

## Step&nbsp;6 – AI-Enhanced Gameplay with byLLM

The final version integrates AI capabilities using byLLM (Meaning Typed LLM). Instead of simple "too high" or "too low" responses, the game now provides intelligent, context-aware hints generated by an LLM. This demonstrates how easily AI can be woven into Jac applications to create more engaging user experiences.

=== "guess_game6.jac"
    ```jac linenums="1"
    --8<-- "jac/examples/guess_game/guess_game6.jac"
    ```
=== "guess_game6.impl.jac"
    ```jac linenums="1"
    --8<-- "jac/examples/guess_game/guess_game6.impl.jac"
    ```

Happy code deconstructing!

## **Jac's Native Superset of Python**

At its core, Jac is a natural evolution of Python—not a replacement, but an enhancement. For Python developers, Jac offers a familiar foundation with powerful new features for modern software architecture, all while integrating seamlessly with the existing Python ecosystem.

### **How it Works: Transpilation to Native Python**

Unlike languages that require their own runtime environments, virtual machines, or interpreters, Jac programs execute on the standard Python runtime. Instead, the Jac compiler **transpiles** your Jac code into pure, efficient Python code. This means:

*   **100% Python Runtime:** Your Jac programs execute on the standard Python runtime, giving you access to Python's mature garbage collector, memory management, and threading model.
*   **Full Ecosystem Access:** Every package on PyPI, every internal library, and every Python tool you already use works out-of-the-box with Jac.

Essentially, Jac is to Python what TypeScript is to JavaScript: a powerful superset that compiles down to the language you know and love.

**Example: From Jac to Python**

A simple Jac module with archetypes, functions, and an entrypoint...

```jac
1 def foo() -> str {
2     return "Hello";
3 }
4
5 obj vehicle {
6     has name: str = "Car";
7 }
8
9 enum Size {
10    Small=1, Medium=2, Large=3
11 }
12
13 with entry {
14    car = vehicle();
15    print(foo());
16    print(car.name);
17    print(Size.Medium.value);
18 }
```

...is transpiled into familiar Python code that your Python interpreter runs natively.

```python
# Simplified Python equivalent
1 from enum import Enum
2
3 def foo() -> str:
4     return "Hello"
5
6 class vehicle:
7     def __init__(self) -> None:
8         self.name: str = "Car"
9
10 class Size(Enum):
11    Small = 1; Medium = 2; Large = 3
12
13 if __name__ == "__main__":
14    car = vehicle()
15    print(foo())
16    print(car.name)
17    print(Size.Medium.value)
```

---

### **Seamless Interoperability: Mix and Match**

Because Jac and Python share the same runtime and object model, you can mix them freely in your projects without any "glue" code.

#### **1. Using Python in Your Jac Code**

Import any Python module or library directly into your Jac files using familiar syntax. Jac automatically forwards any non-`.jac` imports to Python's native importer.

```jac
# mathstest.jac

# Import from Python's standard library
import math;

def area_of_circle(radius: float) -> float {
    return math.pi * radius * radius;
}

with entry {
    print("Area of circle with radius 2:", area_of_circle(2));
}
```

#### **2. Using Jac in Your Python Code**

Integrating Jac modules into an existing Python project is trivial. Simply import `jaclang` at the beginning of your Python entrypoint to activate the import hook.

```python
# main.py

# This one-time import enables Python to find and compile .jac files
import jaclang

# Now you can import your Jac modules just like any Python module
# Assumes you have a file named 'mathstest.jac'
import mathstest

# Use functions and archetypes defined in your Jac module
result = mathstest.area_of_circle(2)
print("Called from Python:", result)
```

---

### **Advanced Integration Patterns**

The integration goes even deeper, allowing you to choose the adoption strategy that fits your team's needs.

#### **1. Using Jac Features as a Python Library**

You can leverage Jac's powerful object-spatial programming model (nodes, edges, walkers) directly in your `.py` files without writing any Jac syntax. This is perfect for incrementally adopting Jac's architectural patterns.

```python
1 from jaclang import JacMachineInterface as _
2 from jaclang.runtimelib.archetype import NodeArchetype, WalkerArchetype
3
4 class Person(NodeArchetype):
5     name: str
6
7 class Greeter(WalkerArchetype):
8     @_.entry
9     def start(self, n: Person):
10        print(f"Hello, {n.name}!")
11
12 if __name__ == "__main__":
13    alice = Person(name="Alice")
14    walker = Greeter()
15    _.spawn(walker, alice)
```

#### **2. The Escape Hatch: Inlining Raw Python**

For situations where you need Python-specific syntax or want to gradually migrate a file, you can embed raw Python code directly inside a `.jac` module using the `::py::` directive.


```jac
1 with entry {
2     print("hello ");
3 }
4
5 ::py::
6 def foo():
7     print("world")
8
9 foo()
10 ::py::
```

Jac's relationship with Python isn't about choosing one over the other. It's about providing a powerful "and". You get the simplicity and vast ecosystem of Python and the advanced architectural constructs of Jac, all within a single, unified development experience.

This design philosophy means you can,

- Adopt Incrementally: Introduce Jac into your existing Python projects one file at a time, at your own pace.

- Leverage Everything: Continue using your favorite Python libraries, frameworks, and tools without any compatibility issues.

- Maintain Flexibility: Choose the level of integration that’s right for you—from writing full .jac modules to using Jac's features as a standard Python library.

- Eliminate Risk: With the ability to transpile Jac to readable Python, you’re never locked into a proprietary ecosystem.

Ultimately, Jac empowers you to write more structured, maintainable, and scalable code easily.


# Jac Playground Guide

Welcome to the Jac Playground! This interactive development environment lets you write, run, and debug Jac programs directly in your browser.

## Getting Started

The Jac Playground is designed to help you learn and experiment with the Jac programming language. When you first open the playground, you'll see a simple "Hello World" program:

```jac
with entry {
    print("Welcome to Jac!");
}
```

## How to Use

![Description of GIF](../assets/runtime.gif)

1. **Start with Examples**: Click on any example from the right sidebar to load it into the editor
2. **Modify and Experiment**: Edit the code to see how changes affect the output
3. **Run Your Code**: Press the "Run" button to execute your program
4. **Debug When Needed**: Enable Debug Mode for detailed execution analysis
5. **Reset When Stuck**: Use the Reset button to start with a clean slate

## Debug Mode
Debug Mode transforms the Jac Playground into a powerful development environment with advanced debugging capabilities. When enabled, the interface splits into two main sections: the code editor on the left and the **Jaclang Graph Visualizer** on the right.

### Jaclang Graph Visualizer

![Debug Mode with Graph Visualizer](../assets/visualizer.jpg)


### Jac Playground - Jaclang Graph Visualizer Demo

![Click here to watch the Debug Mode Demo Video](../assets/playground_demo.gif)

### Using Debug Mode Effectively

1. **Set breakpoints** by clicking on line numbers in the editor
2. **Start debugging** by clicking the Debug button instead of Run
3. **Navigate execution** using the arrow keys or debug controls
4. **Observe the graph** to understand how your nodes and edges are connected
5. **Step through slowly** to see how data flows through your spatial program structure

The Graph Visualizer makes Jac's spatial programming concepts tangible, allowing you to see exactly how your objects, walkers, and edges interact during program execution.


## Interface Overview

### Main Editor
The left side of the screen contains the code editor where you can write your Jac programs. The editor features:

- Syntax highlighting for Jac language
- Line numbers for easy reference
- Auto-indentation and bracket matching

### Control Panel
At the top of the editor, you'll find:

- **Run Button** ▶️ - Execute your Jac program
- **Reset Button** 🔄 - Clear the editor and start fresh
- **Debug Mode Toggle** 🐛 - Enable debugging features for step-by-step execution

### Debug Controls

When Debug Mode is active, you'll notice additional controls in the Run Mode toolbar:

- **Continue** ▶️ - Start or continue execution
- **Step Over** ⏭️ - Execute the next line of code
- **Step Into** ⬇️ - Move deeper into function calls
- **Step Out** ⬆️ - Move up from current execution context
- **Restart** 🔄 - Reset the debug session
- **Stop** ⏹️ - Terminate the current debug session

The Graph Visualizer is one of Jac's most powerful debugging features, providing a real-time visual representation of your program's execution flow. This unique tool shows:

### Output Panel
The bottom section displays the output of your program, including:

- Print statements and results
- Error messages and debugging information
- Program execution feedback

### Example Library
The right sidebar contains a collection of sample programs organized by category:

#### Basic Examples
- **For Loop** - Learn iteration with for loops
- **While Loop** - Understand conditional looping
- **Archetypes** - Explore Jac's type system
- **Code Block Statements** - Work with code organization
- **Assignments** - Variable declaration and manipulation
- **Conditional Statements** - If/else logic and branching

#### Object Spatial Programming
- **Reference** - Understanding object references and relationships

## Tips for New Users

- Start with the "Basic" examples to understand Jac syntax
- Use the print statement to output values and debug your code
- Experiment with modifying the example programs
- Don't be afraid to break things - that's how you learn!
- Use Debug Mode when your program doesn't behave as expected

## Example Categories Explained

### Basic Programming Concepts
These examples cover fundamental programming constructs that are essential for any Jac programmer.

### Object Spatial Programming
Jac's unique approach to spatial programming and object relationships. These advanced examples show how Jac handles complex data structures and spatial reasoning.

## Getting Help

If you encounter issues or want to learn more about specific Jac language features, refer to the official Jac documentation or community resources.

Happy coding with Jac! 🚀


# Tutorial
# Build Your First Social Media App with Jaseci

You'll build **LittleX**, a Twitter-like application, in just 200 lines of code. This tutorial guides you through each step, from installation to deployment.

---

## What You'll Learn

By the end of this tutorial, you'll understand how to:

- **Store data** in connected graph structures
- **Navigate** through relationships between data
- **Add AI features** to your application
- **Deploy** a working social media platform

---

## What You'll Build: LittleX

**LittleX** lets users:

- Create accounts and profiles
- Post messages (tweets)
- Follow other users
- View a personalized feed

### Complete Code Preview

Here's what you'll build - just **200 lines of code** for a full social media platform:

=== "Frontend Preview"
    ![LittleX Frontend](src/front_end.png)

=== "Single User Graph"
      ```mermaid
      graph TD
      %% Root Nodes
      Root1((Root1)):::root --> P1[Profile]:::profile

      %% Tweets
      P1 -->|Post| T1(Tweet):::tweet
      P1 -->|Post| T2(Tweet):::tweet

      %% Comments for P1's Tweet
      T1 --> C1(Comment):::comment
      C1 --> C1a(Comment):::comment
      C1 --> C1b(Comment):::comment
      ```
=== "Multiple User Graph"
      ```mermaid
      graph TD
      %% Subgraph 1: Root1
      subgraph Cluster1[ ]
            direction TB
            Root1((Root1)):::root
            Root1 --> P1[Profile]:::profile
            P1 -->|Post| T1(Tweet):::tweet
            P1 -->|Post| T2(Tweet):::tweet
            T2 --> C4(Comment):::comment
            Root1 -- Follow --> P2
            Root1 -- Like --> T3
      end

      %% Subgraph 2: Root2
      subgraph Cluster2[ ]
            direction TB
            Root2((Root2)):::root
            Root2 --> P2[Profile]:::profile
            P2 -->|Post| T3(Tweet):::tweet
            P2 -->|Post| T4(Tweet):::tweet
            T3 --> C1(Comment):::comment
            C1 --> C1a(Comment):::comment
            C1 --> C1b(Comment):::comment
            Root2 --> T7(Tweet):::tweet
            T7 --> C5(Comment):::comment
            P2 -- Follow --> P3
      end

      %% Subgraph 3: Root3
      subgraph Cluster3[ ]
            direction TB
            Root3((Root3)):::root
            Root3 --> P3[Profile]:::profile
            P3 -->|Post| T5(Tweet):::tweet
            P3 -->|Post| T6(Tweet):::tweet
            T5 --> C2(Comment):::comment
            T6 --> C3(Comment):::comment
      end
      ```

=== "LittleX.jac"
    ```jac linenums="1"
    --8<-- "docs/learn/examples/littleX/src/littleX.jac"
    ```

=== "LittleX.impl.jac"
    ```jac linenums="1"
    --8<-- "docs/learn/examples/littleX/src/littleX.impl.jac"
    ```

=== "LittleX.test.jac"
    ```jac linenums="1"
    --8<-- "docs/learn/examples/littleX/src/littleX.test.jac"
    ```

---

## Before You Start

You'll need:

- **15 minutes** to complete this tutorial
- **Python 3.12 or later** installed
- A text editor or IDE

---

## Step 1: Install Jaseci

Install the required libraries:

```bash
pip install jac_cloud
```

If the install is successful, you'll see:
```
Successfully installed jac_cloud
```

You're ready to start building!

---

## Step 2: Get the Code

Clone the repository:

```bash
git clone https://github.com/Jaseci-Labs/littleX.git
cd littleX
```

Install dependencies:

```bash
# Backend dependencies
pip install -r littleX_BE/requirements.txt

# Frontend dependencies
cd littleX_FE
npm install
cd ..
```

---

## Understanding Jaseci's Building Blocks

Jaseci uses **three main components** to build applications. Let's see how they work together:

### File Structure: Three Files, One Application

Jaseci organizes code into three files that work together automatically:

#### **littleX.jac** - What Your App Has
```jac
# Define what exists
node Profile {
    has username: str;
    can update with update_profile entry;
}
```

#### **littleX.impl.jac** - How Your App Works
```jac
# Define how things work
impl Profile.update {
    self.username = here.new_username;
    report self;
}
```

#### **littleX.test.jac** - Proving It Works
```jac
# Test functionality
test create_tweet {
    root spawn create_tweet(content = "Hello World");
    tweet = [root --> (?Profile) --> (?Tweet)][0];
    check tweet.content == "Hello World";
}
```

### Running Your Code

Jaseci automatically links these files:

```bash
# Run the application
jac run littleX.jac

# Run tests
jac test littleX.jac

# Start API server
jac serve littleX.jac
```

### 1. Nodes: Store Your Data

**Nodes** hold information. In LittleX:

- **Profile nodes** store user information
- **Tweet nodes** store message content
- **Comment nodes** store replies

**Simple Example:**
```jac
node User {
    has username: str;
}
```

This creates a user object with a username.

### 2. Edges: Connect Your Data

**Edges** create relationships between nodes. In LittleX:

- **Follow edges** connect users who follow each other
- **Post edges** connect users to their tweets
- **Like edges** connect users to tweets they liked

**Simple Example:**
```jac
edge Follow {}
```

This creates a "Follow" connection between users.

### 3. Walkers: Make Things Happen

**Walkers** move through your graph and perform actions. They make your app interactive.

**Simple Example:**
```jac
walker create_tweet(visit_profile) {
    has content: str;
    can tweet with Profile entry;
}
```

This walker creates new tweets when users post messages.

---

## Build LittleX Step by Step

Now let's build your social media app by combining these pieces:

### Step 3: Create User Profiles

When someone signs up, we create their profile:

```jac
walker visit_profile {
    can visit_profile with `root entry;
}

impl visit_profile.visit_profile {
    visit [-->(`?Profile)] else {
        new_profile = here ++> Profile();
        grant(new_profile[0], level=ConnectPerm);
        visit new_profile;
    }
}
```

**What this does:** Creates a new profile if one doesn't exist, or visits the existing profile.

### Step 4: Post Messages

Users can create and share posts:

```jac
walker create_tweet(visit_profile) {
    has content: str;
    can tweet with Profile entry;
}

impl create_tweet.tweet {
        embedding = vectorizer.fit_transform([self.content]).toarray().tolist();
        tweet_node = here +>:Post():+> Tweet(content=self.content, embedding=embedding);
        grant(tweet_node[0], level=ConnectPerm);
        report tweet_node;
}
```

**What this does:** Creates a new tweet with the user's message and connects it to their profile.

### Step 5: Follow Other Users

Build your network by following others:

```jac
walker follow_request {}

impl Profile.follow {
    current_profile = [root-->(`?Profile)];
    current_profile[0] +>:Follow():+> self;
    report self;
}
```

**What this does:** Creates a follow relationship between the current user and another user.

### Step 6: View Your Feed

See posts from people you follow:

```jac
walker load_feed(visit_profile) {
    has search_query: str = "";
    has results: list = [];
    can load with Profile entry;
}

impl load_feed.load {
    visit [-->(`?Tweet)];
    for user_node in [->:Follow:->(`?Profile)] {
        visit [user_node-->(`?Tweet)];
    }
    report self.results;
}
```

**What this does:** Collects tweets from the current user and everyone they follow.

---

## Step 7: Run Your App

Let's see your social media platform in action:

### Start the Backend

```bash
jac serve littleX_BE/littleX.jac
```

You should see:
```
INFO: Uvicorn running on http://127.0.0.1:8000
```

Your backend is running!

### Start the Frontend

Open a new terminal:

```bash
cd littleX_FE
npm run dev
```

You should see:
```
Local: http://localhost:5173/
```

Your frontend is ready!

### Try Your App

1. **Open your browser** to: [`http://localhost:5173`](http://localhost:5173)

2. **Test these features**

    - Create an account
    - Post a message
    - Follow someone
    - Check your feed

If everything works, you've successfully built a social media platform!

---

## Key Code Components

Let's examine the main parts of your LittleX app:

### Profile Node
```jac
node Profile {
    has username: str = "";

    can update with update_profile entry;
    can get with get_profile entry;
    can follow with follow_request entry;
    can un_follow with un_follow_request entry;
}
```

This stores user information and defines what users can do.

### Tweet Node
```jac
node Tweet {
    has content: str;
    has embedding: list;
    has created_at: str = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S");

    can update with update_tweet exit;
    can delete with remove_tweet exit;
    can like_tweet with like_tweet entry;
    can remove_like with remove_like entry;
    can comment with comment_tweet entry;

    def get_info() -> TweetInfo;
    can get with load_feed entry;
}
```

This stores tweet content and handles all tweet interactions.

### Follow Implementation
```jac
impl Profile.follow {
    current_profile = [root-->(`?Profile)];
    current_profile[0] +>:Follow():+> self;
    report self;
}
```

This creates the follow relationship between users.

---

## Try These Extensions

Ready to add more features? Try implementing:

1. **Like system** for posts
2. **User search** by username
3. **Comment replies** for deeper conversations
4. **Profile pages** showing user-specific content

---

## What You've Accomplished

You've built a complete social media application. You now understand:

- **Nodes** for storing data
- **Edges** for connecting information
- **Walkers** for creating functionality

Jaseci's graph-based approach works well for social networks where relationships between data are essential.

---

> **Happy coding with Jaseci!** 🚀

# Build an AI-Powered Multimodal MCP Chatbot

This step-by-step guide will walk you through building a modern chatbot that can chat with your documents, images, and videos. By the end, you'll have a working multimodal AI assistant and understand how to use Jac's unique programming features to build intelligent applications.

## What You'll Build

You'll create a chatbot that can:

- Upload and chat with PDFs, text files, images, and videos
- Search your documents and provide context-aware answers
- Answer general questions using web search
- Understand and discuss images and videos using AI vision
- Route different types of questions to specialized AI handlers


## What You'll Learn

- **Object Spatial Programming**: Use Jac's node-walker architecture to organize your application
- **Mean Typed Programming (MTP)**: Let AI classify and route user queries automatically with just simple definitions
- **Model Context Protocol (MCP)**: Build modular, reusable AI tools
- **Multimodal AI**: Work with text, images, and videos in one application

## Technologies We'll Use

- **Jac Language**: For the main application logic
- **Jac Cloud**: Backend server infrastructure
- **Streamlit**: User-friendly web interface
- **ChromaDB**: Document search and storage
- **OpenAI GPT**: AI chat and vision capabilities
- **Serper API**: Real-time web search

## Project Structure

We'll create six main files:

- `client.jac`: The web interface for chat and file uploads
- `server.jac`: The main application using Object Spatial Programming
- `server.impl.jac`: Implementation details and function bodies for `server.jac` (automatically imported by Jac)
- `mcp_server.jac`: Tool server for document search and web search
- `mcp_client.jac`: Interface to communicate with tools
- `tools.jac`: Document processing and search logic

### Complete Code Preview

Here's what you'll build - a full AI-powered multimodal MCP chatbot:

=== "Frontend Preview"
    ![Chatbot Workflow](images/image.png)

=== "client.jac"
    ```jac linenums="1"
    --8<-- "docs/learn/examples/rag_chatbot/solution/client.jac"
    ```

=== "server.jac"
    ```jac linenums="1"
    --8<-- "docs/learn/examples/rag_chatbot/solution/server.jac"
    ```

=== "server.impl.jac"
    ```jac linenums="1"
    --8<-- "docs/learn/examples/rag_chatbot/solution/server.impl.jac"
    ```

=== "mcp_server.jac"
    ```jac linenums="1"
    --8<-- "docs/learn/examples/rag_chatbot/solution/mcp_server.jac"
    ```

=== "mcp_client.jac"
    ```jac linenums="1"
    --8<-- "docs/learn/examples/rag_chatbot/solution/mcp_client.jac"
    ```

=== "tools.jac"
    ```jac linenums="1"
    --8<-- "docs/learn/examples/rag_chatbot/solution/tools.jac"
    ```

The full source code for this project is also available at: [https://github.com/jaseci-labs/Agentic-AI/tree/main/jac-mcp-chatbot](https://github.com/jaseci-labs/Agentic-AI/tree/main/jac-mcp-chatbot)

---

## Step 1: Set Up Your Environment

First, install the required packages. We recommend Python 3.12 or newer:

```bash
pip install jaclang jac-cloud jac-streamlit byllm langchain langchain-community langchain-openai langchain-chroma chromadb openai pypdf tiktoken requests mcp[cli] anyio
```

Next, get your API keys. You'll need an OpenAI API key for the AI features. For web search, get a free API key from [Serper](https://serper.dev/).

Set your environment variables:

```bash
export OPENAI_API_KEY=<your-openai-key>
export SERPER_API_KEY=<your-serper-key>
```

If you see no errors, you're ready to start building!

## Step 2: Understanding the Architecture

Your application uses Jac's Object Spatial Programming to create a clean, modular design:

**Nodes** represent different parts of your system (Router, Chat types, Sessions). Each node has specific responsibilities and capabilities.

**Walkers** move through your node network, carrying information and executing logic. They represent the actions your system can perform.

**Mean Typed Programming (MTP)** lets AI automatically classify and route requests, making your application intelligent without complex rule-based logic.

**Implementation Separation**: The `server.jac` file contains the high-level structure and logic, while `server.impl.jac` provides the detailed function implementations. Jac seamlessly imports the implementation file, allowing for clean separation of concerns.

The application consists of:

- **Document Processing Engine** (`tools.jac`): Processes and searches documents using vector embeddings
- **Tool Server** (`mcp_server.jac`): Exposes document and web search as MCP tools
- **Tool Client** (`mcp_client.jac`): Interfaces with the tool server
- **Main Application** (`server.jac` + `server.impl.jac`): Routes queries and manages conversations
- **Web Interface** (`client.jac`): User-friendly Streamlit interface

## Step 3: Run Your Application

Now let's see your creation in action! You'll need three terminal windows:

**Terminal 1 - Start the tool server:**
```bash
jac run mcp_server.jac
```

**Terminal 2 - Start the main application:**
```bash
jac serve server.jac
```

**Terminal 3 - Launch the web interface:**
```bash
jac streamlit client.jac
```

If everything starts successfully, open your browser and go to the Streamlit URL (typically `http://localhost:8501`).

## Step 4: Test Your Chatbot

1. **Register and log in** using the web interface
2. **Upload some files**: Try PDFs, text files, images, or videos
3. **Start chatting**: Ask questions about your uploaded content or general questions

The system will automatically route your questions:

- Document questions go to the RAG system
- General questions use web search
- Image questions use vision AI
- Video questions analyze video content

## What You've Accomplished

Congratulations! You've built a sophisticated AI application that demonstrates several advanced concepts:

- **Multimodal AI capabilities** that work with text, images, and videos
- **Intelligent routing** using AI-based classification
- **Modular architecture** with reusable tools via MCP
- **Clean separation of concerns** using Object Spatial Programming
- **Real-time web search integration**
- **Efficient document search** with vector embeddings

## Extending Your Chatbot

Your chatbot is designed to be extensible. You could add:

- **New file types**: Support for audio files, spreadsheets, or presentations
- **Additional tools**: Weather APIs, database connections, or custom business logic
- **Enhanced AI models**: Different LLMs for specialized tasks
- **Advanced search**: Hybrid search combining keyword and semantic search
- **Custom chat nodes**: Specialized handlers for domain-specific questions

## Troubleshooting

If you run into issues:

- **Dependencies**: Make sure all packages are installed and compatible with your Python version
- **Server startup**: Start the MCP server before the main server
- **File uploads**: Check server logs if uploads fail, and verify supported file types
- **API keys**: Verify your OpenAI and Serper API keys are set correctly
- **Ports**: Ensure all three services are running on their respective ports

## API Reference

Your application exposes these main endpoints:

- `POST /user/register` — Create a new user account
- `POST /user/login` — Login and get an access token
- `POST /walker/upload_file` — Upload files (requires authentication)
- `POST /walker/interact` — Chat with the AI (requires authentication)

Visit `http://localhost:8000/docs` to see the full API documentation.

---

You now have the foundation to build sophisticated AI applications using Jac's unique programming paradigms. The combination of Object Spatial Programming, Mean Typed Programming, and modular tool architecture gives you a solid base for creating intelligent, scalable applications.

# <span style="color: orange">Tutorial: Building an AI-Powered RPG Level Generator

In this tutorial, we’ll build a dynamic RPG level generator using LLMs and Jaclang’s `by llm` syntax. The tutorial covers creating a system that uses AI to generate balanced, progressively challenging game levels.

The system creates game levels automatically through structured data types for spatial positioning and game elements, progressive difficulty scaling that adapts to player progress, and dynamic map rendering from AI-generated data.

You’ll write code, test it, and see AI-generated levels come to life.

<!-- This tutorial demonstrates how to build a dynamic RPG game level generator using Large Language Models (LLMs) and Jaclang's `by llm` syntax. The tutorial covers creating a system that uses AI to generate balanced, progressively challenging game levels. -->

<div align="center">
  <video width="480" height="300" autoplay loop muted playsinline>
    <source src="/learn/examples/mtp_examples/assets/rpg_demo.mp4" type="video/mp4">
    Your browser does not support the video tag.
  </video>
</div>

<!-- <div align="center" style="margin-top: 20px;">
  <iframe width="560" height="315" src="https://www.youtube.com/watch?v=FSIZmwfQD1s"
          title="RPG Level Generator Tutorial"
          frameborder="0"
          allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
          allowfullscreen>
  </iframe>
</div> -->

#### 📺 **Watch the Tutorial**:

You can find the complete video walkthrough of this RPG level generator tutorial. The video covers each step in detail and shows the AI generation process in action.

<div align="center">
<iframe width="560" height="315" src="https://www.youtube.com/embed/FSIZmwfQD1s?si=rNdyOUNiipFDr6Fn" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
</div>

<!-- ## <span style="color: orange">Overview

This tutorial covers building an AI-driven RPG level generator using Jaclang's `by llm` syntax. The system creates game levels automatically through structured data types for spatial positioning and game elements, progressive difficulty scaling that adapts to player progress, and dynamic map rendering from AI-generated data. -->

## <span style="color: orange">Prerequisites

Required dependencies:

```bash
pip install byllm
```

OpenAI API key configuration:
```bash
export OPENAI_API_KEY="your-api-key-here"
```

- Basic knowledge of [Jaclang syntax](/jac_book/)
- Familiarity with game development concepts (optional)

## <span style="color: orange">Implementation Steps

The implementation consists of the following components:

1. **Define Game Data Structures** - Create the building blocks for the game world
2. **Implement AI-Powered Methods** - Use `by llm` to delegate level creation to AI
3. **Build the Level Manager** - Coordinate the generation process
4. **Test and Iterate** - Run the system and validate AI-generated levels

## <span style="color: orange">Step 1: Define Game Data Structures

**What we’re going to do:**

We’ll set up the core data structures for the game world: positions, walls, levels, and maps. These serve as a vocabulary that the AI uses to understand and generate game content.

### <span style="color: orange">Basic Position and Wall Objects

Create a new file called `level_manager.jac` and start by writing the foundational objects:

```jac linenums="1"
obj Position {
    has x: int, y: int;     # 2D coordinate

}

obj Wall {
    has start_pos: Position, end_pos: Position;       # wall starts and ends here
}
```

- `Position` (Lines 1-2) defines a point in 2D space. `Wall` (Lines 6-7) uses two positions (`start_pos` and  `end_pos`) to define a barrier.

### <span style="color: orange">Game Configuration Objects

Next, define the main game configuration:

```jac linenums="9"
obj Level {
    has name: str, difficulty: int;     # difficulty scaling
    has width: int, height: int, num_wall: int; # spatial constraints
    has num_enemies: int; time_countdown: int;  # enemies + time
    n_retries_allowed: int;     # retries allowed
}

obj Map {
    has level: Level, walls: list[Wall];    # embeds Level + walls
    has small_obstacles: list[Position];    # extra blocks
    has enemies: list[Position];    # enemy positions
    has player_pos: Position;       # player start
}
```

- `Level` (Lines 9-13) describes rules. `Map` (Lines 16-20) describes actual placement of objects.

## <span style="color: orange">Step 2: Implement AI-Powered Generation Methods

**What we’re going to do:**
We’ll connect to an LLM (GPT-4o here) and define AI-powered methods for generating new levels and maps.

At the top of `level_manager.jac`, import the model:

```jac
import from byllm { Model }

glob llm = Model(model_name="gpt-4o", verbose=True);
```

- `glob llm` sets up GPT-4o as our generator, with `verbose=True` so we can see detailed outputs.

Now define the `LevelManager` object:

```jac linenums="22"
obj LevelManager {
    has current_level: int = 0, current_difficulty: int = 1,
        prev_levels: list[Level] = [], prev_level_maps: list[Map] = [];

    def create_next_level (last_levels: list[Level], difficulty: int, level_width: int, level_height: int)
    -> Level by llm();

    def create_next_map(level: Level) -> Map by llm();
}
```

- The key parts are **Lines 26 and 29**: the `by llm` keyword makes the AI responsible for generating `Level` and `Map` objects.

- For `create_next_level()` (Line 26), we pass these arguments to retuen a complete level as the output:
    - Historical Context: last_levels ensures variety
    - Difficulty Guidance: difficulty scales challenge
    - Spatial Constraints: level_width, level_height

- For `create_next_map()` (Line 29) we return a detailed map as the output by:
    - Taking a high-level `Level`
    - Generating specific positions for walls, enemies, and player
    - Producing a balanced, playable layout

<!-- ### <span style="color: orange">AI Method Implementation

The `by llm` methods work as follows:

**Level Creation Method**
```jac
def create_next_level (last_levels: list[Level], difficulty: int, level_width: int, level_height: int)
-> Level by llm();
```

**Method parameters:**
- **Historical Context**: `last_levels` - Previous levels for ensuring variety
- **Difficulty Guidance**: `difficulty` - Scale the challenge appropriately
- **Spatial Constraints**: `level_width, level_height` - Boundary parameters
- **Expected Output**: Return a complete `Level` object

**Map Generation Method**
```jac
def create_next_map(level: Level) -> Map by llm();
```

**Method function:**

- Takes a high-level `Level` configuration
- Generates specific positions for walls, enemies, and the player
- Creates a balanced, playable layout
- Returns a detailed `Map` object -->

??? note "AI Data Structure Understanding"
    The AI automatically understands data structures. When you pass a `Level` object, the AI knows about all its properties (difficulty, dimensions, enemy count, etc.) and uses this context to make intelligent decisions.

## <span style="color: orange">Step 3: Manage Level Flow

**What we’re going to do:**
We’ll write logic to coordinate AI generation: keep track of past levels, scale difficulty, and return a new `Level` + `Map`.

Inside `LevelManager`, add:

```jac linenums="31"
def get_next_level -> tuple(Level, Map) {
    self.current_level += 1;

    # Keeping Only the Last 3 Levels
    if len(self.prev_levels) > 3 {
        self.prev_levels.pop(0);
        self.prev_level_maps.pop(0);
    }

    # Generating the New Level
    new_level = self.create_next_level(
        self.prev_levels,
        self.current_difficulty,
        20, 20
    );

    self.prev_levels.append(new_level);

    # Generating the Map of the New Level
    new_level_map = self.create_next_map(new_level);
    self.prev_level_maps.append(new_level_map);

    # Increasing the Difficulty for end of every 2 Levels
    if self.current_level % 2 == 0 {
        self.current_difficulty += 1;
    }

    return (new_level, new_level_map);
}
```

This method executes the following sequence:

1. **Level Counter**: Increments the level number
2. **Memory Management**: Keeps only the last 3 levels
3. **AI Level Generation**: Calls the AI to create a new level
4. **AI Map Generation**: Requests the AI to generate map
5. **Difficulty Progression**: Increases difficulty every 2 levels
6. **Return Results**: Returns both the level config and detailed map

Now let’s add a visualization helper that converts maps into tile grids. Create a function that converts the AI-generated `Map` into game tiles:

```jac linenums="60"
def get_map(map: Map) -> str {
    map_tiles = [['.' for _ in range(map.level.width)] for _ in range(map.level.height)];

    # Place walls
    for wall in map.walls {
        for x in range(wall.start_pos.x, wall.end_pos.x + 1) {
            for y in range(wall.start_pos.y, wall.end_pos.y + 1) {
                map_tiles[y-1][x-1] = 'B';
            }
        }
    }

    # Place obstacles, enemies, and player
    for obs in map.small_obstacles {
        map_tiles[obs.y-1][obs.x-1] = 'B';
    }
    for enemy in map.enemies {
        map_tiles[enemy.y-1][enemy.x-1] = 'E';
    }
    map_tiles[map.player_pos.y-1][map.player_pos.x-1] = 'P';

    # Add border walls
    map_tiles = [['B'] + row + ['B'] for row in map_tiles];
    map_tiles = [['B' for _ in range(map.level.width + 2)]] + map_tiles + [['B' for _ in range(map.level.width + 2)]];
    return [''.join(row) for row in map_tiles];
}
```

**Now:**

- walls become `B`
- enemies become `E`
- player starts at `P`
- border walls wrap around the map


<!-- ### <span style="color: orange">Map Conversion Process

The conversion function executes these operations:

1. **Initialize Grid**: Creates a 2D array filled with '.' (empty space)
2. **Place Walls**: Converts `Wall` objects into 'B' (block) characters
3. **Add Obstacles**: Places small obstacles as additional 'B' characters
4. **Position Enemies**: Places 'E' characters at enemy positions
5. **Place Player**: Sets 'P' character at the player's starting position
6. **Add Borders**: Surrounds the entire map with walls for boundaries

**Game Symbols:**

- `.` = Empty space (walkable)
- `B` = Block/Wall (impassable)
- `E` = Enemy (dangerous)
- `P` = Player (starting position) -->

## <span style="color: orange">Step 4: Test the AI Level Generator

We’ll test the system by generating AI levels and printing their difficulty, enemies, and maps.

Create a new file called `test_generator.jac`:

```jac linenums="1"
import from level_manager { LevelManager }

with entry {
    level_manager = LevelManager();

    print("Generating 3 AI-powered levels...\n");

    for i in range(3) {
        level, map_obj = level_manager.get_next_level();
        visual_map = level_manager.get_map(map_obj);

        print(f"=== LEVEL {i+1} ===");
        print(f"Difficulty: {level.difficulty}");
        print(f"Enemies: {level.num_enemies}");
        print(f"Walls: {level.num_wall}");
        print("Map:");
        for row in visual_map {
            print(row);
        }
        print("\n");
    }
}
```

Run this script:
```bash
jac run test_generator.jac
```

Expected output (AI may vary):
```
=== LEVEL 1 ===
Difficulty: 1
Enemies: 2
Walls: 3
Map:
BBBBBBBBBBBBBBBBBBBBBB
B..................B
B.....B............B
B..................B
B........E.........B
B..................B
B..........P.......B
B..................B
B.E................B
BBBBBBBBBBBBBBBBBBBBBB
```

## <span style="color: orange">Summary

<!-- This tutorial demonstrates building an AI-powered RPG level generator that implements: -->
Now you have:

- **AI Integration**: Using `by llm` syntax to delegate complex generation tasks
- **Structured Data Design**: Creating types that guide AI understanding
- **Progressive Systems**: Building difficulty curves and variety mechanisms
- **Practical Application**: Converting AI output into usable game content

The approach combines structured programming with AI creativity. The developer provides the framework and constraints, while the AI handles the creative details.

For more details access the [full documentation of MTP](/learn/jac-byllm/with_llm).

# <span style="color: orange">Tutorial: Building AI Agents for Fantasy Trading Game

This tutorial demonstrates how to build AI agents with persistent state that can conduct conversations, execute trades, and maintain context across interactions. The tutorial covers integrating AI functions for character generation and dialogue systems.

## <span style="color: orange">Overview

This tutorial covers building a trading game system with:
- AI-powered character generation functions
- AI agents that maintain conversation state
- Trading transaction system
- Persistent conversation history
- Context-aware decision making

## <span style="color: orange">Prerequisites

Required dependencies:

```bash
pip install byllm
```

OpenAI API key configuration:
```bash
export OPENAI_API_KEY="your-api-key-here"
```

## <span style="color: orange">Step 1: Define Game Data Structures

Define objects that represent the game world:

```jac
obj InventoryItem {
    has name: str;
    has price: float;
}

obj Person {
    has name: str;
    has age: int;
    has hobby: str;
    has description: str;
    has money: float;
    has inventory: list[InventoryItem];
}

obj Chat {
    has person: str;
    has message: str;
}
```

**Structure definitions:**
- **InventoryItem**: Tradeable objects with name and price
- **Person**: Character data including stats, money, and items
- **Chat**: Message history for conversation context

## <span style="color: orange">Step 2: Configure the AI Model

Configure the LLM for AI operations:

```jac
import from byllm {Model}

glob llm = Model(model_name="gpt-4o");
```

## <span style="color: orange">Step 3: Implement AI-Powered Character Generation

Create AI-integrated functions that generate game characters:

```jac
def make_player() -> Person by llm();

def make_random_npc() -> Person by llm();
```

These AI functions generate characters with appropriate attributes, starting money, and themed inventory items.

## <span style="color: orange">Step 4: Implement Transaction Logic

Create functions for core game mechanics:

```jac
def make_transaction(buyer_name: str, seller_name: str, item_name: str, price: int| None = None) -> bool {
    buyer = person_record[buyer_name];
    seller = person_record[seller_name];

    # Find item in seller's inventory
    item_to_buy = None;
    item_index = -1;
    for i in range(len(seller.inventory)) {
        if seller.inventory[i].name.lower() == item_name.lower() {
            item_to_buy = seller.inventory[i];
            item_index = i;
            break;
        }
    }

    price = price or item_to_buy.price;

    # Validate transaction
    if not item_to_buy or buyer.money < price {
        return False;
    }

    # Execute transfer
    buyer.money -= price;
    seller.money += price;
    buyer.inventory.append(item_to_buy);
    seller.inventory.pop(item_index);
    return True;
}
```

**Transaction processing:**
1. Locates the item in the seller's inventory
2. Validates the buyer has sufficient funds
3. Transfers money and items between characters

## <span style="color: orange">Step 5: Build Conversational AI Agent

Create an AI agent that maintains state and can execute actions:

```jac
def chat_with_player(player: Person, npc: Person, chat_history: list[Chat]) -> Chat
    by llm(method="ReAct", tools=[make_transaction]);
```

**AI agent characteristics:**
- **Maintains State**: Uses `chat_history` to remember previous interactions
- **Reasons**: Processes conversation context using ReAct method
- **Acts**: Can use tools like `make_transaction` when appropriate
- **Persists Context**: Builds understanding across multiple conversation turns

**Agent capabilities:**
- Remember previous conversations through persistent `chat_history`
- Execute trades when agreements are reached
- Negotiate prices within reasonable bounds
- Stay in character while being functional

## <span style="color: orange">Step 6: Implement the Game Loop

Connect all components in the main execution:

```jac
with entry {
    # Generate characters using AI functions
    player = make_player();
    npc = make_random_npc();

    # Register characters for transactions
    person_record[player.name] = player;
    person_record[npc.name] = npc;

    history = [];

    while True {
        # AI agent generates response with state
        chat = chat_with_player(player, npc, history);
        history.append(chat);

        # Display game state
        for p in [player, npc] {
            print(p.name, ":  $", p.money);
            for i in p.inventory {
                print("  ", i.name, ":  $", i.price);
            }
        }

        # Show NPC response and get player input
        print("\n[[npc]] >> ", chat.message);
        inp = input("\n[[Player input]] >> ");
        history.append(Chat(person=player.name, message=inp));
    }
}
```

**Game loop execution:**
1. Uses AI functions to generate characters (stateless)
2. Registers characters for transaction system
3. Uses the AI agent for NPC responses (stateful - maintains conversation history)
4. Accumulates conversation history for persistent context
5. Displays current game state after each interaction

## <span style="color: orange">AI Functions vs AI Agents

### <span style="color: orange">AI Functions (Stateless)

AI-integrated functions that operate without persistent state:
- `make_player()` and `make_random_npc()` - Generate characters but don't retain memory
- These are AI-powered utilities, not agents

### <span style="color: orange">AI Agents (Stateful)

AI systems that maintain persistent state across interactions:
- `chat_with_player()` with `chat_history` parameter - Retains conversation context
- Builds understanding over multiple turns
- Can reference previous interactions

## <span style="color: orange">Implementation Concepts

### <span style="color: orange">Tool Integration

The AI agent accesses application functions through tools:
- The `chat_with_player` AI agent can call `make_transaction`
- The AI extracts parameters from natural language
- Tool results are incorporated into responses

### <span style="color: orange">State Management

The AI agent maintains state through:
- Structured data objects (`Person`, `InventoryItem`)
- Conversation history (`Chat` objects)
- Global registries (`person_record`)

## <span style="color: orange">Running the Implementation

1. Install dependencies: `pip install byllm`
2. Configure OpenAI API key
3. Execute: `jac run fantasy_trading_game.jac`
4. Interact with the AI agent through natural language

Complete source code: [fantasy_trading_game.jac](https://github.com/jaseci-labs/jaseci/blob/main/jac-byllm/examples/fantasy_trading_game.jac)

## <span style="color: orange">Example Interaction

```
[[Npc]] >> Greetings, traveler! I am Thornwick the Wise, an ancient dwarf
with a passion for collecting rare gemstones. I've been mining these
mountains for over 200 years. I notice you carry yourself like an
adventurer - perhaps we could do some trading?

[[Player]] >> Hello! What do you have for sale?

[[Npc]] >> Ah, a direct sort - I like that! I have several treasures from
my decades of mining. I have a pristine Moonstone Amulet that glows with
inner light for 75 gold pieces, and a rare Dwarven Pickaxe forged by my
grandfather for 120 gold. What catches your interest, friend?
```

## <span style="color: orange">Summary

This tutorial demonstrates building AI agents with persistent state alongside AI-powered functions. The `chat_with_player` agent maintains conversation history and can execute trades through tool integration, while character generation functions provide stateless AI capabilities. The structured datatypes serve as a vocabulary for communicating game concepts to the AI, enabling natural language interactions that result in functional game mechanics.


