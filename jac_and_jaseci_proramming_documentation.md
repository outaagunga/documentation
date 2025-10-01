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

# **1. Introduction to Jac**
---
This chapter gets you started with the Jac programming language. We will begin by explaining what Jac is and how it works, then walk you through how to model relationships in your code. Along the way, you'll learn about Jac's core concepts through practical examples.


> Jac introduces Object-Spatial Programming (OSP), a new way of thinking about software where computation can move to your data. This allows you to build applications that can be easily scaled and distributed.

## **What is Jac and Why It Exists**
---
Jac was created to better handle the interconnected, graph-like data structures found in many modern applications, such as social networks, knowledge graphs, and AI workflows. While traditional programming languages treat relationships as secondary concerns, Jac makes them first-class citizens.

<br/>
### **Traditional Approach to Modelling Relationships**

In many programming languages, you can model relationships using structures like functions, classes, or pointers.

Let's look at how you might build a simple social network in Python. First, you would define a `Person` class. This class might include a list of other `Person` objects to represent friendships.


```python
class Person:
    def __init__(self, name):
        self.name = name
        self.friends = []

    def add_friend(self, friend):
        self.friends.append(friend)
        friend.friends.append(self)
```
<br/>

Since a friendship is a two-way connection, you need to make sure that when one person adds a friend, the new friend is also updated. This requires extra logic in your code.

```python
# Create people and relationships
alice = Person("Alice")
bob = Person("Bob")
charlie = Person("Charlie")

# Establish relationships
alice.add_friend(bob)
bob.add_friend(alice)  # This is redundant but necessary in Python
bob.add_friend(charlie)
charlie.add_friend(bob)  # Again, redundant but necessary
```
<br/>

### **Modelling Relationships Naturally**

Now, let's see how you can define the same relationships in Jac.

First, we will define a `Person` node and a `FriendsWith` edge. In Jac, nodes are like objects that hold data. The edge defines the type of relationship between two nodes.


```jac
node Person {
    has name: str;
}

edge FriendsWith;
```
<br/>

In the code above, we define a `Person` node with a `name` attribute and a `FriendsWith` edge. Now, you can create people and establish friendships between them in a much more natural way.


```jac
# Create people
alice = root ++> Person(name="Alice");
bob = root ++> Person(name="Bob");
charlie = root ++> Person(name="Charlie");

# Create relationships naturally
alice <+:FriendsWith:+> bob;
bob <+:FriendsWith:+> charlie;
```
<br/>

The line alice `<+:FriendsWith:+> bob;` creates a `FriendsWith` relationship, which is a typed connection between the two nodes. This is one of Jac's core features.


## **Object-Spatial Programming Paradigm Overview**
---
Object-Spatial Programming (OSP) is built on three fundamental concepts:

- **Nodes**: Stateful entities that hold data
- **Edges**: Typed relationships between nodes
- **Walkers**: Mobile computation that traverses the graph

### **Nodes: Data with Location**

Nodes are like objects that have a specific location within a graph structure.

```jac
node User {
    has username: str;
    has email: str;
    has created_at: str;
}

node Post {
    has title: str;
    has content: str;
    has likes: int = 0;
}

with entry {
    # This is where your program starts.
    # We will create the nodes in the graph here.
    user = root ++> User(
        username="alice",
        email="alice@example.com",
        created_at="2024-01-01"
    );

    post = user ++> Post(
        title="Hello Jac!",
        content="My first post in Jac"
    );

    print(f"User {user[0].username} created post: {post[0].title}");
}
```
<br/>

### **Edges: First-Class Relationships**

In Jac, edges are used to create typed connections between your nodes. Think of them as the lines that connect the dots in your data's structure.

```jac
node Person {
    has name: str;
    has age: int;
}

edge FamilyRelation {
    # Edges can also have properties
    has relationship_type: str;
}

with entry {
    # First, let's create our family members as nodes
    parent = root ++> Person(name="John", age=45);
    child1 = root ++> Person(name="Alice", age=20);
    child2 = root ++> Person(name="Bob", age=18);

    # Now, let's create the relationships between them
    parent +>:FamilyRelation(relationship_type="parent"):+> child1;
    parent +>:FamilyRelation(relationship_type="parent"):+> child2;
    child1 +>:FamilyRelation(relationship_type="sibling"):+> child2;

     # You can now ask questions about these relationships
    children = [parent[0]->:FamilyRelation:relationship_type=="parent":->(`?Person)];

    print(f"{parent[0].name} has {len(children)} children:");
    for child in children {
        print(f"  - {child.name} (age {child.age})");
    }
}
```
<br/>


### **Walkers: Mobile Computation**

Walkers are one of Jac's most interesting features. They are like little workers that you can program to travel through your graph of nodes and edges to perform tasks.
Next, we'll create a `walker` that can travel through our friend network and greet each person. Add the following `walker` definition to your code.

```jac
node Person {
    has name: str;
    has visited: bool = False;  # To keep track of who we've greeted
}

edge FriendsWith;

# This walker will greet every person it meets
walker GreetFriends {
    can greet with Person entry {
        if not here.visited {
            here.visited = True;
            print(f"Hello, {here.name}!");

            # Now, tell the walker to go to all connected friends
            visit [->:FriendsWith:->];
        }
    }
}

with entry {
    # Create friend network
    alice = root ++> Person(name="Alice");
    bob = root ++> Person(name="Bob");
    charlie = root ++> Person(name="Charlie");

    # Connect friends
    alice +>:FriendsWith:+> bob +>:FriendsWith:+> charlie;
    alice +>:FriendsWith:+> charlie;  # Alice also friends with Charlie

    # Start the walker on the 'alice' node to greet everyone
    alice[0] spawn GreetFriends();
}
```
<br/>

To make the walker visit a person's friends, we use the `visit` statement. It tells the walker to travel across any outgoing FriendsWith edges.

## Scale-Agnostic Programming Concept
---
One of the key benefits of Jac is that you can write your code once and have it work for a single user or for millions of users. Your code can run on a single computer or on a large, distributed system without any changes.


> The same Jac code works whether you have 1 user or 1 million users, running on a single machine or distributed across the globe.

### **Automatic Persistence**
Jac automatically saves any data that is connected to the graph's root node. This means you don't have to worry about setting up or managing a database. You can focus on what your application does, not on how it stores data.

Here is an example of a simple counter. When you run this on a Jac server, the count will be saved automatically.

```jac
node Counter {
    has count: int = 0;

    def increment() -> None;
}

impl Counter.increment {
    self.count += 1;
    print(f"Counter is now: {self.count}");
}

with entry {
    # Get or create counter
    counters = [root-->(`?Counter)];
    if not counters {
        counter = root ++> Counter();
        print("Created new counter");
    }

    # Increment and save automatically
    counter[0].increment();
}
```
<br/>

To see the automatic persistence for yourself, you can run the counter code on a Jac server. The server turns your Jac program into an API, and it handles saving the data in your graph between runs.
First, save the counter code into a file named "counter.jac". Then, run the following command in your terminal:

```jac
jac serve counter.jac
```

### **Multi-User Isolation**

When your Jac application is running on a server, Jac automatically gives each user their own isolated graph. This means you don't have to write any special code to keep one user's data separate from another's; it's handled for you. The root node in your code always refers to the graph of the current user.

```jac
node UserProfile {
    has username: str;
    has bio: str = "";
}

walker GetProfile {
    can get_user_info with entry {
         # 'root' automatically points to the current user's graph
        profiles = [root-->(`?UserProfile)];
        if profiles {
            profile = profiles[0];
            print(f"Profile: {profile.username}");
            print(f"Bio: {profile.bio}");
        } else {
            print("No profile found");
        }
    }
}

walker CreateProfile {
    has username: str;
    has bio: str;

    can create with entry {
        # It looks for the profile connected to the current user's root
        profile = root ++> UserProfile(
            username=self.username,
            bio=self.bio
        );
        print(f"Created profile for {profile[0].username}");
    }
}

with entry {
    # This code works for any user automatically
    CreateProfile(username="alice", bio="Jac developer") spawn root;
    GetProfile() spawn root;
}
```
<br/>

In a live application, one user might call `CreateProfile` to set up their information. When another user calls `GetProfile`, they will only see their own profile, not anyone else's.

## Comparison with Python and Traditional Languages
---

If you have experience with Python, you'll find Jac's syntax easy to learn. Core programming concepts like variables, functions, and control flow work in a very similar way.

### **Syntax Familiarity**

For example, take a look at how you define and use functions and if/else statements in Jac.

```jac
# Variables and functions work similarly
def calculate_average(numbers: list[float]) -> float {
    if len(numbers) == 0 {
        return 0.0;
    }
    return sum(numbers) / len(numbers);
}

with entry {
    scores = [85.5, 92.0, 78.5, 96.0, 88.5];
    avg = calculate_average(scores);
    print(f"Average score: {avg}");

    # Control flow is familiar
    if avg >= 90.0 {
        print("Excellent performance!");
    } elif avg >= 80.0 {
        print("Good performance!");
    } else {
        print("Needs improvement.");
    }
}
```
This familiar foundation makes it easier for you to get started and begin building with Jac's unique features like nodes, edges, and walkers.
<br/>


### Key Differences

| Aspect | Python | Jac |
|--------|--------|-----|
| **Relationships** | Manual references | First-class edges |
| **Persistence** | External database | Automatic |
| **Multi-user** | Manual session management | Built-in isolation |
| **Distribution** | Complex setup | Transparent |
| **Graph Operations** | Manual traversal | Spatial queries |
| **Type System** | Optional hints | Mandatory annotations |

## Simple Friend Network Example
---

Let's walk through building a complete friend network from scratch. This will show you how Jac's core concepts—nodes, edges, and walkers—work together in a practical example.

### Step 1: Define Your Nodes and Edges
First, we need to define the structure of our data. We'll create a `Person` node to hold information about each individual and a `FriendsWith` edge to represent their relationships.

```jac
node Person {
    has name: str;
    has age: int;
    has interests: list[str] = [];
}

edge FriendsWith {
    has since: str;
    has closeness: int;  # 1-10 scale
}
```
<br/>

### Step 2: Create the People and Their Connections

Now that we have our blueprints, let's create a few people and connect them as friends. Notice how we can add data directly to the `FriendsWith` edge when we create it.

```jac
# Create friend network
alice = root ++> Person(
    name="Alice",
    age=25,
    interests=["coding", "music", "hiking"]
);

bob = root ++> Person(
    name="Bob",
    age=27,
    interests=["music", "sports", "cooking"]
);

charlie = root ++> Person(
    name="Charlie",
    age=24,
    interests=["coding", "gaming", "music"]
);

# Create friendships with metadata
alice +>:FriendsWith(since="2020-01-15", closeness=8):+> bob;
alice +>:FriendsWith(since="2021-06-10", closeness=9):+> charlie;
bob +>:FriendsWith(since="2020-12-03", closeness=7):+> charlie;
```
<br/>

### Step 3: Create a Walker to Find Common Interests

Next, we need a way to analyze our network. Let's create a `walker` that can travel between friends and find out what interests they have in common.

```jac
walker FindCommonInterests {
    # The walker needs to know who we're comparing against.
    has target_person: Person;
    # It will store the results of its search here.
    has common_interests: list[str] = [];

    # This ability runs automatically whenever the walker lands on a Person node.
    can find_common with Person entry {
        # We don't want to compare the person with themselves.
        if here == self.target_person {
            return;  # Skip self
        }

        # Find any interests this person shares with our target_person
        shared = [];
        for interest in here.interests {
            if interest in self.target_person.interests {
                shared.append(interest);
            }
        }

        # If we found any, print them and add them to our list.
        if shared {
            self.common_interests.extend(shared);
            print(f"{here.name} and {self.target_person.name} both like: {', '.join(shared)}");
        }
    }
}
```
<br/>

To use this walker, we'll give it a `target_person` (like Alice) and then "spawn" it on her friends' nodes. As the walker visits each friend, its `find_common ability` will trigger, comparing interests and printing the results.


### Step 4: Bring It All Together
Finally, let's update our `with entry` block to use the walker and analyze the network we created. We'll start by finding all of Alice's friends and then send our walker to visit them.

<div class="code-block">
```jac
node Person {
    has name: str;
    has age: int;
    has interests: list[str] = [];
}

edge FriendsWith {
    has since: str;
    has closeness: int;  # 1-10 scale
}

walker FindCommonInterests {
    has target_person: Person;
    has common_interests: list[str] = [];

    can find_common with Person entry {
        if here == self.target_person {
            return;  # Skip self
        }

        # Find shared interests
        shared = [];
        for interest in here.interests {
            if interest in self.target_person.interests {
                shared.append(interest);
            }
        }

        if shared {
            self.common_interests.extend(shared);
            print(f"{here.name} and {self.target_person.name} both like: {', '.join(shared)}");
        }
    }
}

with entry {
    # Create friend network
    alice = root ++> Person(
        name="Alice",
        age=25,
        interests=["coding", "music", "hiking"]
    );

    bob = root ++> Person(
        name="Bob",
        age=27,
        interests=["music", "sports", "cooking"]
    );

    charlie = root ++> Person(
        name="Charlie",
        age=24,
        interests=["coding", "gaming", "music"]
    );

    # Create friendships with metadata
    alice +>:FriendsWith(since="2020-01-15", closeness=8):+> bob;
    alice +>:FriendsWith(since="2021-06-10", closeness=9):+> charlie;
    bob +>:FriendsWith(since="2020-12-03", closeness=7):+> charlie;

    print("=== Friend Network Analysis ===");

    # 1. Find all nodes connected to Alice by a FriendsWith edge
    alice_friends = [alice[0]->:FriendsWith:->(`?Person)];
    print(f"Alice's friends: {[f.name for f in alice_friends]}");

    # 2. Create an instance of our walker, telling it to compare against Alice
    finder = FindCommonInterests(target_person=alice[0]);

    # 3. Send the walker to visit each of Alice's friends
    for friend in alice_friends {
        friend spawn finder;
    }

    # Find close friendships (closeness >= 8)
    close_friendships = [root-->->:FriendsWith:closeness >= 8:->];
    print(f"Close friendships ({len(close_friendships)} found):");
}
```
</div>

<br/>

This example shows how you can model complex relationships and then create walkers to navigate and analyze them using Jac's Object-Spatial Programming model. The walker moves through the graph, performing actions based on the data it finds, all in a way that is clear and easy to understand.


## Key Takeaways

Here’s a quick summary of what you’ve learned in this chapter.

---
**Core Concepts:**

- **Object-Spatial Programming (OSP)**: This is the core idea behind Jac. It means you can move your code (computation) to your data using a combination of nodes, edges, and walkers.
- **Nodes**: These are the primary objects in your program that hold your data.
- **Edges**: These are the connections that define the relationships between your nodes.
- **Walkers**: These are small, mobile programs you create to travel through your network of nodes and edges to perform tasks.

**Key Advantages:**

- **Write Code That Scales**: Your Jac code works automatically for a single user or for millions, running on one machine or a distributed system, without needing changes.
- **No Database Headaches**: Jac automatically saves the data in your graph, so you don't have to set up or manage a database yourself.
- **Model Relationships Naturally: Working with connected data feels intuitive and straightforward because of the graph-based approach.
- **Built-in User Separation**: Jac automatically keeps each user's data separate, so you don't have to build complex logic to manage user sessions.
- **Familiar Python-like Syntax**: Jac is easy to learn for Python developers, letting you focus on its powerful new features right away.


Start thinking about problems that involve connected data, as these are the areas where Jac truly excels:
- Social networks and user connections
- Knowledge graphs and information relationships
- Workflow systems and process connections
- Data pipelines and transformation chains

!!! Note
    Remember: Jac shines when your problem naturally involves connected data!

---

*Ready to start your Object-Spatial Programming journey? Let's get to know Variables, Types, and Basic Syntax next!*


# 2. Variables, Types, and Basic Syntax
---
**Variables** how you store and manage data in your program. Each variable has a **type**, which tells Jac what kind of information it can hold, like numbers or text.


Jac requires you to declare the type for every variable you create. This is known as **strong typing**. Unlike in Python, where type hints are optional, Jac makes them mandatory. This helps you catch common errors such as runtime type errors early and makes your code easier to read and maintain, especially as your projects grow.


### Variable Declarations
To declare a variable in Jac, you specify its name, its type, and its initial value.

```jac
with entry {
    # Basic type annotations (Jac requires you to specify the type for each variable.)
    student_name: str = "Alice";
    grade: int = 95;
    gpa: float = 3.8;
    is_honor_student: bool = True;
}
```
<br />

A **literal** is a fixed value you write directly in your code, like "Alice" or 95. Jac uses common literals like *string*, *integer*, *float*, and *boolean*. It also introduces a special kind of literal called an **architype** (node, edge, and walker), which was briefly discussed in the prvious chapter.We will explore architypes in more detail later in chapter 9.

Jac supports both local and global variables. Local variables are defined within a block and are not accessible outside it, while global variables can be accessed anywhere in the code.

### Local Variables
```jac
def add_numbers(a: int, b: int) -> int {
    result: int = a + b;  # Local variable
    return result;
}
with entry {
    sum = add_numbers(5, 10);
    print(f"Sum: {sum}");
}
```
<br />

### Global Variables
Sometimes, you may need a variable that can be accessed from anywhere in your program. These are called global variables. In Jac, you can declare them using the `glob` keyword.
Global variables are most often used for defining constants, like configuration settings or version numbers, that need to be available throughout your code.


```jac
glob school_name: str = "Jac High School";
glob passing_grade: int = 60;
glob honor_threshold: float = 3.5;

def get_school_info() -> str {
    :g: school_name; # Accessing global variable
    return f"Welcome to {school_name}";
}

with entry {
    print(get_school_info());
    print(f"Honor threshold is {honor_threshold}");
}
```
<br />

### Integers
An integer is a whole number (without a decimal point). In Jac, you declare integers using the `int` type.

```jac
with entry {
    student_id: int = 12345;
    print(student_id);
}
```
<br />

### Floats
A float is a number with a decimal point. You declare floats using the `float` type.
```jac
with entry {
    gpa: float = 3.85;
    print(gpa);
}
```
<br />

### Strings
A string is a sequence of characters, like a name or a sentence. Strings are declared with the `str` type and are enclosed in quotes.

```jac
with entry {
    student_name: str = "Alice Johnson";
    # You can use f-strings to easily include variables in your output.
    print(f"Student Name: {student_name}");
}
```
<br />

### Booleans
A boolean represents a truth value: either True` or `False`. You declare booleans using the `bool` type.

```jac
with entry {
    is_enrolled: bool = True;
    print(f"Is enrolled: {is_enrolled}");
}
```
<br />




### Any Type for Flexibility
---
Sometimes, you may need a variable that can hold values of different types. For these situations, Jac provides the `any` type similar to Python's dynamic typing.

```jac
with entry {
    # This variable can hold different kinds of data.
    grade_data: any = 95;
    print(f"Grade as number: {grade_data}");

    # Now, we can assign a string to the same variable.
    grade_data = "A";  # Now a letter grade
    print(f"Grade as letter: {grade_data}");
}
```
<br />


## Jac REPL
---
!!! warning "Warning"
    The Jac REPL (Read-Eval-Print Loop) feature is currently under development and not yet available. Please run your code in files using the jac run command for now.

## Wrapping Up
---
In this chapter, we covered the basics of working with variables and types in Jac. You learned how to declare variables and use fundamental data types. This foundation is essential as we move on to explore Jac's more advanced features.


!!! tip "Try It Yourself"
    Practice the basics by creating:
    - A simple temperature converter (Celsius to Fahrenheit).
    - A basic calculator that can add, subtract, multiply, and divide.
    - An inventory tracker that stores an item's name (string), quantity (integer), and price (float).
    - A text processing utility

    Remember: As you build, focus on using the correct types for your variables. This is a key habit for writing good Jac code!

---
# 3. Functions, Control Flow, and Collections in Jac
---
In this chapter, you will learn how to organize your code into reusable blocks called functions. We will also cover how to direct the flow of your program with control flow statements and how to work with groups of data using collections.

## Functions and Type Annotations
---

As your programs become more complex, you'll often find yourself writing the same lines of code in multiple places. **Functions** help you solve this by letting you group a block of code together and give it a name. You can then "call" that function whenever you need to perform that specific task, making your code cleaner and easier to manage.

In Jac, you define a function using the `def` keyword. Just like with variables, you must specify the data type for each of the function's parameters and for the value it returns.

Let's look at an example. Here is how you can create a simple function that adds two numbers together.

```jac
def add_numbers(a: int, b: int) -> int {
    result: int = a + b;
    return result;
}
```

### Basic Calculator Program
---
Let's put what you've learned about functions into practice by building a simple calculator. We will create four functions, one for each basic math operation: addition, subtraction, multiplication, and division.
Each function will take two numbers (floats) as input and return the result.

```jac
# calculator.jac
def add(a: float, b: float) -> float {
    return a + b;
}

def subtract(a: float, b: float) -> float {
    return a - b;
}

def multiply(a: float, b: float) -> float {
    return a * b;
}

def divide(a: float, b: float) -> float {
    return a / b;
}

with entry {
    print("=== Simple Calculator ===");

    # Test calculations
    num1: float = 10.0;
    num2: float = 3.0;

    print(f"{num1} + {num2} = {add(num1, num2)}");
    print(f"{num1} - {num2} = {subtract(num1, num2)}");
    print(f"{num1} * {num2} = {multiply(num1, num2)}");
    print(f"{num1} / {num2} = {divide(num1, num2)}");
}
```
<br />

You might have noticed that our `divide` function has a potential issue: it doesn't handle cases where the second number is zero. Trying to divide by zero will cause an error in our program.
Don't worry about this for now. We will cover how to handle potential errors gracefully in a later section. For now, this example shows how you can use functions to create a clean and organized program.

<br />

## Basic Object Oriented Programming
---
Jac is primarily an Object Spatial Language, but it also supports Object Oriented Programming (OOP) concepts. An object is a self-contained unit that combines data and behavior.
In Jac, you can define a blueprint for an object using the `obj`  keyword. Inside this blueprint, you define the object's data (called attributes) using the `has` keyword, and its behavior (called methods) using the `def` keyword.

Let's create a Student object to see how this works. A student has data (like a name, age, and GPA) and can also perform actions (like providing their information).

### Defining an Object
```jac
obj Student {
    has name: str;
    has age: int;
    has gpa: float;

     # Notice the 'self' parameter, which refers to the object itself.
    def get_info() -> str {
        return f"Name: {self.name}, Age: {self.age}, GPA: {self.gpa}";
    }
}

with entry {
    student: Student = Student("Alice", 20, 3.8);  # Create a new Student object
    print(student.get_info());
}
```
<br />

You might be wondering, "Where is the constructor or `__init__` method?" That's a great question! Jac simplifies the process. Instead of needing a special method to initialize the object, you simply define the attributes with `has` and provide their values directly when you create a new instance of the object.


!!! note
    If you have experience with Python, you might notice that Jac's `obj` works in a way that is similar to Python's dataclasses. They both provide a straightforward way to create objects that are primarily used to group and manage data.


### Enhanced Calculator with Object-Oriented Design
Now, let's improve our calculator by turning it into an object. By using an `obj`, we can not only group the calculation methods together but also add a new feature: a history of all the calculations we perform. This makes our calculator more powerful and easier to use.
We will create a Calculator object that has methods for adding and subtracting, as well as a history attribute to keep a record of each operation.

<div class="code-block">

```jac
# oop_calculator.jac

obj Calculator {
    has history: list[str] = [];

    def add(a: float, b: float) -> float {
        result: float = a + b;
        self.history.append(f"{a} + {b} = {result}");
        return result;
    }

    def subtract(a: float, b: float) -> float {
        result: float = a - b;
        self.history.append(f"{a} - {b} = {result}");
        return result;
    }

    def get_history() -> list[str] {
        return self.history;
    }

    def clear_history() {
        self.history = [];
    }
}

with entry {
    # First, create an instance of our Calculator object.
    calc = Calculator();

    # Perform calculations
    result1: float = calc.add(5.0, 3.0);
    result2: float = calc.subtract(10.0, 4.0);

    print(f"Results: {result1}, {result2}");

    # Show history
    print("\nCalculation History:");
    for entry in calc.get_history() {
        print(f"  {entry}");
    }
}
```
</div>
<br />

This example shows how you can use familiar Object-Oriented Programming (OOP) concepts right here in Jac. Jac is designed to work with both OOP and its own Object-Spatial features. This means you can start with what you know and then gradually incorporate Jac's unique graph-based tools, like nodes and walkers, when your project can benefit from them.


## Collections and Data Structures
---
Since Jac is a super-set of Python, it supports the same collection types: lists, dictionaries, sets, and tuples. However, Jac enforces type annotations for all collections, ensuring type safety and clarity.

### Lists
Lists are ordered collections of items that can be of mixed types. In Jac, lists are declared with the `list` type.

Let's create a list to store a student's grades.

```jac
with entry {
    # Create an empty list for storing integer grades
    alice_grades: list[int] = [];

    # Append grades to the list
    alice_grades.append(88); # [88]
    alice_grades.append(92); # [88, 92]
    alice_grades.append(85); # [88, 92, 85]

    # Access grades by index
    first_grade: int = alice_grades[0];  # 88
    print(f"Alice's first grade: {first_grade}");

    # print the entire list of grades
    print(f"Alice's grades: {alice_grades}");
}
```
<br />

```text
$ jac run example.jac
Alice's first grade: 88
Alice's grades: [88, 92, 85]
```


### Dictionaries
Dictionaries are perfect for storing data as key-value pairs, which allows you to look up a value instantly if you know its key. You declare a dictionary with the `dict` type, specifying the type for the keys and the values.

Here is how you could use a dictionary to create a gradebook where student names are the keys and their grades are the values.

```jac
with entry {
    # Class gradebook
    math_grades: dict[str, int] = {
        "Alice": 92,
        "Bob": 85,
        "Charlie": 78
    };

    # Access grades by student name
    print(f"Alice's Math grade: {math_grades['Alice']}");
    print(f"Bob's Math grade: {math_grades['Bob']}");
    print(f"Charlie's Math grade: {math_grades['Charlie']}");
}
```
<br />

```text
$ jac run example.jac
Alice's Math grade: 92
Bob's Math grade: 85
Charlie's Math grade: 78
```

### Sets
A set is an unordered collection that does not allow duplicate items. This makes them very useful for tasks like tracking unique entries or comparing two groups of data. You declare a set with the `set` type.

In this example, we'll use sets to find out which courses two students have in common.

```jac
with entry {
    # Track unique courses
    alice_courses: set[str] = {"Math", "Science", "English"};
    bob_courses: set[str] = {"Math", "History", "Art"};

    # Find common courses
    common_courses = alice_courses.intersection(bob_courses);
    print(f"Common courses: {common_courses}");

    # All unique courses
    all_courses = alice_courses.union(bob_courses);
    print(f"All courses: {all_courses}");
}
```
The `intersection` method finds items that are present in both sets, while the `union` method combines both sets into one, automatically removing any duplicates. These are standard operations provided by Python’s built-in `set` type, and Jac supports them as well. For a more comprehensive overview of collection-related functions in Python, refer to the [official Python documentation](https:#docs.python.org/3/tutorial/datastructures.html).


## Collection Comprehensions
---
Jac supports list and dictionary comprehensions, which are a concise and powerful way to create new collections by processing existing ones. Let's see how you can use them to work with a gradebook.

Imagine you have a list of test scores and you want to quickly create a new list containing only the passing grades.

```jac
with entry {
    # Raw test scores
    test_scores: list[int] = [78, 85, 92, 69, 88, 95, 72];

    # Get passing grades (70 and above)
    passing_scores: list[int] = [score for score in test_scores if score >= 70];
    print(f"Passing scores: {passing_scores}");
}
```
<br />

The list comprehension syntax in Jac is similar to Python:
```[expression for item in iterable if condition]``` where,
`expression` is the value to include in the new list,
`item` is the variable representing each element in the original collection,
`iterable` is the collection being processed, and
`condition` is an optional filter.

Now, what if you wanted to apply a curve by adding 5 points to every score? A comprehension makes this simple too.

```jac
with entry {
    # Raw test scores
    test_scores: list[int] = [78, 85, 92, 69, 88, 95, 72];

    # Get passing grades (70 and above)
    passing_scores: list[int] = [score for score in test_scores if score >= 70];
    print(f"Passing scores: {passing_scores}");

    # Create a new list where each score is 5 points higher.
    curved_scores: list[int] = [score + 5 for score in test_scores];
    print(f"Curved scores: {curved_scores}");
}
```
<br />

## Control Flow with Curly Braces
---
Earlier, we built a simple calculator but left a problem in our `divide` function: it couldn't handle division by zero. To write robust programs, you need to control if and when certain blocks of code are executed. Jac uses control flow statements like `if`, `elif`, and `else` for this, using curly braces {} to group the code for each block.


### If Statements
An `if` statement allows you to execute code conditionally based on whether a certain condition is true. In Jac, we use curly braces `{}` to define the block of code that should be executed if the condition is met.

Let's now fix our `divide` function. With an `if` statement, we can check if the second number is zero before we try to do the division. This allows us to handle the problem gracefully instead of letting our program crash.

```jac
# We can specify multiple possible return types using the '|' symbol.
def divide(a: float, b: float) -> float | str {
    # Check if b is zero before dividing.
    if b == 0.0 {
        return "Error: Cannot divide by zero!";
    }
    # If b is not zero, we can safely perform the division.
    return a / b;
}
```
<br />
In this updated function, we first check if b is equal to 0.0. If the condition is `True`, the code inside the curly braces {} is executed, and the function returns an error message. If the condition is `False`, the `if` block is skipped, and the function proceeds to the next line to perform the division.

### Conditional Logic `if-elif-else`

Often, you'll need to check for more than just one condition. For these situations, you can use a chain of `if`, `elif` (short for "else if"), and `else` statements. This lets you create a clear path for your program to follow based on different possibilities.

Let's expand on our gradebook example by creating a function that assigns a letter grade based on a score. We'll use a list comprehension to apply this function to a whole list of scores.


```jac
def classify_grade(score: int) -> str {
    if score >= 90 {
        return "A";
    } elif score >= 80 {
        return "B";
    } elif score >= 70 {
        return "C";
    } elif score >= 60 {
        return "D";
    } else {
        return "F";
    }
}

with entry {
    # Raw test scores
    test_scores: list[int] = [78, 85, 92, 69, 88, 95, 72];

    # Get passing grades (70 and above)
    passing_scores: list[int] = [score for score in test_scores if score >= 70];
    print(f"Passing scores: {passing_scores}");

    # Apply curve (+5 points)
    curved_scores: list[int] = [score + 5 for score in test_scores];
    print(f"Curved scores: {curved_scores}");

    # Classify each score
    grades: list[str] = [classify_grade(score) for score in test_scores];
    print(f"Grades: {grades}");
}
```
<br />

When you run this code, you'll see how the `classify_grade` function was applied to each score in the list:

```text
$ jac run example.jac
Passing scores: [78, 85, 92, 88, 95, 72]
Curved scores: [83, 90, 97, 74, 93, 100, 77]
Grades: ['C', 'B', 'A', 'D', 'B', 'A', 'C']
```

## Working with Loops
---
Loops allow you to run a block of code multiple times, which is essential for working with collections or performing repetitive tasks. Jac provides several ways to create loops, each suited for different situations including traditional `for` loops, Jac's unique `for-to-by` loops, and clear, structured `while` loops.

### Traditional For Loops

The standard `for` loop is used to iterate over the items in a collection, such as a list or a dictionary.
Let's write a function that calculates the average grade for each student in a class. We'll use a `for` loop to go through the dictionary of students and another nested `for` loop to go through each student's list of grades.

```jac

def process_class_grades(grades: dict[str, list[int]]) -> None {
    # This loop iterates through the key-value pairs in the dictionary.
    for (student, student_grades) in grades.items() {
        total: int = 0;
        # This nested loop iterates through the list of grades for each student.
        for grade in student_grades {
            total += grade;
        }
        average: float = total / len(student_grades);
        print(f"{student}'s average grade: {average}");
    }
}

with entry {
    class_grades: dict[str, list[int]] = {
        "Alice": [88, 92, 85],
        "Bob": [79, 83, 77],
        "Charlie": [95, 89, 92]
    };

    process_class_grades(class_grades);
}

```
 <br />

### Jac's Unique For-to-by Loops
Jac introduces a special `for-to-by` loop that gives you precise control over a sequence of numbers. This is useful when you need to iterate within a specific range with a defined step.

```jac
with entry {
    print("Converting scores (0-100) to GPA (0-4.0):");

    # This loop starts at 60, continues as long as score <= 100,
    # and increases the score by 10 in each step.
    for score = 60 to score <= 100 by score += 10 {
        gpa: float = (score - 60) * 4.0 / 40.0;
        print(f"Score {score} -> GPA {gpa}");
    }
}
```
<br />

### While Loops
A `while` loop continues to run as long as its condition remains True. This is useful when you don't know in advance how many times you need to loop.

```jac
with entry {
    count: int = 1;
    total: int = 0;

    # This loop will continue as long as 'count' is less than or equal to 5.
    while count <= 5 {
        print(f"Adding {count} to total");
        total += count;
        count += 1;
    }
    print(f"Final total: {total}");
}
```
<br />


## Pattern Matching for Complex Logic
---
When you have a variable that could be one of many different types or values, a long chain of if-elif-else statements can become hard to read. Pattern matching provides a cleaner and more powerful way to handle these complex situations.

```jac
def process_grade_input(input: any) -> str {
    # The 'match' statement checks the input against several possible patterns.
    match input {
        case int() if 90 <= input <= 100:
            return f"Excellent work! Score: {input}";
        case int() if 80 <= input < 90:
            return f"Good job! Score: {input}";
        case int() if 70 <= input < 80:
            return f"Satisfactory. Score: {input}";
        case int() if 0 <= input < 70:
            return f"Needs improvement. Score: {input}";
        case str() if input in ["A", "B", "C", "D", "F"]:
            return f"Letter grade received: {input}";
        case list() if len(input) > 0:
            avg = sum(input) / len(input);
            return f"Average of {len(input)} grades: {avg}";
        # The 'catch-all' case: If no other pattern matched.
        case _:
            return "Invalid grade input";
    }
}

with entry {
    print(process_grade_input(95));        # Number grade
    print(process_grade_input("A"));       # Letter grade
    print(process_grade_input([88, 92, 85])); # List of grades
}
```
<br />


## Exception Handling
---
Sometimes, things go wrong in a program unexpectedly. A user might enter invalid data, or a file might be missing. Exception handling allows you to anticipate these potential errors and manage them without crashing your program.

In Jac, you use a `try...except` block to do this. You put the code that might cause an error inside the `try` block, and the code to handle the error inside the `except` block. You can also use the raise keyword to create your own custom errors.

```jac
def safe_calculate_gpa(grades: list[int]) -> float {
    try {
        if len(grades) == 0 {
            # If the list of grades is empty, we create our own error.
            raise ValueError("No grades provided");
        }

        total = sum(grades);
        return total / len(grades);

    } except ValueError as e {
        # If a ValueError occurs, this block will run.
        print(f"Error: {e}");
        return 0.0;
    }
}

def validate_grade(grade: int) -> None {
    if grade < 0 or grade > 100 {
        raise ValueError(f"Grade {grade} is out of valid range (0-100)");
    }
}

with entry {
    # Test 1: A valid calculation.
    valid_grades: list[int] = [85, 90, 78];
    gpa: float = safe_calculate_gpa(valid_grades);
    print(f"The calculated GPA is: {gpa}");

     # Test 2: Handling a custom validation error.
    try {
        validate_grade(150);
    } except ValueError as e {
        print(f"A validation error occurred: {e}");
    }
}
```
<br />

## Comments in Jac
---
Comments help document your Jac code clearly. Jac supports both single-line and multiline comments.

```jac
with entry {
    # This is a single-line comment
    student_name: str = "Alice";

    #*
        This is a
        multi-line comment.
    *#

    grades: list[int] = [88, 92, 85];

    print(student_name);
    print(grades);
}
```
<br />

## Project Structure Conventions
---
As your Jac programs grow, keeping your code organized is key to making it easy to manage and update. Jac encourages a project structure that separates the what from the how—that is, separating the definition of your objects from the code that makes them work.

A good way to structure your project is to create different folders for your main program logic, your data models, and any utility functions you might need.

Here is a common and effective way to organize a Jac project:

```
my_project/
├── main.jac              # Main program
├── models/
│   ├── user.jac          # User interface
│   ├── user.impl.jac     # User implementation
│   └── user.test.jac     # User tests
└── utils/
    ├── helpers.jac       # Helper functions
    └── constants.jac     # Application constants
```
<br />

### Interface and Implementation Separation
You'll notice that for our User model, we have two files: `user.jac` and `user.impl.jac`. This is a recommended practice in Jac for keeping your code clean. `user.jac` and `user.impl.jac`. The interface file (.jac) is like a blueprint. It defines what an object looks like—its attributes and the methods it should have. The implementation file (.impl.jac) contains the actual code that makes the methods work.

Let's look at an example. We want to create a User object that has a `name` and an `email`. We also need methods to validate the user's information and to get a nicely formatted display name.

First, we define the interface in `user.jac`. This file outlines the structure of our User object.

```jac
# user.jac - Interface declaration
obj User {
    # It has these attributes.
    has name: str;
    has email: str;

    # And it must have these methods.
    # We don't write the code for them here.
    def validate() -> bool;
    def get_display_name() -> str;
}
```
<br />
Next, we provide the implementation in `user.impl.jac`. This is where we write the code for the methods we defined in the interface. Jac automatically links these implementation blocks to the interface.

```jac
# user.impl.jac - Implementation

# The implementation for the validate() method.
impl User.validate {
    # It checks if the email contains an '@' and the name is not empty.
    return "@" in self.email and len(self.name) > 0;
}

# The implementation for the get_display_name() method.
impl User.get_display_name {
    return f"{self.name} <{self.email}>";
}
```
<br />



## Common Beginner Mistakes and Solutions
---
Most beginner issues stem from Jac's stricter type requirements compared to Python. Here are the most common mistakes and their solutions.

| **Issue** | **Solution** |
|-----------|--------------|
| Missing semicolons | Add `;` at the end of statements |
| Missing type annotations | Add types to all variables: `x: int = 5;` |
| No entry block | Add `with entry { ... }` for executable scripts |
| Python-style indentation | Use `{ }` braces instead of indentation |

### Example of Common Fixes
Someone unfamiliar with Jac might write code like this:

```jac
# This won't work - missing types and semicolons
def greet(name) {
    return f"Hello, {name}"
}

# Missing entry block
print(greet("World"))
```
<br />

The corrected version of the code would be:
```jac
# This works - proper types and syntax
def greet(name: str) -> str {
    return f"Hello, {name}";
}

with entry {
    print(greet("World"));
}
```
<br />




## Complete Example: Simple Grade Book System
---
Let's bring everything you've learned in this chapter together to build a complete program. We will create a simple gradebook system using an object to manage students and their grades. This example will showcase how functions, collections, and control flow work together in a practical application.
First, we will define the interface for our `GradeBook` object, outlining its attributes and methods.

<div class="code-block">

```jac
obj GradeBook {
    has students: dict[str, list[int]] = {};

    def add_student(name: str) -> None;
    def add_grade(student: str, grade: int) -> None;
    def get_average(student: str) -> float;
    def get_all_averages() -> dict[str, float];
}

impl GradeBook.add_student(name: str) -> None {
    if name not in self.students {
        self.students[name] = [];
        print(f"Added student: {name}");
    } else {
        print(f"Student {name} already exists");
    }
}

impl GradeBook.add_grade(student: str, grade: int) -> None {
    if grade < 0 or grade > 100 {
        print(f"Invalid grade: {grade}");
        return;
    }

    if student in self.students {
        self.students[student].append(grade);
        print(f"Added grade {grade} for {student}");
    } else {
        print(f"Student {student} not found");
    }
}

impl GradeBook.get_average(student: str) -> float {
    if student not in self.students or len(self.students[student]) == 0 {
        return 0.0;
    }
    grades = self.students[student];
    return sum(grades) / len(grades);
}

impl GradeBook.get_all_averages() -> dict[str, float] {
    averages: dict[str, float] = {};
    for (student, grades) in self.students.items() {
        if len(grades) > 0 {
            averages[student] = sum(grades) / len(grades);
        }
    }
    return averages;
}

with entry {
    # Create gradebook
    gradebook = GradeBook();

    # Add students
    gradebook.add_student("Alice");
    gradebook.add_student("Bob");

    # Add grades
    gradebook.add_grade("Alice", 88);
    gradebook.add_grade("Alice", 92);
    gradebook.add_grade("Bob", 85);
    gradebook.add_grade("Bob", 79);

    # Get results
    all_averages = gradebook.get_all_averages();
    for (student, avg) in all_averages.items() {
        letter = "A" if avg >= 90 else "B" if avg >= 80 else "C" if avg >= 70 else "F";
        print(f"{student}: {avg} ({letter})");
    }
}
```
</div>
<br />



## Wrapping Up
---

Congratulations! You've just covered the essential building blocks of the Jac programming language. In this chapter, you learned about:
- Jac's strong type system and how to declare variables.
- Creating reusable code with functions and objects.
- Directing your program's logic with control flow statements like if, for, and while.
- Managing data with collections like lists and dictionaries.
- Handling errors gracefully with exception handling.
- Organizing your code with a clean project structure.

These fundamental concepts will be your foundation as you begin to explore the more advanced features that make Jac truly powerful.

---

*Now that you have a solid grasp of Jac's core syntax, you're ready to move on to the next chapter. We'll explore how to integrate AI directly into your programs and work with Jac's unique graph-based data structures.*

# Chapter 4: A Deeper Look at Functions
---

In this chapter, we will explore the more advanced capabilities of functions in Jac. You will learn how Jac supports functional programming patterns, how to use functions as first-class citizens, and how to integrate AI directly into your function definitions. We will build a small math library to demonstrate these features in a practical context.

> In Jac, functions are treated as first-class citizens, meaning they can be stored in variables, passed as arguments to other functions, and returned from them, just like any other data type such as an integer or a string.

## Functional Programming in Jac
---
Functional programming is a style of writing software that treats computation as the evaluation of mathematical functions. While Jac is not a strict functional programming language, it provides strong support for functional programming concepts, enabling you to write more modular and expressive code.


### Function as First-Class Citizens
The core principle of functional programming is treating functions as first-class citizens. This means you can handle functions with the same flexibility as any other variable.

Let's revisit the calculator we built in Chapter 3. This time, we will redesign it using functional programming principles to make it more flexible and easier to extend.

First, we will create a single, generic `calculator` function. Instead of performing a specific operation like addition, this function will take an operation as one of its arguments.

```jac
# This function takes two numbers and another function as input.
# The `callable` type annotation indicates that `operation` is expected to be a function.
def calculator(a: float, b: float, operation: callable) -> float {
    return operation(a, b);
}
```
<br />

Next, we can define our basic arithmetic operations as standalone functions.

```jac
# These are the individual operations we can pass to our main calculator function.
def add(a: float, b: float) -> float {
    return a + b;
}

def subtract(a: float, b: float) -> float {
    return a - b;
}

def multiply(a: float, b: float) -> float {
    return a * b;
}
def divide(a: float, b: float) -> float {
    if b == 0 {
        raise ValueError("Cannot divide by zero");
    }
    return a / b;
}
```

Now, we can create a dictionary using `dict` keyword that maps a string (like "add") to the actual function object (like add). This allows us to select an operation dynamically using its name.


```jac
# A global dictionary to map operation names to their corresponding functions.
glob operations: dict[str, callable] = {
    "add": add,
    "subtract": subtract,
    "multiply": multiply,
    "divide": divide
};
```
<br />

Finally, let's put it all together. Our main execution block can now use the calculator function and the operations dictionary to perform calculations dynamically.

``` jac

# Main entry point for the program
with entry {
    a: float = 10.0;
    b: float = 5.0;

    # To test other operations, simply change this string.
    operation_name: str = "add";

    # Check if the requested operation exists in our dictionary.
    if operation_name in operations {
        # Look up the function in the dictionary and pass it to the calculator.
        selected_operation_func = operations[operation_name];
        result: float = calculator(a, b, selected_operation_func);
        print(f"Result of {operation_name}({a}, {b}) = {result}");
    } else {
        print(f"Operation '{operation_name}' is not supported.");
    }
}

```
This design is highly flexible. To add a new operation, like exponentiation, you would simply define a new `power` function and add it to the operations dictionary. You wouldn't need to change the core calculator logic at all. This demonstrates the power of treating functions as first-class data.

<br />


### Lambda Functions
In Jac, a lambda function is a concise, single-line, anonymous function. These are useful for short, specific operations where defining a full function with def would be unnecessarily verbose.

Lambda functions use the syntax lambda `lambda parameters: return_type: expression`. They can be assigned to a variable or used directly as an argument to another function.They are also useful for functional programming patterns like map, filter, and reduce.

For example, a simple add function can be defined as a lambda:

```jac
# This lambda takes two `float` parameters, `a` and `b`, and returns their sum as a `float`. It can be called just like a regular function.
add = lambda x: float, y: float: x + y;
```
<br />


```jac
with entry {
    add = lambda x: float, y: float: x + y;

    a: float = 10.0;
    b: float = 5.0;

    # Using the lambda function
    result: float = add(a, b);
    print(f"Result of add({a}, {b}) = {result}");
}
```
<br />

### Higher-Order Functions
A higher-order function is a function that either takes another function as an argument, returns a function, or both. This is a powerful concept that enables functional programming patterns, promoting code that is abstract, reusable, and composable.

The `callable` type hint is used to specify that a parameter or return value is expected to be a function.

```jac
# Higher-order function that applies operation to list
def apply_operation(numbers: list[float], operation: callable) -> list[float] {
    return [operation(num) for num in numbers];
}

# Function that creates specialized functions
def create_multiplier(factor: float) -> callable[[float], float] {
    return lambda x: float: x * factor;
}

# Function composition
def compose(f: callable, g: callable) -> callable {
    return lambda x: any: f(g(x));
}

with entry {
    print("=== Higher-Order Functions Demo ===");

    numbers = [1.0, 2.0, 3.0, 4.0, 5.0];

    # Create specialized multiplier functions
    triple = create_multiplier(3.0);
    quadruple = create_multiplier(4.0);

    # Apply operations
    tripled = apply_operation(numbers, triple);
    quadrupled = apply_operation(numbers, quadruple);

    print(f"Original: {numbers}");
    print(f"Tripled: {tripled}");
    print(f"Quadrupled: {quadrupled}");
}
```
<br />

### Built-in Higher-Order Functions `map`, `filter`, and `sorted`
Jac supports Python's essential built-in higher-order functions, which are powerful tools for working with lists and other collections without writing explicit loops.

### *filter*

The `filter` function constructs a new iterable from elements of an existing one for which a given function returns True.

Its signature is `filter(function, iterable)`.

Let's revisit our grade-filtering example from Chapter 3. Instead of a list comprehension, we can use filter with a lambda function to define our condition.


```jac
with entry {
    # Raw test scores
    test_scores: list = [78, 85, 92, 69, 88, 95, 72];

    # Get passing grades (70 and above)
    passing_scores: list = [score for score in test_scores if score >= 70];
    print(f"Passing scores: {passing_scores}");
}
```
<br />

The same result can be achieved using the `filter` function along with a lambda function to define the filtering condition.

```jac
with entry {
    test_scores: list[int] = [78, 85, 92, 69, 88, 95, 72];

    # The lambda `lambda score: bool: score >= 70` returns True for passing scores.
    # 'filter' applies this lambda to each item in 'test_scores'.
    passing_scores_iterator = filter(lambda score: float: score >= 70, test_scores);

    # The result of 'filter' is an iterator, so we convert it to a list to see the results.
    passing_scores: list[int] = list(passing_scores_iterator);
    print(f"Passing scores: {passing_scores}");
}
```
<br />


### *map*
The `map` function applies a given function to every item of an iterable and returns an iterator of the results.
Its signature is `map(function, iterable)`. This is ideal for transforming data without writing explicit loops.

```jac
def classify_grade(score: int) -> str {
    if score >= 90 {
        return "A";
    } elif score >= 80 {
        return "B";
    } elif score >= 70 {
        return "C";
    } elif score >= 60 {
        return "D";
    } else {
        return "F";
    }
}

with entry {
    # Raw test scores
    test_scores = [78, 85, 92, 69, 88, 95, 72];

    # Get passing grades (70 and above) using filter
    passing_scores = list(filter(lambda x: float: x >= 70, test_scores));
    print(f"Passing scores: {passing_scores}");

    # Get the grade of passing scores using map
    grades = list(map(classify_grade, passing_scores));
    print(f"Grades: {grades}");
}
```
<br />

### *sorted*
The `sorted` function returns a new sorted list from the items in an iterable. You can customize the sorting logic by providing a function to the `key` parameter.

```jac
with entry {
    # A list of tuples: (student_name, final_score)
    student_records: list[tuple[str, int]] = [("Charlie", 88), ("Alice", 95), ("Bob", 72)];

    # Sort alphabetically by name (the first item in each tuple).
    sorted_by_name = sorted(student_records, key=lambda record: str: record[0]);
    print(f"Sorted by name: {sorted_by_name}");

    # Sort numerically by score (the second item), in descending order.
    sorted_by_score = sorted(student_records, key=lambda record: int: record[1], reverse=True);
    print(f"Sorted by score (desc): {sorted_by_score}");
}
```
<br />



## Decorators for Enhanced Functionality
---
As your programs grow, you'll often need to add cross-cutting functionality—like logging, timing, or caching—to multiple functions. Modifying each function directly would be repetitive and error-prone. Decorators solve this problem by providing a clean way to wrap a function with extra behavior.

A decorator is a function that takes another function as an argument, adds some functionality, and returns a new function.


Consider the following example of a simple decorator that adds pre- and post-processing logic to a function.

The decorator function call `decorator_name` takes a function `func` as an argument and wraps it in a new function `wrapper` that adds additional behavior before and after calling the original function. The decorator returns the `wrapper` function, which is then used to replace the original function when the decorator is applied.

```jac
def decorator_name(func: callable) -> callable {
    def wrapper(*args: any, **kwargs: any) -> any {
        # Pre-processing logic
        result = func(*args, **kwargs);
        # Post-processing logic
        return result;
    }
    return wrapper;
}
```

!!! note
    `*args` is a python contruct that allows a function to accept a variable number of positional arguments, while `**kwargs` allows it to accept a variable number of keyword arguments.


Decorators provide a clean way to add functionality to functions without modifying their core logic. The general syntax for using decorators in Jac is:

```jac
@decorator_name
def function_name(parameters) -> return_type {
    # function body
}
```
<br />


### Decorator Stacking Order
You can apply multiple decorators to a single function. They are applied from the bottom up the decorator closest to the function definition is applied first.

```jac
import time;

def decorator_a(func: callable) -> callable {
    def wrapper(*args: any, **kwargs: any) -> any {
        print("Decorator A Start");
        result = func(*args, **kwargs);
        print("Decorator A End");
        return result;
    }
    return wrapper;
}

def decorator_b(func: callable) -> callable {
    def wrapper(*args: any, **kwargs: any) -> any {
        print("Decorator B Start");
        result = func(*args, **kwargs);
        print("Decorator B End");
        return result;
    }
    return wrapper;
}

# Decorator 'b' is applied first, then 'a' wraps 'b'.
@decorator_a
@decorator_b
def greet(name: str) -> None {
    print(f"Hello, {name}!");
}

with entry {
    greet("Alice");
}
```
<br />

The output will show that decorator B's "start" and "end" messages are nested inside decorator A's messages.

### Parameterized Decorators
For more flexibility, decorators can accept their own parameters. This requires an extra layer of nesting in the decorator function.

```jac
# This outer function takes the decorator's parameter.
def repeat(times: int) -> callable {
    # The second layer is the actual decorator.
    def decorator(func: callable) -> callable {
        # The third layer is the wrapper.
        def wrapper(*args: any, **kwargs: any) -> any {
            result: any;
            for i in range(times) {
                print(f"Execution {i+1} of {times}");
                result = func(*args, **kwargs);
            }
            return result;
        }
        return wrapper;
        }
    return decorator;
}

@repeat(3)
def say_hello(name: str) -> None {
    print(f"Hello, {name}");
}

with entry {
    say_hello("Bob");
}
```
<br />

This will print "Hello, Bob!" three times, as specified by the `@repeat(times=3)` parameter.

### Error Handling in Decorators

Decorators in Jac can handle exceptions, retry operations, and log errors gracefully.

```jac
import time;

def retry_decorator(max_retries: int, delay: float) -> callable {
    def decorator(func: callable) -> callable {
        def wrapper(*args: any, **kwargs: any) -> any {
            attempts: int = 0;
            while attempts < max_retries {
                try {
                    return func(*args, **kwargs);
                } except Exception as e {
                    attempts += 1;
                    print(f"Attempt {attempts} failed: {e}");
                    time.sleep(delay);
                }
            }
            raise Exception("Maximum retries exceeded");
        }
        return wrapper;
    }
    return decorator;
}

@retry_decorator(max_retries=3, delay=1.0)
def risky_operation() -> None {
    import random;
    if random.random() < 0.7 {
        raise ValueError("Random failure");
    }
    print("Operation succeeded!");
}

with entry {
    risky_operation();
}
```
<br />

### Timing Decorator
A timing decorator is a simple way to measure the performance of your functions.

```jac
import time;

# Timing decorator to measure function performance
def timing_decorator(func: callable) -> callable {
    def wrapper(*args: any, **kwargs: any) -> any {
        start_time = time.time();
        result = func(*args, **kwargs);
        end_time = time.time();
        execution_time = end_time - start_time;
        print(f"{func.__name__} executed in {execution_time} seconds");
        return result;
    }
    return wrapper;
}

# Apply timing to our math functions
@timing_decorator
def slow_fibonacci(n: int) -> int {
    if n <= 1 {
        return n;
    }
    return slow_fibonacci(n - 1) + slow_fibonacci(n - 2);
}

@timing_decorator
def slow_factorial(n: int) -> int {
    if n <= 1 {
        return 1;
    }
    return n * slow_factorial(n - 1);
}

with entry {
    print("=== Timing Decorator Demo ===");
    result1 = slow_fibonacci(2);
    print(f"Fibonacci(2) = {result1}");

    result2 = slow_factorial(3);
    print(f"Factorial(3) = {result2}");
}
```
<br />

### Caching (Memoization) Decorator
For functions that perform expensive calculations, a caching decorator can store results and return them instantly on subsequent calls with the same arguments. This technique is known as memoization.

```jac
import time;

# Timing decorator to measure function performance
def timing_decorator(func: callable) -> callable {
    def wrapper(*args: any, **kwargs: any) -> any {
        start_time = time.time();
        result = func(*args, **kwargs);
        end_time = time.time();
        execution_time = end_time - start_time;
        print(f"{func.__name__} executed in {execution_time} seconds");
        return result;
    }
    return wrapper;
}

# Caching decorator for expensive computations
def cache_decorator(func: callable) -> callable {
    cache: dict[str, any] = {};

    def wrapper(*args: any) -> any {
        # Create a simple cache key from arguments
        cache_key = str(args);

        if cache_key in cache {
            print(f"Cache hit for {func.__name__}{args}");
            return cache[cache_key];
        }

        print(f"Computing {func.__name__}{args}");
        result = func(*args);
        cache[cache_key] = result;
        return result;
    }
    return wrapper;
}

# Combine timing and caching decorators
@timing_decorator
@cache_decorator
def optimized_fibonacci(n: int) -> int {
    if n <= 1 {
        return n;
    }
    return optimized_fibonacci(n - 1) + optimized_fibonacci(n - 2);
}

@timing_decorator
@cache_decorator
def expensive_calculation(n: int) -> int {
    # Simulate expensive computation
    result = 0;
    for i in range(n * 1000) {
        result += i;
    }
    return result;
}

with entry {
    print("=== Cached Functions Demo ===");

    # First call - computed and cached
    result1 = optimized_fibonacci(3);
    print(f"Fibonacci(3) = {result1}");

    # Second call - retrieved from cache
    result2 = optimized_fibonacci(3);
    print(f"Fibonacci(3) again = {result2}");

    # Expensive calculation test
    result3 = expensive_calculation(10);
    print(f"Expensive calculation result: {result3}");

    # Second call to expensive calculation
    result4 = expensive_calculation(10);
    print(f"Expensive calculation again: {result4}");
}
```
<br />


## Async Functions
---
Some tasks, like network requests or reading large files, are I/O-bound. This means your program spends most of its time waiting for an external resource. During this waiting time, a standard program sits idle.

Jac's support for async functions allows your program to perform other work while it waits, leading to significant performance improvements for I/O-bound applications. This is known as concurrency.

- `async def`: Marks a function as a "coroutine"—a special function that can be paused and resumed.
- `await`: Pauses the execution of the current coroutine, allowing the program to work on other tasks until the awaited operation (e.g., a network call) is complete.

### Basic Async Functions

```jac
import asyncio;
import time;

# Async function for simulated API calls
async def fetch_data(source: str, delay: float) -> dict[str, any] {
    print(f"Starting to fetch from {source}...");
    await asyncio.sleep(delay);  # Simulate network delay

    return {
        "source": source,
        "data": f"Data from {source}",
        "timestamp": time.time()
    };
}

# Async function that processes multiple sources
async def gather_all_data() -> list[dict[str, any]] {
    # Run multiple async operations concurrently
    tasks = [
        fetch_data("API-1", 1.0),
        fetch_data("API-2", 0.5),
        fetch_data("API-3", 1.5)
    ];

    results = await asyncio.gather(*tasks);
    return results;
}

# Regular function that uses async
def run_async_example() -> None {
    print("=== Async Functions Demo ===");

    # Run the async function
    results = asyncio.run(gather_all_data());

    print("All data fetched:");
    for result in results {
        print(f"  {result['source']}: {result['data']}");
    }
}

with entry {
    run_async_example();
}
```
<br />

## Best Practices
---
- **Use descriptive names**: Function names should clearly indicate their purpose
- **Keep functions focused**: Each function should have a single, well-defined responsibility
- **Handle errors gracefully**: Use appropriate return types and exception handling
- **Leverage decorators**: Use decorators for cross-cutting concerns like timing and caching
- **Document with types**: Let type annotations serve as documentation
- **Consider async**: Use async functions for I/O-bound operations

## Wrapping Up
---

In this chapter, we looked at higher order functions, decorators, and async functions in Jac. We explored how to use these features to create flexible, reusable code that can handle complex operations efficiently.


*Ready to explore advanced AI operations? Continue to [Chapter 5: Advanced AI Operations](chapter_5.md)!*

# Chapter 5: Advanced AI Operations
---
In this chapter, you will learn to integrate advanced AI capabilities directly into your Jac applications using the byLLM (Meaning-Typed LLM) framework. We will build a multi-modal image analysis tool to demonstrate how Jac simplifies complex AI operations, including model configuration, context enhancement, and multi-modal data handling.


## byLLM Overview
---
byLLM (Meaning Typed LLM) is Jac's native AI integration framework. It transforms the way developers interact with Large Language Models by shifting from manual prompt engineering and complex API calls to a streamlined, function-based approach that is fully integrated with Jac's type system.


!!! success "byLLM Benefits"
    - **Zero Prompt Engineering**: Define function signatures, let AI handle implementation
    - **Type Safety**: AI functions integrate with Jac's type system
    - **Model Flexibility**: Switch between different AI models easily
    - **Multimodal Support**: Handle text, images, and audio seamlessly
    - **Built-in Optimization**: Automatic prompt optimization and caching

## Functions as Prompts
---
Up until this point, we've used Jac's functions to define behavior. However, what if we wanted to incorperate AI capabilities directly into our Jac applications? For example, lets say we're writing a poetry application that can generate poems based on a user supplied topic.

Since Jac is a super set of Python, we can create a function `write_poetry` that takes a topic as input and then make a call to an OpenAI model using its python or langchain library to generate the poem.

First, install the OpenAI Python package:
```bash
pip install openai
```
<br />
then set your OpenAI API key as an environment variable:

```bash
export OPENAI_API_KEY="your-api-key"
```
<br />
Now we can write our Jac code to integrate with OpenAI's API:

```jac
import from openai { OpenAI }

glob client = OpenAI();

""" Write a poem about topic """
def write_poetry(topic: str) -> str {
    response = client.responses.create(
        model="gpt-4.1-mini",
        input=f"Write a poem about {topic}."
    );
    return response.output_text;
}

with entry {
    poem = write_poetry("A serene landscape with mountains.");
    print(poem);
}
```
<br />

Finally, lets generate our poetic masterpiece by running the Jac code:
```console
$ jac run poetry.jac
Amidst the quiet, mountains rise,
Their peaks adorned with endless skies.
A tranquil breeze, a gentle stream,
Within this landscape, like a dream.

Soft whispers of the morning light,
Embrace the earth in pure delight.
A serene world, where hearts find peace,
In nature's hold, all worries cease.
```
<br />

Very nice! However, this approach requires manual API management (what if we want to switch to a different AI provider?), and we still have to write the prompt ourselves. Wouldn't it be great if we could just define the function signature and let the AI handle the rest? *Imagine a world where the function was the prompt?* Where we could simply declare a function and the AI would understand what to do? That's the power of byLLM.

Let's see how this works.

First we'll need to install the byLLM package:
```bash
pip install byllm
```
<br />
Next we replace the OpenAI import with that of the byLLM package

```jac
import from byllm { Model }
glob llm = Model(model_name="gpt-4.1-mini");
```
<br />
Instead of writing the function ourselves, we simply declare the function signature and use the `by` keyword to indicate that this function should be handled by the AI model referenced by `llm()`. The byLLM framework will automatically generate the appropriate prompt based on the function signature.
```jac
def write_poetry(topic: str) -> str by llm();
```

Finally, lets put it all together and run the Jac code:
```jac
# mt_poem.jac - Simple AI integration
import from byllm { Model }

glob llm = Model(model_name="gpt-4.1-mini");

""" Write a poem about topic """
def write_poetry(topic: str) -> str by llm();

with entry {
    poem = write_poetry("A serene landscape with mountains.");
    print(poem);
}
```
<br />


```console
$ jac run mt_poem.jac
Beneath the sky so vast and grand,
Mountains rise like ancient bands,
Whispers soft in tranquil air,
A serene landscape, calm and fair.

Colors blend in gentle hues,
Nature's brush with peaceful views,
Rivers sing and breezes dance,
In this quiet, soul’s expanse.
```
<br />

### Simple Image Captioning Tool
To further illustrate byLLM's capabilities, let's build a simple image captioning tool. This tool will analyze an image and generate a descriptive caption using an AI model.

First lets grab an image from upsplash to work with. You can use any image you like, but for this example, we'll use a photo of a french bulldog. Download the image and save it as `photo.jpg` in the same directory as your Jac code.

![Image Captioning Example](../assets/photo.jpg){ width=300px }
/// caption
Photo by <a href="https://unsplash.com/@karsten116?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Karsten Winegeart</a> on <a href="https://unsplash.com/photos/a-french-bulldog-in-a-hoodie-and-gold-chain-GkpLfCRooCA?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>
///

Next we'll make use of MLTLLM's `Image` function to handle image inputs. This function allows us to pass images directly to the AI model for analysis. We'll use OpenAI's `gpt-4o-mini` model for this task.

```jac
# image_captioning.jac - Simple Image Captioning Tool
import from byllm { Model, Image }

glob llm = Model(model_name="gpt-4o-mini");

"""Generate a detailed caption for the given image."""
def caption_image(image: Image) -> str by llm();

with entry {
    caption = caption_image(Image("photo.jpg"));
    print(caption);
}
```
<br />
Now we can run our Jac code to generate a caption for the image:
```console
$ jac run image_captioning.jac

A stylish French Bulldog poses confidently against a vibrant yellow backdrop,
showcasing its trendy black and yellow hoodie emblazoned with "WOOF." The pup's
playful demeanor is accentuated by a shiny gold chain draped around its neck,
adding a touch of flair to its outfit. With its adorable large ears perked up
and tongue playfully sticking out, this fashion-forward canine is ready to steal
the spotlight and capture hearts with its charm and personality.
```
<br />

## Model Declaration and Configuration
---
byLLM supports various AI models through the unified `Model` interface. For example, you can load multiple models like OpenAI's GPT-4, Google's Gemini, or any other compatible model in the same way. This allows you to switch between models easily without changing your code structure.

```jac
# basic_setup.jac
import from byllm { Model, Image }

# Configure different models
glob text_model = Model(model_name="gpt-4o");
glob vision_model = Model(model_name="gpt-4-vision-preview");
glob gemini_model = Model(model_name="gemini-2.0-flash");
```
<br />

### Model Configuration Options
The `Model` class allows you to configure various parameters for your AI model, such as temperature, max tokens, and more. Here's an example of how to set up a model with custom parameters:

```jac
import from byllm { Model, Image }

# Configure model with custom parameters
glob creative_model = Model(
    model_name="gpt-4.1-mini",
    temperature=0.9,  # More creative
    max_tokens=500,
    verbose=True      # Show prompts for debugging
);
```
<br />
Below is a breakdown of the parameters you can configure when creating a `Model` instance:

| Parameter         | Purpose / Description                                                                                      | Default Value / Example                     |
|-------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------|
| `model`           | Specifies the name of the language model to use (e.g., "gpt-3.5-turbo", "claude-3-sonnet-20240229").      | Required (set during initialization)        |
| `api_base`        | Sets the base URL for the API endpoint. Used to override the default endpoint for the model provider.      | Optional                                   |
| `messages`        | List of formatted message objects (system/user/assistant) for the LLM prompt.                             | Required (built from function call context) |
| `tools`           | List of tool definitions for function/tool calls the LLM can invoke.                                       | Optional                                   |
| `response_format` | Specifies the output schema or format expected from the model (e.g., JSON schema, plain text).             | Optional                                   |
| `temperature`     | Controls randomness/creativity of the model output (higher = more random).                                 | 0.7 (if not explicitly set)                 |
| `max_tokens`      | Maximum number of tokens in the generated response. (Commented out; can be enabled)                        | 100 (if enabled and not set)                |
| `top_k`           | Limits sampling to the top K probable tokens. (Commented out; can be enabled)                              | 50 (if enabled and not set)                 |
| `top_p`           | Controls token selection by probability sum (nucleus sampling). (Commented out; can be enabled)            | 0.9 (if enabled and not set)                |

Here we have a simple example of how to use the `Model` class to create a model instance with custom parameters:
```jac
# model_config.jac
import from byllm { Model, Image }

# Configure model with custom parameters
glob creative_model = Model(
    model_name="gpt-4.1-mini",
    temperature=0.9,  # More creative
    max_tokens=500,
    verbose=True      # Show prompts for debugging
);

glob precise_model = Model(
    model_name="gpt-4.1-mini",
    temperature=0.1,  # More deterministic
    max_tokens=200
);

"""Generate creative story from image."""
def create_story(image: Image) -> str by creative_model();

"""Extract factual information from image."""
def extract_facts(image: Image) -> str by precise_model();


with entry {
    # Example usage
    story = create_story(Image("photo.jpg"));
    print("Creative Story:", story);

    facts = extract_facts(Image("photo.jpg"));
    print("Extracted Facts:", facts);
}
```
<br />



## Updating the Image Captioning Tool
---
Let's progressively build an image captioning tool that demonstrates byLLM's capabilities.

```jac
# image_captioner.jac
import from byllm { Model, Image }

glob vision_llm = Model(model_name="gpt-4o-mini");

obj ImageCaptioner {
    has name: str;

    """Generate a brief, descriptive caption for the image."""
    def generate_caption(image: Image) -> str by vision_llm();

    """Extract specific objects visible in the image."""
    def identify_objects(image: Image) -> list[str] by vision_llm();

    """Determine the mood or atmosphere of the image."""
    def analyze_mood(image: Image) -> str by vision_llm();
}

with entry {
    captioner = ImageCaptioner(name="AI Photo Assistant");
    image = Image("photo.jpg");

    # Generate basic caption
    caption = captioner.generate_caption(image);
    print(f"Caption: {caption}");

    # Identify objects
    objects = captioner.identify_objects(image);
    print(f"Objects found: {objects}");

    # Analyze mood
    mood = captioner.analyze_mood(image);
    print(f"Mood: {mood}");
}
```
<br />

```console
$ jac run image_captioner.jac

Caption: A stylish French Bulldog poses confidently in a black and yellow "WOOF" sweatshirt,
accessorized with a chunky gold chain against a vibrant yellow backdrop.

Objects found: ['dog', 'sweater', 'chain', 'yellow background']

Mood: The mood of the image is playful and cheerful. The bright yellow background and
the stylish outfit of the dog contribute to a fun and lighthearted atmosphere.
```
<br />

## Semantic Strings and Context Enhancement
---
**Semantic strings** provide additional context to AI functions via the `sem` keyword, allowing for more nuanced understanding without cluttering your code. They're particularly useful for domain-specific applications.

```jac
# enhanced_captioner.jac
import from byllm { Model, Image }

glob vision_llm = Model(model_name="gpt-4.1-mini");

obj PhotoAnalyzer {
    has photographer_name: str;
    has style_preference: str;
    has image: Image;
}

# Add semantic context for better AI understanding
sem PhotoAnalyzer = "Professional photo analysis tool for photographers";
sem PhotoAnalyzer.photographer_name = "Name of the photographer for personalized analysis";
sem PhotoAnalyzer.style_preference = "Preferred photography style (artistic, documentary, commercial)";


"""Generate caption considering photographer's style preference."""
def generate_styled_caption(pa: PhotoAnalyzer) -> str by vision_llm();

"""Provide technical photography feedback."""
def analyze_composition(pa: PhotoAnalyzer) -> list[str] by vision_llm();

"""Suggest improvements for the photo."""
def suggest_improvements(pa: PhotoAnalyzer) -> list[str] by vision_llm();


with entry {
    analyzer = PhotoAnalyzer(
        photographer_name="Alice",
        style_preference="artistic",
        image=Image("photo.jpg")
    );

    # Generate styled caption
    caption = generate_styled_caption(analyzer);
    print(f"Styled caption: {caption}");

    # Analyze composition
    composition = analyze_composition(analyzer);
    print(f"Composition analysis: {composition}");

    # Get improvement suggestions
    suggestions = suggest_improvements(analyzer);
    print(f"Suggestions: {suggestions}");
}
```
<br />

## Testing and Error Handling
---
AI applications require robust error handling and testing strategies.

### Robust AI Integration

```jac
# robust_ai.jac
import from byllm { Model, Image }

glob reliable_llm = Model(model_name="gpt-4o", max_tries=3);

obj RobustCaptioner {
    has fallback_enabled: bool = True;

    """Generate caption with error handling."""
    def safe_caption(image_path: str) -> dict {
        try {
            caption = self.generate_caption_ai(image_path);
            return {
                "success": True,
                "caption": caption,
                "source": "ai"
            };
        } except Exception as e {
            if self.fallback_enabled {
                fallback_caption = f"Image analysis unavailable for {image_path}";
                return {
                    "success": False,
                    "caption": fallback_caption,
                    "source": "fallback",
                    "error": str(e)
                };
            } else {
                raise e;
            }
        }
    }

    """AI-powered caption generation."""
    def generate_caption_ai(image_path: str) -> str by reliable_llm();

    """Validate generated content."""
    def validate_caption(caption: str) -> bool {
        # Basic validation rules
        if len(caption) < 10 {
            return False;
        }
        if "error" in caption.lower() {
            return False;
        }
        return True;
    }
}

with entry {
    captioner = RobustCaptioner(fallback_enabled=True);

    # Test with different scenarios
    test_images = [
        Image("valid_photo.jpg"),
        Image("corrupted.jpg"),
        Image("missing.jpg")
    ];

    for image in test_images {
        result = captioner.safe_caption(image);

        if result["success"] {
            is_valid = captioner.validate_caption(result["caption"]);
            print(f"{image}: {result['caption']} (Valid: {is_valid})");
        } else {
            print(f"{image}: Failed - {result['error']}");
        }
    }
}
```
<br />

## Best Practices
---
!!! tip "AI Development Guidelines"
    - **Start Simple**: Begin with basic AI functions, add complexity gradually
    - **Use Semantic Strings**: Provide context without cluttering function signatures
    - **Handle Failures**: Always implement fallback strategies for AI functions
    - **Test Thoroughly**: AI outputs can be unpredictable, test with various inputs
    - **Optimize Models**: Choose appropriate models and parameters for your use case

## Key Takeaways

!!! summary "What We've Learned"
    **byLLM Integration:**

    - **Simple AI Functions**: Define AI capabilities with `by llm()` syntax
    - **Model Configuration**: Flexible model selection and parameter tuning
    - **Type Safety**: AI functions integrate seamlessly with Jac's type system
    - **Zero Prompt Engineering**: Function signatures become prompts automatically

    **Advanced Features:**

    - **Semantic Context**: Enhanced AI understanding through semantic strings
    - **Multimodal Support**: Seamless handling of images, text, and audio
    - **Error Handling**: Robust patterns for production AI applications
    - **Model Selection**: Easy switching between different AI providers and models

    **Practical Applications:**

    - **Content Generation**: Automated text and media analysis
    - **Data Processing**: Intelligent data transformation and extraction
    - **API Integration**: Direct AI integration without complex setup
    - **Scalable Architecture**: AI functions work in both local and cloud deployments

!!! tip "Try It Yourself"
    Enhance the image captioning tool by adding:
    - Batch processing for multiple images
    - Integration with cloud storage services
    - Custom model fine-tuning capabilities
    - Real-time image analysis from camera feeds

    Remember: AI functions in Jac are as easy to use as regular functions, but with the power of Large Language Models!

---

If you are interested in learning more about byLLM, check out [ Quickstart to byLLM ](../learn/jac-byllm/with_llm.md)

*Ready to learn about imports and modular programming? Continue to [Chapter 6: Imports System and File Operations](chapter_6.md)!*

# Chapter 6: Imports System and File Operations

As your projects grow beyond a single file, keeping your code organized becomes essential for maintainability and collaboration. A well-structured project is easier to understand, test, and scale. This chapter introduces Jac's module system for organizing code across multiple files and its familiar approach to file operations.

You will learn how to import your own code, leverage the vast Python ecosystem, and use Jac's powerful interface-implementation pattern to build clean, robust applications.

!!! topic "Module Organization Philosophy"
    Jac's import system is your primary tool for structuring code. It allows you to define objects, nodes, walkers, and functions in separate files and use them wherever they are needed. Jac seamlessly integrates with the Python ecosystem, allowing you to import and use Python libraries directly.

## Import Statements and Module Organization

### Basic Import Patterns

!!! example "Basic Import Statements"
    === "Jac"
        <div class="code-block">
        ```jac
        # Import Python modules
        import os;
        import json;
        import sys;

        # Import specific functions from Python modules
        import from datetime  {datetime}
        import from pathlib {Path}

        # Import Jac modules
        # include my_module;
        # include utils.file_helper;

        with entry {
            # Use imported modules
            current_time = datetime.now();
            current_dir = os.getcwd();
            print(f"Current time: {current_time}");
            print(f"Current directory: {current_dir}");
        }
        ```
        </div>
    === "Python"
        ```python
        # Import Python modules
        import os
        import json
        import sys

        # Import specific functions
        from datetime import datetime
        from pathlib import Path

        # Import local modules
        import my_module
        from utils import file_helper

        if __name__ == "__main__":
            # Use imported modules
            current_time = datetime.now()
            current_dir = os.getcwd()
            print(f"Current time: {current_time}")
            print(f"Current directory: {current_dir}")
        ```

### Implementation Separation

Jac encourages a clean architectural pattern that separates what a component does from how it does it. This is achieved by splitting an object's or node's definition (its interface) from its method logic (its implementation).

The interface is defined in a `.jac` file, while the implementation is placed in a corresponding .`impl.jac` file. When you import the object, Jac automatically links them together.

!!! topic "Benefits of Separation"
    This pattern makes your code significantly more maintainable. You can change the internal logic of a method in the `.impl.jac` file without ever touching the files that use the object. It also makes testing easier, as you can mock implementations while testing against a stable interface.

!!! example "Interface and Implementation Separation"
    === "math_ops.jac"

        ```jac
        # Interface definition
        obj Calculator {
            has precision: int = 2;

            def add(a: float, b: float) -> float;
            def subtract(a: float, b: float) -> float;
            def multiply(a: float, b: float) -> float;
            def divide(a: float, b: float) -> float;
        }
        ```

    === "math_ops.impl.jac"
        <div class="code-block">
        ```jac
        # Implementation file
        impl Calculator.add {
            result = a + b;
            return round(result, self.precision);
        }

        impl Calculator.subtract {
            result = a - b;
            return round(result, self.precision);
        }

        impl Calculator.multiply {
            result = a * b;
            return round(result, self.precision);
        }

        impl Calculator.divide {
            if b == 0.0 {
                raise ValueError("Division by zero");
            }
            result = a / b;
            return round(result, self.precision);
        }
        ```
        </div>
    === "Python Equivalent"
        ```python
        # Python class definition
        class Calculator:
            def __init__(self, precision: int = 2):
                self.precision = precision

            def add(self, a: float, b: float) -> float:
                result = a + b
                return round(result, self.precision)

            def subtract(self, a: float, b: float) -> float:
                result = a - b
                return round(result, self.precision)

            def multiply(self, a: float, b: float) -> float:
                result = a * b
                return round(result, self.precision)

            def divide(self, a: float, b: float) -> float:
                if b == 0.0:
                    raise ValueError("Division by zero")
                result = a / b
                return round(result, self.precision)
        ```
### Namespace Injection
!!! topic "Namespace Injection"
    Jac provides several mechanisms to manage namespaces clearly and effectively:

    * **import**: Loads an entire Python module or package, preserving its namespace.

    ```jac
    import os;
    os.getcwd();
    ```

    * **include**: Imports all exported symbols from a Jac module directly into the current namespace, flattening it and simplifying access.

    ```jac
    include my_utils;
    utility_function();
    ```

    * **import from**: Explicitly imports selected symbols from a module, improving clarity and avoiding namespace pollution.

    ```jac
    import from datetime {datetime};
    now = datetime.now();
    ```

    * **Aliasing**: Allows renaming imported modules or symbols, helping avoid naming conflicts.

    ```jac
    import json as js;
    data = js.load(file);
    ```

### Jac Import Internals
!!! topic "Import Resolution Workflow"
    Jac resolves imports using a structured process:

    * Parses import statements to determine modules.
    * Searches for modules in the caller directory, `JAC_PATH`, and Python's `sys.path`.
    * Compiles `.jac` files to bytecode (`.jir`) if necessary.
    * Executes bytecode to populate module namespaces.
    * Caches modules to improve performance.

    Common issues include missing bytecode, syntax errors, and circular dependencies.

## File Operations and External Integration

!!! topic "File Handling"
    File operations are essential for configuration management, data processing, and system integration.

### Basic File Operations

!!! example "File Reading and Writing"
    === "Jac"
        <div class="code-block">
        ```jac
        import os;
        import json;

        # Read text file safely
        def read_file(filepath: str) -> str | None {
            try {
                with open(filepath, 'r') as file {
                    return file.read();
                }
            } except FileNotFoundError {
                print(f"File not found: {filepath}");
                return None;
            } except Exception as e {
                print(f"Error reading file: {e}");
                return None;
            }
        }

        # Write text file safely
        def write_file(filepath: str, content: str) -> bool {
            try {
                with open(filepath, 'w') as file {
                    file.write(content);
                }
                return True;
            } except Exception as e {
                print(f"Error writing file: {e}");
                return False;
            }
        }

        # Read JSON file
        def read_json(filepath: str) -> dict | None {
            try {
                with open(filepath, 'r') as file {
                    return json.load(file);
                }
            } except FileNotFoundError {
                print(f"JSON file not found: {filepath}");
                return None;
            } except json.JSONDecodeError {
                print(f"Invalid JSON in file: {filepath}");
                return None;
            }
        }

        with entry {
            # Test file operations
            test_content = "Hello from Jac!";
            if write_file("test.txt", test_content) {
                content = read_file("test.txt");
                print(f"File content: {content}");
            }
        }
        ```
        </div>
    === "Python"
        ```python
        import os
        import json
        from typing import Optional

        # Read text file safely
        def read_file(filepath: str) -> Optional[str]:
            try:
                with open(filepath, 'r') as file:
                    return file.read()
            except FileNotFoundError:
                print(f"File not found: {filepath}")
                return None
            except Exception as e:
                print(f"Error reading file: {e}")
                return None

        # Write text file safely
        def write_file(filepath: str, content: str) -> bool:
            try:
                with open(filepath, 'w') as file:
                    file.write(content)
                return True
            except Exception as e:
                print(f"Error writing file: {e}")
                return False

        # Read JSON file
        def read_json(filepath: str) -> Optional[dict]:
            try:
                with open(filepath, 'r') as file:
                    return json.load(file)
            except FileNotFoundError:
                print(f"JSON file not found: {filepath}")
                return None
            except json.JSONDecodeError:
                print(f"Invalid JSON in file: {filepath}")
                return None

        if __name__ == "__main__":
            # Test file operations
            test_content = "Hello from Python!"
            if write_file("test.txt", test_content):
                content = read_file("test.txt")
                print(f"File content: {content}")
        ```

## Complete Example: Configuration Management System

!!! topic "Multi-Module Application"
    This example demonstrates how to build a configuration system using multiple modules working together.

### Configuration Reader Module

!!! example "Configuration Reader (config_reader.jac)"
    === "Jac"

        ```jac
        # config_reader.jac
        import json;
        import os;
        import from pathlib { Path }

        obj ConfigReader {
            has config_file: str;
            has config_data: dict[str, any] = {};

            def load_config() -> bool;
            def get_value(key: str, default: any = None) -> any;
            def set_value(key: str, value: any) -> None;
            def save_config() -> bool;
            def create_default_config() -> None;
        }

        impl ConfigReader.load_config {
            if not os.path.exists(self.config_file) {
                print(f"Config file {self.config_file} not found, creating default");
                self.create_default_config();
                return True;
            }

            try {
                with open(self.config_file, 'r') as file {
                    self.config_data = json.load(file);
                }
                print(f"Config loaded from {self.config_file}");
                return True;
            } except json.JSONDecodeError {
                print(f"Invalid JSON in {self.config_file}");
                return False;
            } except Exception as e {
                print(f"Error loading config: {e}");
                return False;
            }
        }

        impl ConfigReader.get_value {
            return self.config_data.get(key, default);
        }

        impl ConfigReader.set_value {
            self.config_data[key] = value;
        }

        impl ConfigReader.save_config {
            try {
                with open(self.config_file, 'w') as file {
                    json.dump(self.config_data, file, indent=2);
                }
                print(f"Config saved to {self.config_file}");
                return True;
            } except Exception as e {
                print(f"Error saving config: {e}");
                return False;
            }
        }

        impl ConfigReader.create_default_config {
            self.config_data = {
                "app_name": "My Jac App",
                "version": "1.0.0",
                "debug": False,
                "database": {
                    "host": "localhost",
                    "port": 5432,
                    "name": "myapp_db"
                },
                "logging": {
                    "level": "INFO",
                    "file": "app.log"
                }
            };
            self.save_config();
        }
        ```

    === "Python"
        ```python
        # config_reader.py
        import json
        import os
        from pathlib import Path
        from typing import Any, Dict, Optional

        class ConfigReader:
            def __init__(self, config_file: str):
                self.config_file = config_file
                self.config_data: Dict[str, Any] = {}

            def load_config(self) -> bool:
                if not os.path.exists(self.config_file):
                    print(f"Config file {self.config_file} not found, creating default")
                    self.create_default_config()
                    return True

                try:
                    with open(self.config_file, 'r') as file:
                        self.config_data = json.load(file)
                    print(f"Config loaded from {self.config_file}")
                    return True
                except json.JSONDecodeError:
                    print(f"Invalid JSON in {self.config_file}")
                    return False
                except Exception as e:
                    print(f"Error loading config: {e}")
                    return False

            def get_value(self, key: str, default: Any = None) -> Any:
                return self.config_data.get(key, default)

            def set_value(self, key: str, value: Any) -> None:
                self.config_data[key] = value

            def save_config(self) -> bool:
                try:
                    with open(self.config_file, 'w') as file:
                        json.dump(self.config_data, file, indent=2)
                    print(f"Config saved to {self.config_file}")
                    return True
                except Exception as e:
                    print(f"Error saving config: {e}")
                    return False

            def create_default_config(self) -> None:
                self.config_data = {
                    "app_name": "My Python App",
                    "version": "1.0.0",
                    "debug": False,
                    "database": {
                        "host": "localhost",
                        "port": 5432,
                        "name": "myapp_db"
                    },
                    "logging": {
                        "level": "INFO",
                        "file": "app.log"
                    }
                }
                self.save_config()
        ```

### Application Module

!!! example "Application Module (app.jac)"
    === "Jac"

        ```jac
        # app.jac
        # include config_reader;
        import logging;

        obj Application {
            has config: ConfigReader;
            has logger: any;

            def start() -> None;
            def setup_logging() -> None;
            def get_database_config() -> dict[str, any];
            def run_debug_mode() -> None;
            def run_normal_mode() -> None;
        }

        impl Application.start {
            print("=== Starting Application ===");

            # Load configuration
            if self.config.load_config() {
                self.setup_logging();

                # Display app info
                app_name = self.config.get_value("app_name", "Unknown App");
                version = self.config.get_value("version", "1.0.0");
                debug_mode = self.config.get_value("debug", False);

                print(f"App: {app_name} v{version}");
                print(f"Debug mode: {debug_mode}");

                # Show database config
                db_config = self.get_database_config();
                print(f"Database: {db_config['host']}:{db_config['port']}/{db_config['name']}");

                if debug_mode {
                    self.run_debug_mode();
                } else {
                    self.run_normal_mode();
                }
            } else {
                print("Failed to load configuration");
            }
        }

        impl Application.setup_logging {
            log_config = self.config.get_value("logging", {});
            log_level = log_config.get("level", "INFO");
            log_file = log_config.get("file", "app.log");

            logging.basicConfig(
                level=getattr(logging, log_level),
                format='%(asctime)s - %(levelname)s - %(message)s',
                handlers=[
                    logging.FileHandler(log_file),
                    logging.StreamHandler()
                ]
            );

            self.logger = logging.getLogger("app");
            self.logger.info("Logging configured");
        }

        impl Application.get_database_config {
            default_db = {"host": "localhost", "port": 5432, "name": "default_db"};
            return self.config.get_value("database", default_db);
        }

        impl Application.run_debug_mode {
            print(">>> Running in DEBUG mode");
            print(f">>> Full config: {self.config.config_data}");
        }

        impl Application.run_normal_mode {
            print(">>> Running in NORMAL mode");
            print(">>> Application ready");
        }
        ```

    === "Python"
        ```python
        # app.py
        from config_reader import ConfigReader
        import logging
        from typing import Dict, Any

        class Application:
            def __init__(self, config_file: str):
                self.config = ConfigReader(config_file)
                self.logger = None

            def start(self) -> None:
                print("=== Starting Application ===")

                # Load configuration
                if self.config.load_config():
                    self.setup_logging()

                    # Display app info
                    app_name = self.config.get_value("app_name", "Unknown App")
                    version = self.config.get_value("version", "1.0.0")
                    debug_mode = self.config.get_value("debug", False)

                    print(f"App: {app_name} v{version}")
                    print(f"Debug mode: {debug_mode}")

                    # Show database config
                    db_config = self.get_database_config()
                    print(f"Database: {db_config['host']}:{db_config['port']}/{db_config['name']}")

                    if debug_mode:
                        self.run_debug_mode()
                    else:
                        self.run_normal_mode()
                else:
                    print("Failed to load configuration")

            def setup_logging(self) -> None:
                log_config = self.config.get_value("logging", {})
                log_level = log_config.get("level", "INFO")
                log_file = log_config.get("file", "app.log")

                logging.basicConfig(
                    level=getattr(logging, log_level),
                    format='%(asctime)s - %(levelname)s - %(message)s',
                    handlers=[
                        logging.FileHandler(log_file),
                        logging.StreamHandler()
                    ]
                )

                self.logger = logging.getLogger("app")
                self.logger.info("Logging configured")

            def get_database_config(self) -> Dict[str, Any]:
                default_db = {"host": "localhost", "port": 5432, "name": "default_db"}
                return self.config.get_value("database", default_db)

            def run_debug_mode(self) -> None:
                print(">>> Running in DEBUG mode")
                print(f">>> Full config: {self.config.config_data}")

            def run_normal_mode(self) -> None:
                print(">>> Running in NORMAL mode")
                print(">>> Application ready")
        ```

### Main Application Entry Point

!!! example "Main Entry Point (main.jac)"
    === "Jac"

        ```jac
        # main.jac
        include app;

        with entry {
            print("=== Configuration Management Demo ===");

            # Create and run application
            application = Application(config=ConfigReader(config_file="app_config.json"));
            application.start();

            print("\n=== Configuration Update Demo ===");

            # Update configuration at runtime
            application.config.set_value("debug", True);
            application.config.set_value("app_name", "Updated Jac App");
            application.config.save_config();

            # Restart with new config
            print("\nRestarting with updated configuration:");
            application.start();
        }
        ```

    === "Python"
        ```python
        # main.py
        from app import Application

        if __name__ == "__main__":
            print("=== Configuration Management Demo ===")

            # Create and run application
            application = Application("app_config.json")
            application.start()

            print("\n=== Configuration Update Demo ===")

            # Update configuration at runtime
            application.config.set_value("debug", True)
            application.config.set_value("app_name", "Updated Python App")
            application.config.save_config()

            # Restart with new config
            print("\nRestarting with updated configuration:")
            application.start()
        ```

## Package Structure and Organization

!!! topic "Project Structure"
    Well-organized project structure makes your code maintainable and scalable.

!!! example "Recommended Project Structure"
    === "Jac Project Structure"
        ```
        my_jac_project/
        ├── main.jac                 # Main entry point
        ├── app.jac                  # Application logic
        ├── app.test.jac             # App tests
        ├── config_reader.jac        # Config management
        ├── config_reader.impl.jac   # Config implementation
        ├── config_reader.test.jac   # Config tests
        ├── utils/
        │   ├── file_utils.jac       # File utilities
        │   └── data_utils.jac       # Data processing
        ├── models/
        │   ├── user.jac             # User model
        │   └── user.impl.jac        # User implementation
        ├── docs/
        │   └── README.md            # Documentation
        └── config/
            └── app_config.json      # Configuration files
        ```
    === "Python Project Structure"
        ```
        my_python_project/
        ├── main.py                  # Main entry point
        ├── app.py                   # Application logic
        ├── config_reader.py         # Config management
        ├── utils/
        │   ├── __init__.py
        │   ├── file_utils.py        # File utilities
        │   └── data_utils.py        # Data processing
        ├── models/
        │   ├── __init__.py
        │   └── user.py              # User model
        ├── tests/
        │   ├── __init__.py
        │   ├── test_config.py       # Config tests
        │   └── test_app.py          # App tests
        ├── docs/
        │   └── README.md            # Documentation
        └── config/
            └── app_config.json      # Configuration files
        ```

## Best Practices

!!! summary "Import and File Operation Best Practices"
    - **Organize by functionality**: Group related code into logical modules
    - **Use explicit imports**: Import only what you need for clarity
    - **Handle errors gracefully**: Always use try-catch for file operations
    - **Separate interface from implementation**: Use `.impl.jac` files for complex objects
    - **Validate file inputs**: Check file existence and format before processing
    - **Use configuration files**: Externalize settings for flexibility
    - **Document your modules**: Clear documentation helps team collaboration

## Key Takeaways

!!! summary "What We've Learned"
    **Import System:**

    - **Python integration**: Seamless access to Python modules and libraries
    - **Namespace management**: Clear control over imported symbols and namespaces
    - **Aliasing support**: Rename imports to avoid conflicts and improve readability
    - **Selective imports**: Import specific functions and classes for better organization

    **Module Organization:**

    - **Implementation separation**: `.impl.jac` files promote clean architecture
    - **Interface definitions**: Clear separation between public interfaces and implementations
    - **Namespace injection**: Various mechanisms for managing symbol visibility
    - **Dependency management**: Structured approach to module dependencies

    **File Operations:**

    - **Safe file handling**: Robust error handling for file operations
    - **JSON processing**: Built-in support for configuration and data files
    - **Path management**: Integration with Python's pathlib for file system operations
    - **Configuration management**: External configuration files for application flexibility

    **Project Structure:**

    - **Modular design**: Logical organization of code into focused modules
    - **Testing integration**: Built-in support for test files alongside implementation
    - **Documentation**: Clear structure for maintaining project documentation
    - **Scalability**: Structure that grows with project complexity

!!! tip "Try It Yourself"
    Build a modular application by:
    - Creating a multi-file configuration system
    - Implementing interface/implementation separation
    - Setting up a proper project structure
    - Adding error handling for file operations

    Remember: Well-organized modules make your code maintainable and scalable!

---

*Your code is now well-organized and modular. Let's enhance it further with Jac's powerful object-oriented features!*

# Chapter 7: Enhanced OOP - Objects and Classes
---

Jac fully supports the principles of Object-Oriented Programming (OOP) but enhances them to be more intuitive and efficient. This chapter will guide you through using Jac's obj archetype to create well-structured, maintainable, and powerful objects.

Jac simplifies the object creation process by providing features like **automatic constructors**, **implementation separation**, and **improved access control**, which reduce boilerplate code and allow you to focus more on the logic of your application.


## Jac `obj` Archetype
---

In Jac, you define a blueprint for an object using the `obj` archetype, which serves a similar purpose to the class keyword in Python. An `obj` bundles data (attributes) and behavior (methods) into a single, self-contained unit.

Let's define a `Pet` object to see how this works.

```jac
obj Pet {
    # 1. Define attributes with the 'has' keyword.
    has name: str;
    has species: str;
    has age: int;
    has is_adopted: bool = False;  # Automatic default

    # 2. Define methods with the 'def' keyword.
    # Methods use 'self' to access the object's own attributes.
    def adopt() -> None {
        self.is_adopted = True;
        print(f"{self.name} has been adopted!");
    }

    def get_info() -> str {
        status = "adopted" if self.is_adopted else "available";
        return f"{self.name} is a {self.age}-year-old {self.species} ({status})";
    }
}

with entry {
    # 3. Create an instance using the automatic constructor.
    pet = Pet(name="Buddy", species="dog", age=3);
    print(pet.get_info());
    pet.adopt();
}
```
<br />

Let's break down the key features demonstrated in this example.

1. Defining Attributes with `has`: The `has` keyword is used to declare the data fields (attributes) that each Pet object will hold. You must provide a type for each attribute, and you can optionally set a default value, like `is_adopted: bool = False`.
2. The Automatic Constructor: Notice that we did not have to write an __init__ method. Jac automatically generates a constructor for you based on the attributes you declare with has. This saves you from writing repetitive boilerplate code and allows you to create a new Pet instance with a clean and direct syntax: `Pet(name="Buddy", species="dog", age=3)`.


### Advanced Constructor Features

Sometimes, you need to run logic after an object's initial attributes have been set. For this, Jac provides the `postinit` method. This is useful for calculated properties or for validation that depends on multiple attributes.
To use it, you declare an attribute with the by `postinit` modifier. This signals that the attribute exists, but its value will be assigned within the `postinit` method.
Let's enhance our pet shop example. We'll create a PetShop object and use `postinit` to automatically set its is_open status based on whether it has reached its capacity.


```jac
obj PetShop {
    # Attributes set by the automatic constructor
    has name: str;
    has pets: list[Pet] = [];
    has capacity: int = 10;
    # This attribute's value will be calculated after initialization.
    has is_open: bool by postinit;

     # This method runs automatically after the object is created
    def postinit() -> None {
        # This logic determines the value of 'is_open'.
        self.is_open = len(self.pets) < self.capacity;
        print(f"{self.name} shop initialized with {len(self.pets)} pets");
    }
}
```
<br />

## Object Inheritance
---
Inheritance is a fundamental concept in OOP that allows you to create a new, specialized object based on an existing one. The new object, or subclass, inherits all the attributes and methods of the parent object, and can add its own unique features or override existing ones. This promotes code reuse and helps create a logical hierarchy.

### Simple Inheritance Example

```jac
obj Animal {
    has name: str;
    has species: str;
    has age: int;

    def make_sound() -> None {
        print(f"{self.name} makes a sound.");
    }
}
# A subclass that inherits from Animal
obj Dog(Animal) {
    has breed: str;

    def make_sound() -> None {
        print(f"{self.name} barks.");
    }
}

obj Cat(Animal) {
    has color: str;

    def make_sound() -> None {
        print(f"{self.name} meows.");
    }
}
```
<br />
In this example, both `Dog` and `Cat` automatically have the `name`, `species`, and `age` attributes from Animal. However, they each provide a specialized version of the `make_sound` method, demonstrating polymorphism.

## Access Control with `:pub`, `:priv`, `:protect`
---
To create robust and secure objects, it is important to control which of their attributes and methods can be accessed from outside the object's own code. This principle is called encapsulation. Unlike Python, Jac provides explicit keywords that are enforced by the runtime.

### Public Access
Public members are accessible from anywhere. This is the default behavior in Jac, so the `:pub` keyword is optional but can be used for clarity.

```jac
obj PublicExample {
    # This attribute is public by default.
    has :pub public_property: str;

    # Explicitly marking a method as public.
    def :pub public_method() -> str {
        return "This is a public method";
    }
}
with entry {
    example = PublicExample(public_property="Hello");
    # Both are accessible from outside the object.
    print(example.public_method());
    print(example.public_property);
}
```
<br />

### Private Access
Private members, marked with `:priv`, can only be accessed from within the object itself. Any attempt to access a private member from outside code will result in an error. This is essential for protecting an object's internal state.

```jac
obj PrivateExample {
    has :priv private_property: str;
    has :priv another_private_property: int = 42;

    def :priv private_method() -> str {
        return "This is a private method";
    }

    def :pub public_method() -> str {
        return self.private_method();
    }
}
with entry {
    example = PrivateExample(private_property="Secret");
    print(example.public_method());
    # print(example.private_property);  # This would raise an error
}
```
<br />

### Protected Access
Protected members, marked with `:protect`, create a middle ground between public and private. They are accessible within the object that defines them and within any of its subclasses. This is useful for creating internal logic that you want to share across a family of related objects but still keep hidden from the outside world.

```jac
obj ProtectedExample {

    has :protect protected_property: str = "Protected";
    has :protect protected_list: list[int] = [];
    has :protect protected_dict: dict[str, int] = {"key": 1};

    def :protect protected_method() -> str {
        return "This is a protected method";
    }
}
obj SubProtectedExample(ProtectedExample) {
    def :pub public_method() -> str {
        return self.protected_method();
    }
}
with entry {
    example = SubProtectedExample();
    print(example.public_method());
    print(example.protected_property);
    print(example.protected_list);
    print(example.protected_dict);
}
```
<br />


### Example: Pet Record System

Let's combine these access control concepts into a practical example. We will build a `PetRecord` object that securely manages a pet's information.

- Public information, like the pet's name, will be freely accessible.
- Protected information, like medical history, will be accessible only to specialized subclasses like a VetRecord.
- Private information, like the owner's contact details, will be strictly controlled by the object itself.


<div class="code-block">
```jac
obj PetRecord {
    # Public - anyone can access
    has :pub name: str;
    has :pub species: str;

    # Private - only this class
    has :priv owner_contact: str;
    has :priv microchip_id: str;

    # Protected - only this class and subclasses
    has :protect medical_history: list[str] = [];
    has :protect last_checkup: str = "";

    # Public method
    def :pub get_basic_info() -> str {
        return f"{self.name} is a {self.species}";
    }

    # Protected method - for vets and staff
    def :protect add_medical_record(record: str) -> None {
        self.medical_history.append(record);
        print(f"Medical record added for {self.name}");
    }

    # Private method - internal use only
    def :priv validate_contact(contact: str) -> bool {
        return "@" in contact and len(contact) > 5;
    }

    def :pub update_owner_contact(new_contact: str) -> bool {
        if self.validate_contact(new_contact) {
            self.owner_contact = new_contact;
            return True;
        }
        return False;
    }
}

obj VetRecord(PetRecord) {
    has :protect vet_notes: str = "";

    def :pub add_vet_note(note: str) -> None {
        # Can access protected members from parent
        self.add_medical_record(f"Vet note: {note}");
        self.vet_notes = note;
    }

    def :pub get_medical_summary() -> str {
        # Can access protected data
        record_count = len(self.medical_history);
        return f"{self.name} has {record_count} medical records";
    }
}

with entry {
    # Create a pet record
    pet = PetRecord(
        name="Fluffy",
        species="cat",
        owner_contact="owner@example.com",
        microchip_id="123456789"
    );

    # Public access works
    print(pet.get_basic_info());
    print(f"Pet name: {pet.name}");

    # Update contact through public method
    success = pet.update_owner_contact("new_owner@example.com");
    print(f"Contact updated: {success}");

    # Vet record with access to protected methods
    vet_record = VetRecord(
        name="Rex",
        species="dog",
        owner_contact="owner2@example.com",
        microchip_id="987654321"
    );

    vet_record.add_vet_note("Annual checkup - healthy");
    print(vet_record.get_medical_summary());
}
```
</div>
<br />



## Key Differences from Python OOP
- **Automatic Constructors**: No need to write `__init__` methods
- **Enforced Access Control**: `:pub`, `:priv`, `:protect` are actually enforced
- **Clean Inheritance**: Automatic constructor chaining in inheritance
- **Type Safety**: All method parameters and returns must be typed
- **Implementation Separation**: Can separate interface from implementation


## Wrapping Up
---

In this chapter, you've learned how Jac builds upon classic Object-Oriented Programming with features that promote cleaner, safer, and more maintainable code. You now have the tools to create robust, well-structured objects with automatic constructors, enforced access control, and clear inheritance.

These concepts form the foundation upon which Jac's most powerful paradigm is built. In the next chapter, we will make the leap from Object-Oriented to Object-Spatial Programming (OSP). We will see how these objects are extended into nodes that can exist in a graph, giving them a spatial context and unlocking a new way to handle complex, interconnected data.

---

*You now have powerful object-oriented tools at your disposal. Let's discover how OSP takes these concepts to the next level!*


# Chapter 8: OSP Introduction and Paradigm Shift
---
So far, we have explored Jac's enhancements to familiar programming concepts. Now, we will introduce the paradigm that makes Jac truly unique, Object-Spatial Programming (OSP). This represents a fundamental shift in how we structure and execute our programs.

In traditional programming, the application logic is stationary, and data is constantly fetched from databases and other services to be processed. OSP inverts this model, it allows your computation to travel to where your data lives. This approach is more natural, efficient, and scalable for the interconnected data that defines modern AI applications.

## Journey from OOP to OSP
---
The transition from Object-Oriented Programming to Object-Spatial Programming begins with understanding how Jac perceives your program's structure.

### `with entry` vs `if __name__ == "__main__":`

In Python, the entry point of a program is typically defined by the `if __name__ == "__main__":` block. This tells the interpreter, "Start running the script from here."

Jac's `with entry` block serves a similar purpose but has a deeper, more powerful meaning. It isn't just starting a script, it is your moment of entry into a persistent, spatial environment. Think of it as opening the door to a workshop. When your program starts, this workshop is not empty; it contains a single, special starting point: the `root` node. We're entering the root of a global graph structure that we can build upon and traverse.

![With Entry](../assets/examples/jac_book/with_entry.png){ width=350px }

`with entry` marks your entry point into the program's graph. This graph initially contains only the root node, which serves as the anchor for everything you will build.


Everything you create and connect within this graph space can be persisted, traversed, and reasoned about spatially.

### Creating a Node and adding it to the Graph

When the `with entry` block is executed, it creates a root node in the Jac graph. From there, we can add nodes` and `edges` to build our data structure. Lets look at an example of creating a simple node using Jac's syntax:

```jac
node Node{
    has name: str;
}

with entry {
    node_a = Node(name="A");
}
```
<br />

![With Entry](../assets/examples/jac_book/node1.png){ width=350px }


Here, we define a node using the `node` keyword, which is similar to defining a class in traditional OOP. The `has` keyword declares properties for the node, and we create an instance of this node within the `with entry` block.

### Connecting Nodes with Edges

When the entry point is executed, it creates a root node on the Jac graph, which can be accessed using the `root` variable. This root node serves as the starting point for the program's graph structure, enabling traversal and manipulation of connected nodes.

In the example above, we create a new node `node_a` with the name "A". However, this node is not automatically part of the graph—it exists in isolation. To incorporate it into the graph, we need to connect it to an existing node using an `edge`.

This is where the `++>` operator comes in. It creates a directional edge from the root node to `node_a`, effectively linking the two and adding `node_a` into the graph.

```jac
node Node{
    has name: str;
}

with entry {
    node_a = Node(name="A");
    root ++> node_a;  # Add node_a to the root graph
}
```
<br />

![With Entry](../assets/examples/jac_book/node2.png){ width=350px }


### Building out the rest of the Graph

Now that we have a basic understanding of nodes and edges, let's add a few more nodes and edges to create a more complex graph structure. We'll introduce a second node and connect it to the first one:

```jac
node Node{
    has name: str;
}

with entry {
    node_a = Node(name="A");
    node_b = Node(name="B");

    root ++> node_a;  # Add node_a to the root graph
    node_a ++> node_b;  # Connect node_a to node_b
}
```
<br />

Next let's define a terminal node that will represent the end of our graph traversal. This node will not have any outgoing edges, indicating that it is a leaf node in our graph structure:

```jac
node EndNode {}
glob END = EndNode();  # Create a global end node
```
<br />

Now we can connect our nodes to this end node, creating a complete graph structure:
```jac
node Node{
    has name: str;
}
node EndNode {}
glob END = EndNode();

with entry {
    node_a = Node(name="A");
    node_b = Node(name="B");

    root ++> node_a;  # Add node_a to the root graph
    node_a ++> node_b;  # Connect node_a to node_b
    node_b ++> END;  # Connect node_b to the end node
}
```
<br />

![With Entry](../assets/examples/jac_book/node3.png){ width=350px }

## From "Data to Computation" to "Computation to Data"


### Walking the Graph

In Object-Oriented Programming, your objects are stationary. You call a method on an object, and the logic executes within that object's context.

One of the core innovations of Object-Spatial Programming (OSP) is the concept of **walkers**. A walker is a mobile unit of computation that you design to travel through your graph, moving from node to node along the edges that connect them. Instead of pulling data to your logic, a walker brings your logic directly to the data.

Walkers operate **locally**, performing actions at each node or edge they encounter. This enables a more natural and efficient way to process distributed data, especially in systems modeled as networks, hierarchies, or flows.


Walkers are more than simple graph crawlers. Because they are a subtype of the `object` archetype, they can,

- Maintain State: A walker can have its own attributes (has fields) to store information it collects during its journey.
- Execute Logic: A walker has methods (can abilities) that are automatically triggered when it "lands on" a specific type of node or edge.
- Make Decisions: Based on the data it finds at its current location, a walker can decide where to go next.

This paradigm shift—from centralized logic to distributed, mobile computation—is what makes OSP so powerful for modeling complex, real-world systems.


Getting back to our graph structure, lets define a simple walker that will traverse our graph and gather the names of the nodes it visits. When it reaches the terminal node, it will stop and return the collected names as a string:

First, we need to enhance our graph with a starting and ending point.

```jac
node Node {
    has name: str;
}

# A special node to mark the end of a path.
node EndNode {}

# Our full graph structure
with entry {
    # Spawn nodes and attach them to the graph.
    node_a = root ++> Node(name="A");
    node_b = node_a ++> Node(name="B");
    node_c = node_b ++> Node(name="C");
    end_node = node_c ++> EndNode(); # The path ends here.
}
```

Next we define our walker archetype, that has a `input` field to store the names of the nodes it visits:
```jac
walker PathWalker {
    has input: str;
}

walker PathWalker {
    has input: str;

    # 1. The starting point for the walker's journey.
    can start with `root entry {
        # Start visiting nodes connected to the root.
        visit [-->];
    }

    # 2. This ability triggers every time the walker lands on a 'Node'.
    can visit_node with Node entry {
        # 'here' refers to the node the walker is currently on.
        self.input += ", visiting " + here.name;  # Append node name to input
        # Continue to the next node in the path.
        visit [-->];
    }

    # 3. This ability triggers when the walker reaches the 'EndNode'.
    can visit_end with EndNode entry {
        self.input += ", reached the end";  # Append end message
        return;  # Stop visiting
    }
}
```
<br />

### The `visit` Statement and `-->` Syntax

To understand how walkers move through the graph, it's important to break down the `visit` statement and the `-->` operator used in the example above.

In Jac, visit tells the walker to continue its traversal along the graph. What makes this powerful is the use of edge selectors inside the square brackets, like `[-->]`, which control how and where the walker moves.

The `-->` symbol represents a forward edge in the graph—specifically, an edge from the current node (`here`) to any of its connected child nodes. So when you write visit `[-->];`, you're instructing the walker to follow all outgoing edges from the current node to the next set of reachable nodes.

Let's walk through what each part means:

- `visit [-->];`: Move the walker along all forward edges from the current node.
- `visit [<--];`: Move backward (along incoming edges), useful for reverse traversals or backtracking.
- `visit [-->-->];`: Move along two forward edges in succession, allowing for deeper traversal into the graph.

Jac supports more complex edge selectors as well which we'll explore in subsequent chapters. For now, the key takeaway is that `visit` combined with edge selectors allows walkers to navigate the graph structure dynamically, processing nodes and edges as they go.


### Putting it All Together

Lets put everything together in a complete example that demonstrates how to create a graph, define a walker, and run it to collect node names:

```jac
node Node{
    has name: str;
}

node EndNode {}
glob END = EndNode();

walker PathWalker {
    has input: str;

    can start with `root entry {
        visit [-->];
    }

    can visit_node  with Node entry{
        self.input += ", visiting " + here.name;
        visit [here-->];
    }

    can visit_end with EndNode entry {
        self.input += ", reached the end";
        return;
    }
}

with entry {
    root ++> Node(name="A")
         ++> Node(name="B")
         ++> END;

    my_walker = PathWalker(input="Start walking") spawn root;

    print(my_walker.input);
}
```
<br />

```bash
$ jac run path_walker.jac
Start walking, visiting A, visiting B, reached the end
```
<br />


## Wrapping Up

In this chapter, we've introduced the core concepts of Object-Spatial Programming (OSP) and how it differs from traditional object-oriented programming. We've seen how Jac allows us to define nodes and edges, create walkers, and traverse graphs in a way that naturally reflects the relationships between data.


## Key Takeaways


- **Computation to data**: Move processing to where data naturally lives
- **Spatial relationships**: Model connections as first-class graph structures
- **Natural representation**: Express real-world relationships directly in code
- **Distributed processing**: Each data location can be processed independently

**Core Concepts:**

- **Nodes**: Stateful entities that hold data and can react to visitors
- **Edges**: First-class relationships with their own properties and behaviors
- **Walkers**: Mobile computation that traverses and processes graph structures
- **Graph thinking**: Shift from object-oriented to relationship-oriented design

**Key Advantages:**

- **Intuitive modeling**: Problems are expressed in their natural graph form
- **Efficient processing**: Computation happens exactly where it's needed
- **Scalable architecture**: Naturally distributes across multiple nodes
- **Maintainable code**: Clear separation of data, relationships, and processing logic


!!! tip "Try It Yourself"
    Start thinking spatially by modeling:
    - Family trees with person nodes and relationship edges
    - Social networks with user connections
    - Organization charts with employee and department relationships
    - Knowledge graphs with concept connections

    Remember: OSP shines when your problem naturally involves connected data!

---

*You've now grasped the fundamental paradigm shift of OSP. Let's build the foundation with nodes and edges!*

# Chapter 9: Nodes and Edges
---

In Object-Spatial Programming, **nodes** and **edges** are the fundamental building blocks of your application's graph. A node represents an entity or a location for your data, while an edge represents a typed, directional relationship between two nodes.
This chapter will show you how to define these core components and how to give your nodes special abilities, allowing them to interact with the walkers that visit them.


## Node Abilities
---
So far, we have seen how walkers can be programmed with intelligence to traverse a graph and collect information. But in Jac, nodes are not just passive data containers. They can have their own abilities—methods that are specifically designed to trigger when a certain type of walker arrives.
This two-way dynamic enables powerful, flexible interactions between walkers and the graph they explore.

### Usecase: Uninformed State Agent
Imagine you have a generic agent, a StateAgent, whose job is to collect information about its environment. This agent knows nothing in advance about the types of nodes it will encounter. In a traditional program, this would be difficult; how can code act on data structures it doesn't know exist?

In Jac, we can make the nodes themselves intelligent. They can recognize the StateAgent when it arrives and "teach" it about their own data.
First, let's define our simple StateAgent. Its only feature is a state dictionary to store any information it gathers.


First, let's define our StateAgent walker:
```jac
walker StateAgent {
    # This dictionary will hold the data collected during the journey.
    has state: dict = {};

    # The walker's journey starts at the root node.
    can start with `root entry {
        # From the root, visit all directly connected nodes.
        visit [-->];
    }
}
```

Next, we will define two different types of nodes, `Weather` and `Time`. Each one will have a special ability that only triggers for a StateAgent.

```jac
node Weather {
    has temp: int = 80;

    # This ability is only triggered when a walker of type 'StateAgent' arrives.
    can get with StateAgent entry {
        # 'visitor' is a special keyword that refers to the walker currently on this node.
        visitor.state["temperature"] = self.temp;
    }
}
```

The `Weather` node knows that when a `StateAgent` visits, it should update the agent's state dictionary by adding a "temperature" key with its own local temp value.


```jac
node Time {
    has hour: int = 12;

    # This node also has a specific ability for the StateAgent.
    can get with StateAgent entry {
        visitor.state["time"] = f"{self.hour}:00 PM";
    }
}
```
Similarly, the `Time` node updates the agent's state with the current hour.

Finally, let's build the graph and dispatch our agent.

```jac
with entry {
    # Create and connect our nodes to the root.
    root ++> Weather();
    root ++> Time();

    # Create an instance of our agent and spawn it on the root node to begin.
    agent = StateAgent() spawn root;

    # After the walker has finished visiting all connected nodes,
    # print the state it has collected.
    print(agent.state);
}
```

When you run this program, the `StateAgent` starts at the `root`, visits both the `Weather` node and the `Time` node, and at each stop, the node's specific ability is triggered. By the time the walker's journey is complete, its state dictionary has been fully populated by the nodes themselves.


```jac
# node_abilities.jac
walker StateAgent{
    has state: dict = {};

    can start with `root entry {
        visit [-->];
    }
}

node Weather {
    has temp: int = 80;

    can get with StateAgent entry {
        visitor.state["temperature"] = self.temp;
    }
}

node Time {
    has hour: int = 12;

    can get with StateAgent entry {
        visitor.state["time"] = f"{self.hour}:00 PM";
    }
}

with entry {
    root ++> Weather();
    root ++> Time();

    agent = StateAgent() spawn root;
    print(agent.state);
}
```


```terminal
$ jac run node_abilities.jac

{"temperature": 80, "time": "12:00 PM"}
```

This pattern is incredibly powerful. It allows you to build a complex world of specialized nodes and then explore it with simple, generic agents. The intelligence is distributed throughout the graph, not just centralized in the walker.

## Node Inheritance
---

In Jac, nodes are more than just data—they can encapsulate behavior and interact with walkers. When building modular systems, it’s often useful to group nodes by type or functionality. This is where node inheritance comes in.

Let’s revisit the `Weather` and `Time` nodes from the previous example. While they each provide different types of information, they serve a common purpose: delivering contextual data to an agent. In Jac, we can express this shared role using inheritance, just like in traditional object-oriented programming.

We define a base node archetype called `Service`. This acts as a common interface for all context-providing nodes. Any node that inherits from `Service` is guaranteed to support certain interactions—either by shared methods or simply by tagging it with a common type.

```jac
node Service {}
```

Next, we will redefine `Weather` and `Time` to inherit from `Service`. This tells our system that they are both specific kinds of `Service` nodes.

```jac
# Weather is a type of Service.
node Weather(Service) {
    has temp: int = 80;

    can get with StateAgent entry {
        visitor.state["temperature"] = self.temp;
    }
}

# Time is also a type of Service.
node Time(Service) {
    has hour: int = 12;

    can get with StateAgent entry {
        visitor.state["time"] = f"{self.hour}:00 PM";
    }
}
```
<br />

Now that our nodes are organized, we can make our `StateAgent` walker much smarter. Instead of blindly visiting every node connected to the root, we can instruct it to only visit nodes that are a Service.

```jac
walker StateAgent {
    has state: dict = {};

    can start with `root entry {
        visit [-->(`?Service)];  # Visit any node that is a subtype of Service
    }
}
```
<br />

This simple change makes your `StateAgent` incredibly flexible. If you later add new service nodes to your graph, like `Location` or `Date`, you don't need to change the walker's code at all. As long as the new nodes inherit from `Service`, the walker will automatically visit them.


The ```-->(`?NodeType)``` syntax is a powerful feature of Jac that allows you to filter nodes by type. It tells the walker to visit any node that matches the `NodeType` type, regardless of its specific implementation.

### Usecase: A Smarter NPC

Let's apply these advanced patterns to a practical and engaging example. We will create an NPC (non-player character) in a game whose behavior changes based on the **weather** or **time of day**. For instance, the NPC might be cheerful in the morning, grumpy when it's hot, or sleepy at night.

We have already built the perfect foundation for this.
- We have `Weather` and `Time` nodes that can provide environmental context.
- They both inherit from a common `Service` type.
- We have a StateAgent walker that can automatically visit all `Service` nodes and collect their data into its state dictionary.

Now, let's build the NPC itself.

#### Step 1 - The Mood Function

Our NPC needs to determine its mood based on the environmental data collected by the StateAgent. Instead of writing complex if/else statements, we can delegate this creative task to a Large Language Model (LLM).

Using the byLLM plugin, we can define a function that sends the agent's state to an LLM and asks it to return a mood.

Here’s the code for our mood function:
```jac
import from byllm { Model }

# Configure the LLM
glob npc_model = Model(model_name="gpt-4.1-mini");

"""Adjusts the tone or personality of the shop keeper npc depending on weather/time."""
def get_ambient_mood(state: dict) -> str by npc_model();
```
This function, `get_ambient_mood`, takes a dictionary (the walker’s state) and sends it to the model. The model interprets the contents like `temperature` and `time`—and returns a textual mood that fits the situation.

#### Step 2 - The NPC Node

Next, we'll define the `NPC` node. Its key feature is an ability that triggers when our StateAgent visits. This ability uses the get_ambient_mood function to determine its own mood based on the information the agent has already collected.


```jac

node NPC {
    can get with StateAgent entry {
        visitor.state["npc_mood"] = get_ambient_mood(visitor.state);
    }
}
```
This is where the NPC’s personality is generated—based entirely on the graph-derived context.


#### Walker Composition
Our `StateAgent` knows how to collect environmental data, but it doesn't know to visit an `NPC` node. We can create a new, more specialized walker that does both.

Using walker inheritance, we can create an NPCWalker that inherits all the abilities of StateAgent and adds a new ability to visit NPC nodes.

```jac
walker NPCWalker(StateAgent) {
    can visit_npc with `root entry {
        visit [-->(`?NPC)];
    }
}
```
The `NPCWalker` first inherits the behavior of `StateAgent` (which collects context), and then adds a second phase to interact with the NPC after that context is built.


#### Putting It All Together

Finally, we can compose everything in a single entry point:
```jac
import from byllm { Model }

# Configure different models
glob npc_model = Model(model_name="gpt-4.1-mini");

node Service{}

walker StateAgent{
    has state: dict = {};

    can start with `root entry {
        visit [-->(`?Service)];
    }
}

node Weather(Service) {
    has temp: int = 80;

    can get with StateAgent entry {
        visitor.state["temperature"] = self.temp;
    }
}

node Time(Service) {
    has hour: int = 12;

    can get with StateAgent entry {
        visitor.state["time"] = f"{self.hour}:00 PM";
    }
}

"""Adjusts the tone or personality of the shop keeper npc depending on weather/time."""
def get_ambient_mood(state: dict) -> str by npc_model();

node NPC {
    can get with StateAgent entry {
        visitor.state["npc_mood"] = get_ambient_mood(visitor.state);
    }
}

walker NPCWalker(StateAgent) {
    can visit_npc with `root entry{
        visit [-->(`?NPC)];
    }
}


with entry {
    root ++> Weather();
    root ++> Time();
    root ++> NPC();

    agent = NPCWalker() spawn root;
    print(agent.state['npc_mood']);
}
```
<br />

```terminal
$ jac run npc_mood.jac

"The shopkeeper greets you warmly with a bright smile, saying, "What a hot day we’re having!
Perfect time to stock up on some refreshing potions or cool drinks. Let me know if you need
anything to beat the heat!"
```
<br />


## Edge Types and Relationships
---
In Jac, edges are the pathways that connect your nodes. They are more than just simple pointers; they are first-class citizens of the graph. This means an `edge` can have its own attributes and abilities, allowing you to model rich, complex relationships like friendships, ownership, or enrollment.

Edges in Jac are not just connections - they're full objects with their own properties and behaviors. This makes relationships as important as the data they connect.

### Basic Edge Declaration

You define an edge's blueprint using the `edge` keyword, and you can give it has attributes just like a node or object.

Let's model a simple school environment with Student, Teacher, and Classroom nodes, and the various relationships that connect them.

```jac
# Basic node for representing teachers
node Teacher {
    has name: str;
    has subject: str;
    has years_experience: int;
    has email: str;
}

# Basic node for representing students
node Student {
    has name: str;
    has age: int;
    has grade_level: int;
    has student_id: str;
}

node Classroom {
    has room_number: str;
    has capacity: int;
    has has_projector: bool = True;
}

# Edge for student enrollment
edge EnrolledIn {
    has enrollment_date: str;
    has grade: str = "Not Assigned";
    has attendance_rate: float = 100.0;
}

# Edge for teaching assignments
edge Teaches {
    has start_date: str;
    has schedule: str;  # "MWF 9:00-10:00"
    has is_primary: bool = True;
}

# Edge for friendship between students
edge FriendsWith {
    has since: str;
    has closeness: int = 5;  # 1-10 scale
}

with entry {
    # 1. Create the main classroom node and attach it to the root.
    science_lab = root ++> Classroom(
        room_number="Lab-A",
        capacity=24,
        has_projector=True
    );

    # 2. Create a teacher AND the 'Teaches' edge that connects them to the classroom.
    dr_smith = science_lab +>:Teaches(
        start_date="2024-08-01",
        schedule="TR 10:00-11:30"
    ):+> Teacher(
        name="Dr. Smith",
        subject="Chemistry",
        years_experience=12,
        email="smith@school.edu"
    );
}
```
Let's break down the edge creation syntax: <+:EdgeType(attributes):+>.
science_lab: The starting node (the "source" of the edge).

- `<+: ... :+>`: This syntax creates a bi-directional edge. It means the relationship can be traversed from science_lab to dr_smith and also from dr_smith back to science_lab.
- `Teaches(...)`: The type of edge we are creating, along with the data for its attributes.
- `Teacher(...)`: The destination node.

## Graph Navigation and Filtering
---
Jac provides powerful and expressive syntax for navigating and querying graph structures. Walkers can traverse connections directionally—forward or backward—and apply filters to control exactly which nodes or edges should be visited.

### Directional Traversal
- `-->` : Follows outgoing edges from the current node.
- `<--` : Follows incoming edges to the current node.

These can be wrapped in a visit statement to direct walker movement:
```jac
visit [-->];   # Move to all connected child nodes
visit [<--];   # Move to all parent nodes
```


### Filter by Node Type
To narrow traversal to specific node types, use the filter syntax:
```jac
-->(`?NodeType)
```

This ensures the walker only visits nodes of the specified archetype.
```jac
# Example: Visit all Student nodes connected to the root
walker FindStudents {
    can start with `root entry {
        visit [-->(`?Student)];
    }
}
```
This allows your walker to selectively traverse part of the graph, even in the presence of mixed node types.

### Filtering by Node Attributes
To make your walkers more intelligent, you can instruct them to only visit nodes of a specific type. You achieve this using the (?NodeType) filter. It's also called **attribute-based filtering**.


```jac
-->(`?NodeType: attr1 op value1, attr2 op value2, ...)
```
Where:

- `NodeType` is the node archetype to match (e.g., `Student`)
- `attr1`, `attr2` are properties of that node
- `op` is a comparison operator


#### Supported Operators

| Operator   | Description                    | Example                             |
|------------|--------------------------------|-------------------------------------|
| `==`       | Equality                       | `grade == 90`                        |
| `!=`       | Inequality                     | `status != "inactive"`              |
| `<`        | Less than                      | `age < 18`                           |
| `>`        | Greater than                   | `score > 70`                         |
| `<=`       | Less than or equal to          | `temp <= 100`                       |
| `>=`       | Greater than or equal to       | `hour >= 12`                        |
| `is`       | Identity comparison            | `mood is "happy"`                   |
| `is not`   | Negative identity comparison   | `type is not "admin"`              |
| `in`       | Membership (value in list)     | `role in ["student", "teacher"]`    |
| `not in`   | Negative membership            | `status not in ["inactive", "banned"]` |

#### Example
```jac
# Find all students with a grade above 85
walker FindTopStudents {
    can start with `root entry {
        visit [-->(`?Student: grade > 85)];
    }
}
```
This walker will only visit `Student` nodes where the `grade` property is greater than 85.

### Filtering by Edge Type and Attributes

In addition to filtering by node types and attributes, Jac also allows you to filter based on edge types and edge attributes, enabling precise control over traversal paths in complex graphs.

To traverse only edges of a specific type, use the following syntax:
```jac
visit [->:EdgeType->];
```

This tells the walker to follow only edges labeled as `EdgeType`, regardless of the type of the nodes they connect.

#### Example
```jac
# Only follow "enrolled_in" edges
visit [->:enrolled_in->];
```

### Edge Atribute Filtering
You can further refine edge traversal by applying attribute-based filters directly to the edge:
```jac
visit [->:EdgeType: attr1 op val1, attr2 op val2:->];
```
This format allows you to filter based on metadata stored on the edge itself, not the nodes.

#### Example
```jac
# Follow "graded" edges where score is above 80
visit [->:graded: score > 80:->];
```
This pattern is especially useful when edges carry contextual data, such as timestamps, weights, relationships, or scores.

## Wrapping Up
---
In this chapter, we explored the foundational concepts of nodes and edges in Jac. We learned how to define nodes with properties, create edges to represent relationships, and navigate the graph using walkers. We also saw how to filter nodes and edges based on types and attributes, enabling powerful queries and interactions.

These concepts form the backbone of Object-Spatial Programming, allowing you to model complex systems and relationships naturally. As you continue to build your Jac applications, keep these principles in mind to create rich, interconnected data structures that reflect the real-world entities and relationships you want to represent.

## Key Takeaways
---

**Node Fundamentals:**

- **Spatial objects**: Nodes can be connected and automatically persist when linked to root
- **Property storage**: Nodes hold data using `has` declarations with automatic constructors
- **Automatic persistence**: Nodes connected to root persist between program runs
- **Type safety**: All node properties must have explicit types

**Edge Fundamentals:**

- **First-class relationships**: Edges are full objects with their own properties and behaviors
- **Typed connections**: Edges define the nature of relationships between nodes
- **Bidirectional support**: Edges can be traversed in both directions
- **Rich metadata**: Store relationship-specific data directly in edge properties

**Graph Operations:**

- **Creation syntax**: Use `++>` to create new connections, `-->` to reference existing ones
- **Navigation patterns**: `[-->]` for outgoing, `[<--]` for incoming connections
- **Filtering support**: Apply conditions to find specific nodes or edges
- **Traversal efficiency**: Graph operations are optimized for spatial queries

**Practical Applications:**

- **Natural modeling**: Represent real-world entities and relationships directly
- **Query capabilities**: Find related data through graph traversal
- **Persistence automation**: No manual database management required
- **Scalable architecture**: Graph structure supports distributed processing

!!! tip "Try It Yourself"
    Practice with nodes and edges by building:
    - A classroom management system with students, teachers, and courses
    - A family tree with person nodes and relationship edges
    - A social network with users and friendship connections
    - An inventory system with items and location relationships

    Remember: Nodes represent entities, edges represent relationships - think spatially!

---

*Your graph foundation is solid! Now let's add mobile computation with walkers and abilities.*

# Chapter 10: Walkers and Abilities

In the previous chapters, you learned how to build the static structure of your application using nodes and edges. Now, you will learn how to put that structure into motion. The primary way you interact with and process your graph is by using walkers.

!!! topic "Mobile Computation Philosophy"
    Walkers introduce a fundamental shift from traditional programming. This is the core philosophy of Object-Spatial Programming.
    - In traditional programming, you write a function and then bring data to your code to be processed.
    - In Jac, you create a walker and send your code to your data.
    This model of "mobile computation" is incredibly effective for working with the complex, interconnected data found in AI and other modern systems.

## Walker Creation

!!! topic "What are Walkers?"
    Walkers are special objects that can move through your graph, carrying state and executing abilities when they encounter different types of nodes and edges.

### Basic Walker Declaration

A walker is defined with the walker archetype. Like an obj, it can have has attributes to store data and def methods for internal logic. At this stage, a walker is just an object; it doesn't move or interact with the graph until it is "spawned."

!!! example "Simple Walker"
    === "Jac"
        <div class="code-block">
        ```jac
        # Simple walker for visiting nodes
        walker MessageDelivery {
            has message: str;
            has delivery_count: int = 0;
            has visited_locations: list[str] = [];

            # Regular methods work like normal
            def get_status() -> str {
                return f"Delivered {self.delivery_count} messages to {len(self.visited_locations)} locations";
            }
        }

        with entry {
            # Create walker instance (but don't activate it yet)
            messenger = MessageDelivery(message="Hello from the principal!");

            # Check initial state
            print(f"Initial status: {messenger.get_status()}");
        }
        ```
        </div>
    === "Python"
        ```python
        class MessageDelivery:
            def __init__(self, message: str):
                self.message = message
                self.delivery_count = 0
                self.visited_locations = []

            def get_status(self) -> str:
                return f"Delivered {self.delivery_count} messages to {len(self.visited_locations)} locations"

        class Student:
            def __init__(self, name: str):
                self.name = name
                self.messages = []

        class Teacher:
            def __init__(self, name: str, subject: str):
                self.name = name
                self.subject = subject

        if __name__ == "__main__":
            # Create messenger instance
            messenger = MessageDelivery(message="Hello from the principal!")

            # Check initial state
            print(f"Initial status: {messenger.get_status()}")
        ```

## Ability Definitions and Triggers

!!! topic "Event-Driven Execution"
    Abilities are methods that execute automatically when certain events occur during graph traversal. They create reactive, context-aware behavior.

### Entry and Exit Abilities

The most common abilities are entry and exit abilities.
- An `entry` ability triggers when a walker arrives at a node or edge.
- An `exit` ability triggers when a walker leaves a node or edge.

You can create specialized entry abilities that only trigger for specific node or edge types.

!!! example "Basic Abilities"
    === "Jac"
        <div class="code-block">
        ```jac
        # Basic classroom nodes
        node Student {
            has name: str;
            has messages: list[str] = [];
        }

        node Teacher {
            has name: str;
            has subject: str;
        }

        walker MessageDelivery {
            has message: str;
            has delivery_count: int = 0;
            has visited_locations: list[str] = [];

            # Entry ability - triggered when entering any Student
            can deliver_to_student with Student entry {
                print(f"Delivering message to student {here.name}");
                here.messages.append(self.message);
                self.delivery_count += 1;
                self.visited_locations.append(here.name);
            }

            # Entry ability - triggered when entering any Teacher
            can deliver_to_teacher with Teacher entry {
                print(f"Delivering message to teacher {here.name} ({here.subject})");
                # Teachers just acknowledge the message
                print(f"  {here.name} says: 'Message received!'");
                self.delivery_count += 1;
                self.visited_locations.append(here.name);
            }

            # Exit ability - triggered when leaving any node
            can log_visit with entry {
                node_type = type(here).__name__;
                print(f"  Visited {node_type}");
            }
        }

        with entry {
            # Create simple classroom
            alice = root ++> Student(name="Alice");
            bob = root ++> Student(name="Bob");
            ms_smith = root ++> Teacher(name="Ms. Smith", subject="Math");

            # Create and activate messenger
            messenger = MessageDelivery(message="School assembly at 2 PM");

            # Spawn walker on Alice - this activates it
            alice[0] spawn messenger;

            # Check Alice's messages
            print(f"Alice's messages: {alice[0].messages}");
        }
        ```
        </div>
    === "Python"
        ```python
        class MessageDelivery:
            def __init__(self, message: str):
                self.message = message
                self.delivery_count = 0
                self.visited_locations = []

            def deliver_to_student(self, student):
                print(f"Delivering message to student {student.name}")
                student.messages.append(self.message)
                self.delivery_count += 1
                self.visited_locations.append(student.name)

            def deliver_to_teacher(self, teacher):
                print(f"Delivering message to teacher {teacher.name} ({teacher.subject})")
                print(f"  {teacher.name} says: 'Message received!'")
                self.delivery_count += 1
                self.visited_locations.append(teacher.name)

            def visit_node(self, node):
                node_type = type(node).__name__
                print(f"  Visited {node_type}")

                # Manually check type and call appropriate method
                if isinstance(node, Student):
                    self.deliver_to_student(node)
                elif isinstance(node, Teacher):
                    self.deliver_to_teacher(node)

        if __name__ == "__main__":
            # Create simple classroom
            alice = Student("Alice")
            bob = Student("Bob")
            ms_smith = Teacher("Ms. Smith", "Math")

            # Create and use messenger manually
            messenger = MessageDelivery("School assembly at 2 PM")

            # Manually visit nodes (simulating walker spawn)
            messenger.visit_node(alice)

            # Check Alice's messages
            print(f"Alice's messages: {alice.messages}")
        ```

## Walker Spawn and Visit

!!! topic "Graph Traversal"
    Walkers move through graphs using `spawn` (to start) and `visit` (to continue to connected nodes). This enables complex traversal patterns with simple syntax.

### Basic Traversal Patterns

!!! example "Classroom Message Delivery"
    === "Jac"
        <div class="code-block">
        ```jac
        # Basic classroom nodes
        node Student {
            has name: str;
            has messages: list[str] = [];
        }

        node Teacher {
            has name: str;
            has subject: str;
        }

        walker MessageDelivery {
            has message: str;
            has delivery_count: int = 0;
            has visited_locations: list[str] = [];

            # Entry ability - triggered when entering any Student
            can deliver_to_student with Student entry {
                print(f"Delivering message to student {here.name}");
                here.messages.append(self.message);
                self.delivery_count += 1;
                self.visited_locations.append(here.name);
            }

            # Entry ability - triggered when entering any Teacher
            can deliver_to_teacher with Teacher entry {
                print(f"Delivering message to teacher {here.name} ({here.subject})");
                # Teachers just acknowledge the message
                print(f"  {here.name} says: 'Message received!'");
                self.delivery_count += 1;
                self.visited_locations.append(here.name);
            }

            # Exit ability - triggered when leaving any node
            can log_visit with entry {
                node_type = type(here).__name__;
                print(f"  Visited {node_type}");
            }
        }

        edge InClass {
            has room: str;
        }

        walker ClassroomMessenger {
            has announcement: str;
            has rooms_visited: set[str] = {};
            has people_reached: int = 0;

            can deliver_student with Student entry {
                print(f" Student {here.name}: {self.announcement}");
                self.people_reached += 1;

                # Continue to connected nodes
                visit [-->];
            }

            can deliver_teacher with Teacher entry {
                print(f" Teacher {here.name}: {self.announcement}");
                self.people_reached += 1;

                # Continue to connected nodes
                visit [-->];
            }

            can track_room with InClass entry {
                room = here.room;
                if room not in self.rooms_visited {
                    self.rooms_visited.add(room);
                    print(f" Now in {room}");
                }
            }

            can summarize with Student exit {
                # Only report once at the end
                if len([-->]) == 0 {  # At a node with no outgoing connections
                    print(f" Delivery complete!");
                    print(f"   People reached: {self.people_reached}");
                    print(f"   Rooms visited: {list(self.rooms_visited)}");
                }
            }
        }

        with entry {
            # Create classroom structure
            alice = root ++> Student(name="Alice");
            bob = root ++> Student(name="Bob");
            charlie = root ++> Student(name="Charlie");
            ms_jones = root ++> Teacher(name="Ms. Jones", subject="Science");

            # Connect them in the same classroom
            alice +>:InClass(room="Room 101"):+> bob;
            bob +>:InClass(room="Room 101"):+> charlie;
            charlie +>:InClass(room="Room 101"):+> ms_jones;

            # Send a message through the classroom
            messenger = ClassroomMessenger(announcement="Fire drill in 5 minutes");
            alice[0] spawn messenger;
        }
        ```
        </div>
    === "Python"
        ```python
        class InClass:
            def __init__(self, from_node, to_node, room: str):
                self.from_node = from_node
                self.to_node = to_node
                self.room = room

        class ClassroomMessenger:
            def __init__(self, announcement: str):
                self.announcement = announcement
                self.rooms_visited = set()
                self.people_reached = 0
                self.visited_nodes = set()

            def deliver_to_student(self, student):
                print(f" Student {student.name}: {self.announcement}")
                self.people_reached += 1

            def deliver_to_teacher(self, teacher):
                print(f" Teacher {teacher.name}: {self.announcement}")
                self.people_reached += 1

            def track_room(self, edge):
                if edge.room not in self.rooms_visited:
                    self.rooms_visited.add(edge.room)
                    print(f"   Now in {edge.room}")

            def visit_network(self, node, connections):
                # Avoid infinite loops
                if node in self.visited_nodes:
                    return

                self.visited_nodes.add(node)

                # Process current node
                if isinstance(node, Student):
                    self.deliver_to_student(node)
                elif isinstance(node, Teacher):
                    self.deliver_to_teacher(node)

                # Visit connected nodes
                for edge in connections.get(node, []):
                    self.track_room(edge)
                    self.visit_network(edge.to_node, connections)

                # Check if we're at the end
                if not connections.get(node, []):
                    print(f" Delivery complete!")
                    print(f"   People reached: {self.people_reached}")
                    print(f"   Rooms visited: {list(self.rooms_visited)}")

        if __name__ == "__main__":
            # Create classroom structure
            alice = Student("Alice")
            bob = Student("Bob")
            charlie = Student("Charlie")
            ms_jones = Teacher("Ms. Jones", "Science")

            # Create connections manually
            connections = {
                alice: [InClass(alice, bob, "Room 101")],
                bob: [InClass(bob, charlie, "Room 101")],
                charlie: [InClass(charlie, ms_jones, "Room 101")],
                ms_jones: []
            }

            # Send message through classroom
            messenger = ClassroomMessenger("Fire drill in 5 minutes")
            messenger.visit_network(alice, connections)
        ```

### Advanced Traversal Control

!!! example "Selective Message Delivery"
    === "Jac"
        <div class="code-block">
        ```jac
        import random;

        node Student {
            has name: str;
            has grade_level: int;
            has messages: list[str] = [];
        }

        edge StudyGroup {
            has subject: str;
        }

        walker AttendanceChecker {
            has present_students: list[str] = [];
            has absent_students: list[str] = [];
            has max_checks: int = 5;
            has checks_done: int = 0;

            can check_attendance with Student entry {
                self.checks_done += 1;

                # Simulate checking if student is present (random for demo)
                is_present = random.choice([True, False]);

                if is_present {
                    print(f"{here.name} is present");
                    self.present_students.append(here.name);
                } else {
                    print(f"{here.name} is absent");
                    self.absent_students.append(here.name);
                }

                # Control flow based on conditions
                if self.checks_done >= self.max_checks {
                    print(f"Reached maximum checks ({self.max_checks})");
                    self.report_final();
                    disengage;  # Stop the walker
                }

                # Skip if no more connections
                connections = [-->];
                if not connections {
                    print("No more students to check");
                    self.report_final();
                    disengage;
                }

                # Continue to next student
                visit [-->];
            }

            def report_final() -> None {
                print(f" Attendance Report:");
                print(f"   Present: {self.present_students}");
                print(f"   Absent: {self.absent_students}");
                print(f"   Total checked: {self.checks_done}");
            }
        }

        with entry {
            # Create a chain of students
            alice = root ++> Student(name="Alice", grade_level=9);
            bob = alice ++> Student(name="Bob", grade_level=9);
            charlie = bob ++> Student(name="Charlie", grade_level=9);
            diana = charlie ++> Student(name="Diana", grade_level=9);
            eve = diana ++> Student(name="Eve", grade_level=9);

            # Start attendance check
            checker = AttendanceChecker(max_checks=3);
            alice[0] spawn checker;
        }
        ```
        </div>
    === "Python"
        ```python
        class StudyGroup:
            def __init__(self, from_node, to_node, subject: str):
                self.from_node = from_node
                self.to_node = to_node
                self.subject = subject

        class Student:
            def __init__(self, name: str, grade_level: int):
                self.name = name
                self.grade_level = grade_level
                self.messages = []

        class GradeSpecificMessenger:
            def __init__(self, message: str, target_grade: int):
                self.message = message
                self.target_grade = target_grade
                self.delivered_to = []
                self.visited = set()

            def visit_student(self, student, connections):
                if student in self.visited:
                    return

                self.visited.add(student)

                if student.grade_level == self.target_grade:
                    print(f"Delivering to {student.name} (Grade {student.grade_level}): {self.message}")
                    student.messages.append(self.message)
                    self.delivered_to.append(student.name)
                else:
                    print(f"Skipping {student.name} (Grade {student.grade_level}) - not target grade")

                # Visit connected students
                for edge in connections.get(student, []):
                    print(f"  Moving through {edge.subject} study group")
                    self.visit_student(edge.to_node, connections)

                # Report if at end
                if not connections.get(student, []):
                    print(f" Delivery Summary:")
                    print(f"   Target: Grade {self.target_grade} students")
                    print(f"   Message: '{self.message}'")
                    print(f"   Delivered to: {self.delivered_to}")

        if __name__ == "__main__":
            # Create multi-grade study network
            alice = Student("Alice", 9)
            bob = Student("Bob", 10)
            charlie = Student("Charlie", 9)
            diana = Student("Diana", 11)

            # Create connections
            connections = {
                alice: [StudyGroup(alice, bob, "Math")],
                bob: [StudyGroup(bob, charlie, "Science")],
                charlie: [StudyGroup(charlie, diana, "History")],
                diana: []
            }

            # Send grade-specific message
            messenger = GradeSpecificMessenger(
                "Grade 9 field trip permission slips due Friday!",
                9
            )

            messenger.visit_student(alice, connections)

            # Check who got the message
            print(f"Alice's messages: {alice.messages}")
            print(f"Bob's messages: {bob.messages}")
            print(f"Charlie's messages: {charlie.messages}")
        ```

## Walker Control Flow

!!! topic "Traversal Control"
    Walkers can control their movement through the graph using special statements like `visit` and `disengage`.

### Controlling Walker Behavior

!!! example "Smart Walker Control"
    === "Jac"
        <div class="code-block">
        ```jac
        node Student {
            has name: str;
            has grade_level: int;
        }

        walker AttendanceChecker {
            has present_students: list[str] = [];
            has absent_students: list[str] = [];
            has max_checks: int = 5;
            has checks_done: int = 0;

            can check_attendance with Student entry {
                self.checks_done += 1;

                # Simulate checking if student is present (random for demo)
                import random;
                is_present = random.choice([True, False]);

                if is_present {
                    print(f"{here.name} is present");
                    self.present_students.append(here.name);
                } else {
                    print(f"{here.name} is absent");
                    self.absent_students.append(here.name);
                }

                # Control flow based on conditions
                if self.checks_done >= self.max_checks {
                    print(f"Reached maximum checks ({self.max_checks})");
                    self.report_final();
                    disengage;  # Stop the walker
                }

                # Skip if no more connections
                connections = [-->];
                if not connections {
                    print("No more students to check");
                    self.report_final();
                    disengage;
                }

                # Continue to next student
                visit [-->];
            }

            def report_final() -> None {
                print(f" Attendance Report:");
                print(f"   Present: {self.present_students}");
                print(f"   Absent: {self.absent_students}");
                print(f"   Total checked: {self.checks_done}");
            }
        }

        with entry {
            # Create a chain of students
            alice = root ++> Student(name="Alice", grade_level=9);
            bob = alice ++> Student(name="Bob", grade_level=9);
            charlie = bob ++> Student(name="Charlie", grade_level=9);
            diana = charlie ++> Student(name="Diana", grade_level=9);
            eve = diana ++> Student(name="Eve", grade_level=9);

            # Start attendance check
            checker = AttendanceChecker(max_checks=3);
            alice[0] spawn checker;
        }
        ```
        </div>
    === "Python"
        ```python
        import random

        class AttendanceChecker:
            def __init__(self, max_checks: int = 5):
                self.present_students = []
                self.absent_students = []
                self.max_checks = max_checks
                self.checks_done = 0
                self.should_stop = False

            def check_student(self, student, connections):
                if self.should_stop:
                    return

                self.checks_done += 1

                # Simulate checking if student is present
                is_present = random.choice([True, False])

                if is_present:
                    print(f" {student.name} is present")
                    self.present_students.append(student.name)
                else:
                    print(f" {student.name} is absent")
                    self.absent_students.append(student.name)

                # Control flow based on conditions
                if self.checks_done >= self.max_checks:
                    print(f" Reached maximum checks ({self.max_checks})")
                    self.report_final()
                    return  # Stop checking

                # Continue to next student if available
                next_students = connections.get(student, [])
                if not next_students:
                    print(" No more students to check")
                    self.report_final()
                    return

                # Visit next student
                for next_student in next_students:
                    self.check_student(next_student, connections)

            def report_final(self):
                print(f" Attendance Report:")
                print(f"   Present: {self.present_students}")
                print(f"   Absent: {self.absent_students}")
                print(f"   Total checked: {self.checks_done}")

        if __name__ == "__main__":
            # Create a chain of students
            alice = Student("Alice", 9)
            bob = Student("Bob", 9)
            charlie = Student("Charlie", 9)
            diana = Student("Diana", 9)
            eve = Student("Eve", 9)

            # Create connections (linear chain)
            connections = {
                alice: [bob],
                bob: [charlie],
                charlie: [diana],
                diana: [eve],
                eve: []
            }

            # Start attendance check
            checker = AttendanceChecker(max_checks=3)
            checker.check_student(alice, connections)
        ```

## Key Concepts Summary

!!! summary "Walker and Ability Fundamentals"
    - **Walkers** are mobile computational entities that traverse graphs
    - **Abilities** are event-driven methods that execute automatically during traversal
    - **Entry abilities** trigger when a walker arrives at a node
    - **Exit abilities** trigger when a walker leaves a node
    - **Spawn** activates a walker at a specific starting location
    - **Visit** moves a walker to connected nodes
    - **Disengage** stops a walker's execution

## Best Practices

!!! summary "Walker Design Guidelines"
    - **Keep abilities focused**: Each ability should have a single, clear purpose
    - **Use descriptive names**: Make it clear what each walker and ability does
    - **Handle edge cases**: Check for empty connections before visiting
    - **Control traversal flow**: Use conditions to avoid infinite loops
    - **Report results**: Use exit abilities to summarize walker activities
    - **Manage state**: Use walker properties to track progress and results

## Key Takeaways

!!! summary "What We've Learned"
    **Walker System:**

    - **Mobile computation**: Walkers bring processing directly to data locations
    - **State management**: Walkers carry their own state as they traverse
    - **Traversal control**: Fine-grained control over movement patterns
    - **Spawning mechanism**: Activate walkers at specific graph locations

    **Ability System:**

    - **Event-driven execution**: Abilities trigger automatically based on walker location
    - **Entry/exit patterns**: React to walker arrival and departure events
    - **Context awareness**: Abilities have access to current node (`here`) and walker state
    - **Conditional execution**: Abilities can include logic to control when they execute

    **Traversal Patterns:**

    - **Visit statements**: Direct walker movement to connected nodes
    - **Filtering support**: Visit only nodes that match specific criteria
    - **Flow control**: Use `disengage` to stop walker execution
    - **Recursive traversal**: Walkers can spawn other walkers for complex patterns

    **Practical Applications:**

    - **Data processing**: Process distributed data where it lives
    - **Graph analysis**: Analyze relationships and connections
    - **Message delivery**: Distribute information through networks
    - **State propagation**: Update related nodes based on changes

!!! tip "Try It Yourself"
    Master walkers and abilities by building:
    - A message delivery system that traverses social networks
    - An attendance checker that visits classrooms
    - A family tree explorer that analyzes relationships
    - A network analyzer that processes organizational structures

    Remember: Walkers bring computation to data - think about how your processing can move through your graph!

---

*Walkers and abilities are now part of your toolkit. Let's master advanced graph operations and sophisticated traversal patterns!*

# Chapter 11: Advanced Object Spatial Operations

Now that you understand basic walkers and abilities, let's explore advanced patterns that make Object-Spatial Programming truly powerful. This chapter covers sophisticated filtering, visit control, and traversal patterns using familiar social network examples.

!!! topic "Advanced Graph Operations Philosophy"
    Complex graph operations become intuitive when you move computation to data. Instead of loading entire datasets, walkers intelligently navigate only the relevant portions of your graph, making sophisticated queries both efficient and expressive.

## Advanced Filtering

Advanced filtering allows you to create sophisticated queries that combine multiple criteria, making complex graph searches simple and readable.

!!! topic "Multi-Criteria Graph Queries"
    Advanced filtering combines relationship traversal, node properties, and complex conditions to find exactly the data you need.

### Property-Based Filtering

!!! example "Finding Specific People"
    === "Jac"
        <div class="code-block">
        ```jac
        node Person {
            has name: str;
            has age: int;
            has city: str;
        }

        edge FriendsWith {
            has since: str;
            has closeness: int; # 1-10 scale
        }

        with entry {
            # Create a social network
            alice = root ++> Person(name="Alice", age=25, city="NYC");
            bob = root ++> Person(name="Bob", age=30, city="SF");
            charlie = root ++> Person(name="Charlie", age=22, city="NYC");
            diana = root ++> Person(name="Diana", age=28, city="LA");

            # Create friendships
            alice +>:FriendsWith(since="2020", closeness=8):+> bob;
            alice +>:FriendsWith(since="2021", closeness=9):+> charlie;
            bob +>:FriendsWith(since="2019", closeness=6):+> diana;

            # Find all young people in NYC (age < 25)
            nyc = [root-->(`?Person)](?city == "NYC");
            print("People in NYC:");
            for person in nyc {
                print(f"  {person.name}, age {person.age}");
            }
            young_nyc = nyc(?age < 25);
            print("Young people in NYC:");
            for person in young_nyc {
                print(f"  {person.name}, age {person.age}");
            }

            # Find Alice's close friends (closeness >= 8)
            close_friends = [alice->:FriendsWith:closeness >= 8:->(`?Person)];
            print(f"Alice's close friends:");
            for friend in close_friends {
                print(f"  {friend.name}");
            }

            # Find all friendships that started before 2021
            old_friendships = [root->:FriendsWith:since < "2021":->];
            print(f"Old friendships: {len(old_friendships)} found");
        }
        ```
        </div>
    === "Python"
        ```python
        class Person:
            def __init__(self, name: str, age: int, city: str):
                self.name = name
                self.age = age
                self.city = city
                self.friendships = []

        class FriendsWith:
            def __init__(self, person1: Person, person2: Person, since: str, closeness: int):
                self.person1 = person1
                self.person2 = person2
                self.since = since
                self.closeness = closeness
                person1.friendships.append(self)
                person2.friendships.append(self)

        # Create a social network
        alice = Person("Alice", 25, "NYC")
        bob = Person("Bob", 30, "SF")
        charlie = Person("Charlie", 22, "NYC")
        diana = Person("Diana", 28, "LA")

        # Create friendships
        friendship1 = FriendsWith(alice, bob, "2020", 8)
        friendship2 = FriendsWith(alice, charlie, "2021", 9)
        friendship3 = FriendsWith(bob, diana, "2019", 6)

        all_people = [alice, bob, charlie, diana]

        # Find all young people in NYC (age < 25)
        young_nyc = [p for p in all_people if p.age < 25 and p.city == "NYC"]
        print("Young people in NYC:")
        for person in young_nyc:
            print(f"  {person.name}, age {person.age}")

        # Find Alice's close friends (closeness >= 8)
        close_friends = []
        for friendship in alice.friendships:
            if friendship.closeness >= 8:
                friend = friendship.person2 if friendship.person1 == alice else friendship.person1
                close_friends.append(friend)

        print(f"\nAlice's close friends:")
        for friend in close_friends:
            print(f"  {friend.name}")

        # Find all friendships that started before 2021
        all_friendships = [friendship1, friendship2, friendship3]
        old_friendships = [f for f in all_friendships if f.since < "2021"]
        print(f"\nOld friendships: {len(old_friendships)} found")
        ```

### Complex Relationship Filtering

!!! example "Multi-Hop Filtering"
    === "Jac"
        <div class="code-block">
        ```jac
        node Person {
            has name: str;
            has age: int;
            has city: str;
        }

        edge FriendsWith {
            has since: str;
            has closeness: int; # 1-10 scale
        }

        with entry {
            # Create extended family network
            john = root ++> Person(name="John", age=45, city="NYC");
            emma = root ++> Person(name="Emma", age=43, city="NYC");
            alice = root ++> Person(name="Alice", age=20, city="SF");
            bob = root ++> Person(name="Bob", age=18, city="NYC");

            # Family relationships
            john +>:FriendsWith(since="1995", closeness=10):+> emma;  # Married
            [john[0], emma[0]] +>:FriendsWith(since="2004", closeness=10):+> alice;  # Parents
            [john[0], emma[0]] +>:FriendsWith(since="2006", closeness=10):+> bob;    # Parents

            # Find John's family members under 25
            young_family = [john[0]->:FriendsWith:closeness == 10:->(`?Person)](?age < 25);
            print("John's young family members:");
            for person in young_family {
                print(f"  {person.name}, age {person.age}");
            }

            # Find all people in NYC connected to John
            nyc_connections = [john[0]->:FriendsWith:->(`?Person)](?city == "NYC");
            print(f"John's NYC connections:");
            for person in nyc_connections {
                print(f"  {person.name}");
            }

            # Find friends of friends (2-hop connections)
            friends_of_friends = [john[0]->:FriendsWith:->->:FriendsWith:->(`?Person)];
            print(f"Friend of friends: {len(friends_of_friends)} found");
            for person in friends_of_friends {
                print(f"  {person.name}");
            }
        }
        ```
        </div>
    === "Python"
        ```python
        # Extended family network
        john = Person("John", 45, "NYC")
        emma = Person("Emma", 43, "NYC")
        alice = Person("Alice", 20, "SF")
        bob = Person("Bob", 18, "NYC")

        # Family relationships
        family_relationships = [
            FriendsWith(john, emma, "1995", 10),  # Married
            FriendsWith(john, alice, "2004", 10),  # Parent
            FriendsWith(emma, alice, "2004", 10),  # Parent
            FriendsWith(john, bob, "2006", 10),    # Parent
            FriendsWith(emma, bob, "2006", 10)     # Parent
        ]

        all_people = [john, emma, alice, bob]

        # Find John's family members under 25
        john_connections = []
        for friendship in john.friendships:
            if friendship.closeness == 10:
                connected_person = friendship.person2 if friendship.person1 == john else friendship.person1
                john_connections.append(connected_person)

        young_family = [p for p in john_connections if p.age < 25]
        print("John's young family members:")
        for person in young_family:
            print(f"  {person.name}, age {person.age}")

        # Find all people in NYC connected to John
        nyc_connections = [p for p in john_connections if p.city == "NYC"]
        print(f"\nJohn's NYC connections:");
        for person in nyc_connections {
            print(f"  {person.name}");
        }

        # Find friends of friends (simplified)
        friends_of_friends = []
        for direct_friend in john_connections:
            for friendship in direct_friend.friendships:
                indirect_friend = friendship.person2 if friendship.person1 == direct_friend else friendship.person1
                if indirect_friend != john and indirect_friend not in john_connections:
                    friends_of_friends.append(indirect_friend)

        print(f"\nFriend of friends: {len(set(f.name for f in friends_of_friends))} found")
        for person in set(friends_of_friends):
            print(f"  {person.name}")
        ```

## Visit Patterns

Visit patterns control how walkers traverse your graph. The most powerful feature is indexed visiting using `:0:`, `:1:`, etc., which controls the order of traversal.

!!! topic "Controlling Walker Navigation"
    Visit patterns let you control exactly how walkers move through your graph - whether breadth-first, depth-first, or custom ordering based on your needs.

### Breadth-First vs Depth-First Traversal

!!! example "BFS vs DFS Walker Behavior"
    === "Jac"
        <div class="code-block">
        ```jac
        node Person {
            has name: str;
            has level: int = 0;
        }

        edge ParentOf {}

        walker BFSWalker {
            can traverse with Person entry {
                print(f"BFS visiting: {here.name} (level {here.level})");

                # Visit children - default queue behavior (breadth-first)
                children = [->:ParentOf:->(`?Person)];
                for child in children {
                    child.level = here.level + 1;
                }
                visit children;
            }
        }

        walker DFSWalker {
            can traverse with Person entry {
                print(f"DFS visiting: {here.name} (level {here.level})");

                # Visit children with :0: (stack behavior for depth-first)
                children = [->:ParentOf:->(`?Person)];
                for child in children {
                    child.level = here.level + 1;
                }
                visit :0: children;
            }
        }

        with entry {
            # Create family tree
            grandpa = root ++> Person(name="Grandpa");
            dad = root ++> Person(name="Dad");
            mom = root ++> Person(name="Mom");
            child1 = root ++> Person(name="Alice");
            child2 = root ++> Person(name="Bob");
            grandchild = root ++> Person(name="Charlie");

            # Create relationships
            grandpa +>:ParentOf:+> dad;
            grandpa +>:ParentOf:+> mom;
            dad +>:ParentOf:+> child1;
            mom +>:ParentOf:+> child2;
            child1 +>:ParentOf:+> grandchild;

            print("=== Breadth-First Search ===");
            grandpa[0] spawn BFSWalker();

            # Reset levels
            all_people = [root-->(`?Person)];
            for person in all_people {
                person.level = 0;
            }

            print("\n=== Depth-First Search ===");
            grandpa[0] spawn DFSWalker();
        }
        ```
        </div>
    === "Python"
        ```python
        from collections import deque

        class Person:
            def __init__(self, name: str):
                self.name = name
                self.level = 0
                self.children = []

        class FamilyTraverser:
            def bfs_traversal(self, start_person: Person):
                queue = deque([start_person])
                visited = set()

                while queue:
                    person = queue.popleft()
                    if person in visited:
                        continue

                    visited.add(person)
                    print(f"BFS visiting: {person.name} (level {person.level})")

                    for child in person.children:
                        if child not in visited:
                            child.level = person.level + 1
                            queue.append(child)

            def dfs_traversal(self, person: Person, visited=None):
                if visited is None:
                    visited = set()

                if person in visited:
                    return

                visited.add(person)
                print(f"DFS visiting: {person.name} (level {person.level})")

                for child in person.children:
                    if child not in visited:
                        child.level = person.level + 1
                        self.dfs_traversal(child, visited)

        # Create family tree
        grandpa = Person("Grandpa")
        dad = Person("Dad")
        mom = Person("Mom")
        child1 = Person("Alice")
        child2 = Person("Bob")
        grandchild = Person("Charlie")

        # Create relationships
        grandpa.children = [dad, mom]
        dad.children = [child1]
        mom.children = [child2]
        child1.children = [grandchild]

        traverser = FamilyTraverser()

        print("=== Breadth-First Search ===")
        traverser.bfs_traversal(grandpa)

        # Reset levels
        all_people = [grandpa, dad, mom, child1, child2, grandchild]
        for person in all_people:
            person.level = 0

        print("\n=== Depth-First Search ===")
        traverser.dfs_traversal(grandpa)
        ```

### Priority-Based Visiting

!!! example "Custom Visit Ordering"
    === "Jac"
        <div class="code-block">
        ```jac
        node Person {
            has name: str;
            has priority: int;
        }

        edge ConnectedTo {
            has strength: int;
        }

        walker PriorityWalker {
            can visit_by_priority with Person entry {
                print(f"Visiting: {here.name} (priority: {here.priority})");

                # Get all connections
                connections = [->:ConnectedTo:->(`?Person)];

                if connections {
                    print(f"  Found {len(connections)} connections");
                    for conn in connections {
                        print(f"    {conn.name} (priority: {conn.priority})");
                    }

                    # Visit highest priority first using :0:
                    visit :0: connections;
                }
            }
        }

        with entry {
            # Create network with different priorities
            center = root ++> Person(name="Center", priority=5);
            high_priority = root ++> Person(name="VIP", priority=10);
            medium_priority = root ++> Person(name="Regular", priority=5);
            low_priority = root ++> Person(name="Basic", priority=1);

            # Create connections
            center +>:ConnectedTo(strength=8):+> high_priority;
            center +>:ConnectedTo(strength=5):+> medium_priority;
            center +>:ConnectedTo(strength=3):+> low_priority;

            print("=== Priority-Based Traversal ===");
            center[0] spawn PriorityWalker();
        }
        ```
        </div>
    === "Python"
        ```python
        class Person:
            def __init__(self, name: str, priority: int):
                self.name = name
                self.priority = priority
                self.connections = []

        class ConnectedTo:
            def __init__(self, person1: Person, person2: Person, strength: int):
                self.strength = strength
                person1.connections.append(person2)

        class PriorityWalker:
            def __init__(self):
                self.visited = set()

            def visit_by_priority(self, person: Person):
                if person in self.visited:
                    return

                self.visited.add(person)
                print(f"Visiting: {person.name} (priority: {person.priority})")

                if person.connections:
                    # Sort by priority (highest first)
                    connections = sorted(
                        [p for p in person.connections if p not in self.visited],
                        key=lambda p: p.priority,
                        reverse=True
                    )

                    print(f"  Found {len(connections)} connections")
                    for conn in connections:
                        print(f"    {conn.name} (priority: {conn.priority})")

                    # Visit highest priority first
                    for conn in connections:
                        self.visit_by_priority(conn)

        # Create network with different priorities
        center = Person("Center", 5)
        high_priority = Person("VIP", 10)
        medium_priority = Person("Regular", 5)
        low_priority = Person("Basic", 1)

        # Create connections
        ConnectedTo(center, high_priority, 8)
        ConnectedTo(center, medium_priority, 5)
        ConnectedTo(center, low_priority, 3)

        print("=== Priority-Based Traversal ===")
        walker = PriorityWalker()
        walker.visit_by_priority(center)
        ```

## Best Practices

!!! summary "Advanced Operation Guidelines"
    - **Plan traversal depth**: Use depth limits to prevent infinite loops
    - **Cache expensive calculations**: Store results in walker state
    - **Use early returns**: Skip unnecessary processing with guards
    - **Implement backtracking**: Remove items from paths when backtracking
    - **Optimize filters**: Apply most selective filters first
    - **Consider performance**: Use indexed visits for better control over traversal order

## Key Takeaways

!!! summary "What We've Learned"
    **Advanced Filtering:**

    - **Multi-criteria queries**: Combine node properties, edge attributes, and relationships
    - **Complex conditions**: Use logical operators and nested filters
    - **Property-based selection**: Filter based on node and edge properties
    - **Relationship filtering**: Navigate specific types of connections

    **Visit Patterns:**

    - **Traversal control**: Direct how walkers move through the graph
    - **Breadth vs depth**: Choose appropriate traversal strategy
    - **Priority-based visiting**: Use indexed visits for custom ordering
    - **Performance optimization**: Control traversal for better efficiency

    **Advanced Techniques:**

    - **Smart visiting patterns**: Enable conditional and multi-path exploration
    - **Complex traversals**: Make advanced algorithms like recommendations simple
    - **Walker state management**: Enable backtracking and path discovery
    - **Performance considerations**: Optimize for large graph structures

    **Practical Applications:**

    - **Social network analysis**: Find friends of friends and connection patterns
    - **Recommendation systems**: Discover related items through graph traversal
    - **Path finding**: Navigate through complex relationship networks
    - **Data analysis**: Extract insights from connected information

!!! tip "Try It Yourself"
    Master advanced operations by building:
    - A recommendation engine using friend-of-friend patterns
    - A family tree analyzer with complex relationship queries
    - A social network explorer with priority-based traversal
    - A pathfinding system using breadth-first search

    Remember: Advanced operations enable sophisticated graph algorithms with simple, readable code!

---

*You've mastered advanced graph operations! Next, let's discover how walkers automatically become API endpoints.*

# Chapter 12: Walkers as API Endpoints

In this chapter, we'll explore how Jac automatically transforms walkers into RESTful API endpoints with our **Jac Cloud** Plugin. Jac Cloud, is a revolutionary cloud platform that transforms your Jac programs into scalable web services without code changes. This means you can focus on building your application logic while Jac handles the HTTP details for you.


We'll build a simple shared notebook system that demonstrates automatic API generation, request handling, and parameter validation through a practical example.

!!! info "What You'll Learn"
    - Understanding Jac Cloud's scale-agnostic architecture
    - Converting walkers into API endpoints automatically
    - Deploying applications with zero configuration
    - Managing persistence and state in the cloud

---

## What is Jac Cloud?

Jac Cloud is a cloud-native execution environment designed specifically for Jac programs. It enables developers to write code once and run it anywhere - from local development to production-scale deployments - without any modifications.

!!! success "Key Benefits"
    - **Zero Code Changes**: Same code runs locally and in the cloud
    - **Automatic APIs**: Walkers become REST endpoints automatically
    - **Built-in Persistence**: Data storage handled transparently
    - **Instant Scaling**: Scale by increasing service replicas
    - **Developer Focus**: No infrastructure management needed


## Quick Setup and Deployment
Let's start with a minimal weather API example and gradually enhance it throughout this chapter.

First, ensure you have the Jac Cloud plugin installed:

```bash
pip install jac-cloud
```

Next, crate a simple Jac program that contains a single walker that produces weather information based on a city name. This program creates a REST API endpoint that accepts a city name and returns the weather information. The walker has a property `city` which is automatically mapped to an expected request parameter in the request body.


!!! example "Basic Weather API"
    === "Jac Cloud"
        ```jac
        # weather.jac - No manual API setup needed
        walker get_weather {
            has city: str;

            obj __specs__ {
                static has auth: bool = False;
            }

            can get_weather_data with `root entry {
                # Your weather logic here
                weather_info = f"Weather in {self.city}: Sunny, 25°C";
                report {"city": self.city, "weather": weather_info};
            }
        }
        ```

    === "Traditional Approach"
        ```python
        # app.py - Manual API setup required
        from flask import Flask, jsonify

        app = Flask(__name__)

        @app.route('/weather/<city>', methods=['GET'])
        def get_weather(city):
            weather_info = f"Weather in {city}: Sunny, 25°C"
            return jsonify({"city": city, "weather": weather_info})

        if __name__ == '__main__':
            app.run(debug=True)
        ```


### Deploying to Cloud
To deploy your Jac program as a cloud service, use the `jac serve` command:

```bash
jac serve weather_service.jac
```

!!! success "Instant Deployment"
    ```
    INFO:     Started server process [26286]
    INFO:     Waiting for application startup.
    INFO:     Application startup complete.
    INFO:     Uvicorn running on http://0.0.0.0:8000 (Press CTRL+C to quit)
    ```

Your walker is now automatically available as a REST API endpoint!

To test the API, you can use `curl` or any HTTP client:

```bash
curl -X POST http://localhost:8000/walker/get_weather \
  -H "Content-Type: application/json" \
  -d '{"city": "New York"}'
```

In this example the endpoint is defined as `/walker/get_weather`, however, the endpoint also expects a JSON request body with a `city` field.

The response will be a JSON object containing the weather information.

!!! success "API Response"
    ```json
    {
        "returns": [
            {
                "city": "New York",
                "weather": "Weather in New York: Sunny, 25°C"
            }
        ]
    }
    ```


!!! note "Authentication"
    - Jac Cloud provides built-in support for authentication and authorization.
    - You can define authentication requirements using the `auth` property in the `__specs__` object.
    - By default, all walkers are private, but you can make them public by setting `auth: False`.
    - To learn more about authentication, see the [Jac Cloud Section of the Documentation](../learn/jac-cloud/introduction.md).

---

## Going Beyond: Building a Shared Notebook API
Now that we have a basic understanding of Jac Cloud and how it automatically generates APIs from walkers, let's build a more complex application: a shared notebook system.

First lets develop a walker that allows users to create and retrieve notes in a notebook. This will demonstrate how Jac handles request/response mapping, parameter validation, and persistence automatically.

!!! example "API Development Comparison"
    === "Jac Automatic APIs"
        ```jac
        import uuid;

        # notebook.jac - No manual API setup needed
        node Note {
            has title: str;
            has content: str;
            has author: str;
            has created_at: str = "2024-01-15";
            has id: str = "note_" + str(uuid.uuid4());
        }



        walker create_note {
            has title: str;
            has content: str;
            has author: str;

            obj __specs__ {
                static has auth: bool = False;
            }

            can create_new_note with `root entry {
                new_note = Note(
                    title=self.title,
                    content=self.content,
                    author=self.author
                );

                here ++> new_note;
                report {"message": "Note created", "id": new_note.id};
            }
        }

        walker get_notes {
            obj __specs__ {
                static has auth: bool = False;
            }

            can fetch_all_notes with `root entry {
                all_notes = [-->(`?Note)];
                notes_data = [
                    {"id": n.id, "title": n.title, "author": n.author}
                    for n in all_notes
                ];
                report {"notes": notes_data, "total": len(notes_data)};
            }
        }
        ```
    === "Traditional Approach"
        ```python
        # app.py - Manual API setup required
        from flask import Flask, request, jsonify
        from typing import Dict, List

        app = Flask(__name__)

        # Global storage (in production, use a database)
        notebooks = {}

        @app.route('/create_note', methods=['POST'])
        def create_note():
            try:
                data = request.get_json()
                title = data.get('title')
                content = data.get('content')
                author = data.get('author')

                # Manual validation
                if not title or not content or not author:
                    return jsonify({"error": "Missing required fields"}), 400

                note_id = len(notebooks) + 1
                notebooks[note_id] = {
                    "id": note_id,
                    "title": title,
                    "content": content,
                    "author": author
                }

                return jsonify({"message": "Note created", "id": note_id})
            except Exception as e:
                return jsonify({"error": str(e)}), 500

        @app.route('/get_notes', methods=['GET'])
        def get_notes():
            return jsonify(list(notebooks.values()))

        if __name__ == '__main__':
            app.run(debug=True)
        ```


Deploy your notebook API:

```bash
jac serve simple_notebook.jac
```

We can now test the API using `curl` or any HTTP client via the POST method. The `create_note` walker will accept a JSON request body with `title`, `content`, and `author` fields, and return a response indicating the note was created.

```bash
curl -X POST http://localhost:8000/walker/create_note \
  -H "Content-Type: application/json" \
  -d '{
    "title": "My First Note",
    "content": "This is a test note",
    "author": "Alice"
  }'
```

!!! success "API Response"
    ```json
    {
        "returns": [
            {
                "status": "created",
                "note": {
                    "title": "My First Note",
                    "author": "Alice"
                }
            }
        ]
    }
    ```

To retrieve all notes, we can use the `get_notes` walker:

```bash
curl -X POST http://localhost:8000/walker/get_notes \
  -H "Content-Type: application/json" \
  -d '{}'
```

### Convert to GET request
To convert the `get_notes` walker to a GET request, we can simply change the walker `___specs__` to indicate that it can be accessed via a GET request. This is done by setting the `methods` attribute in the `__specs__` object.

```jac
walker get_notes {
    obj __specs__ {
        static has auth: bool = False;
        static has methods: list = ["get"];
    }

    can fetch_all_notes with `root entry {
        all_notes = [-->(`?Note)];
        notes_data = [
            {"id": n.id, "title": n.title, "author": n.author}
            for n in all_notes
        ];
        report {"notes": notes_data, "total": len(notes_data)};
    }
}
```

The `get_notes` walker can now be accessed via a GET request at the endpoint `/walker/get_notes`.

```bash
curl -X GET http://localhost:8000/walker/get_notes \
  -H "Content-Type: application/json" \
  -d '{}'
```

---

## Parameter Validation

Jac automatically validates request parameters based on walker attribute types. This eliminates manual validation code and ensures type safety.

### Enhanced Notebook with Validation

!!! example "Notebook with Type Validation"

    ```jac
    # validated_notebook.jac
    node Note {
        has title: str;
        has content: str;
        has author: str;
        has priority: int = 1;  # 1-5 priority level
        has tags: list[str] = [];
    }

    walker create_note {
        has title: str;
        has content: str;
        has author: str;
        has priority: int = 1;
        has tags: list[str] = [];

        obj __specs__ {
            static has auth: bool = False;
        }

        can validate_and_create with `root entry {
            # Jac automatically validates types before this runs

            # Additional business logic validation
            if len(self.title) < 3 {
                report {"error": "Title must be at least 3 characters"};
                return;
            }

            if self.priority < 1 or self.priority > 5 {
                report {"error": "Priority must be between 1 and 5"};
                return;
            }

            # Create note with validated data
            new_note = Note(
                title=self.title,
                content=self.content,
                author=self.author,
                priority=self.priority,
                tags=self.tags
            );
            here ++> new_note;

            report {
                "message": "Note created successfully",
                "note_title": new_note.title,
                "priority": new_note.priority
            };
        }
    }
    ```

### Testing Validation

```bash
# Valid request
curl -X POST http://localhost:8000/walker/create_note \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Important Meeting",
    "content": "Discuss project timeline",
    "author": "Bob",
    "priority": 3,
    "tags": ["work", "meeting"]
  }'

# Invalid request - priority out of range
curl -X POST http://localhost:8000/walker/create_note \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Test",
    "content": "Test content",
    "author": "Bob",
    "priority": 10
  }'
```

!!! tip "Automatic Type Validation"
    Jac validates types before your walker code runs. Invalid types return HTTP 400 automatically.

---

## REST Patterns with Walkers

Walkers naturally map to REST operations, creating intuitive API patterns for common CRUD operations.

### Complete Notebook API

!!! example "Full CRUD Notebook System"

    ```jac
    import from uuid { uuid4 }

    # complete_notebook.jac
    node Note {
        has title: str;
        has content: str;
        has author: str;
        has priority: int = 1;
        has created_at: str = "2024-01-15";
        has id: str;
    }

    # CREATE - Add new note
    walker create_note {
        has title: str;
        has content: str;
        has author: str;
        has priority: int = 1;

        can add_note with `root entry {
            new_note = Note(
                title=self.title, content=self.content,
                author=self.author, priority=self.priority,
                id="note_" + str(uuid4())
            );
            here ++> new_note;
            report {"message": "Note created", "id": new_note.id};
        }
    }

    # READ - Get all notes
    walker list_notes {
        can get_all_notes with `root entry {
            all_notes = [-->(`?Note)];
            report {
                "notes": [
                    {
                        "id": n.id,
                        "title": n.title,
                        "author": n.author,
                        "priority": n.priority
                    }
                    for n in all_notes
                ],
                "total": len(all_notes)
            };
        }
    }

    # READ - Get specific note
    walker get_note {
        has note_id: str;

        can fetch_note with `root entry {
            target_note = [-->(`?Note)](?id == self.note_id);

            if target_note {
                note = target_note[0];
                report {
                    "note": {
                        "id": note.id,
                        "title": note.title,
                        "content": note.content,
                        "author": note.author,
                        "priority": note.priority
                    }
                };
            } else {
                report {"error": "Note not found"};
            }
        }
    }

    # UPDATE - Modify note
    walker update_note {
        has note_id: str;
        has title: str = "";
        has content: str = "";
        has priority: int = 0;

        can modify_note with `root entry {
            target_note = [-->(`?Note)](?id == self.note_id);

            if target_note {
                note = target_note[0];

                # Update only provided fields
                if self.title {
                    note.title = self.title;
                }
                if self.content {
                    note.content = self.content;
                }
                if self.priority > 0 {
                    note.priority = self.priority;
                }

                report {"message": "Note updated", "id": note.id};
            } else {
                report {"error": "Note not found"};
            }
        }
    }

    # DELETE - Remove note
    walker delete_note {
        has note_id: str;

        can remove_note with `root entry {
            target_note = [-->(`?Note)](?id == self.note_id);

            if target_note {
                note = target_note[0];
                # Delete the node and its connections
                del note;
                report {"message": "Note deleted", "id": self.note_id};
            } else {
                report {"error": "Note not found"};
            }
        }
    }
    ```

### API Usage Examples

```bash
# Create a note
curl -X POST http://localhost:8000/walker/create_note \
  -H "Content-Type: application/json" \
  -d '{"title": "Shopping List", "content": "Milk, Bread, Eggs", "author": "Alice"}'

# List all notes
curl -X POST http://localhost:8000/walker/list_notes \
  -H "Content-Type: application/json" \
  -d '{}'

# Get specific note (replace with actual ID)
curl -X POST http://localhost:8000/walker/get_note \
  -H "Content-Type: application/json" \
  -d '{"note_id": "note_123"}'

# Update a note
curl -X POST http://localhost:8000/walker/update_note \
  -H "Content-Type: application/json" \
  -d '{"note_id": "note_123", "priority": 5}'

# Delete a note
curl -X POST http://localhost:8000/walker/delete_note \
  -H "Content-Type: application/json" \
  -d '{"note_id": "note_123"}'
```

---

## Shared Notebook with Permissions

Let's add basic permission checking to demonstrate multi-user patterns:

!!! example "Notebook with User Permissions"
    ```jac
    import from uuid { uuid4 }

    # shared_notebook.jac
    node Note {
        has title: str;
        has content: str;
        has author: str;
        has shared_with: list[str] = [];
        has is_public: bool = false;
        has id: str;
    }

    walker create_shared_note {
        has title: str;
        has content: str;
        has author: str;
        has shared_with: list[str] = [];
        has is_public: bool = false;

        can create_note with `root entry {
            new_note = Note(
                title=self.title,
                content=self.content,
                author=self.author,
                shared_with=self.shared_with,
                is_public=self.is_public,
                id="note_" + str(uuid4())
            );
            here ++> new_note;

            report {
                "message": "Shared note created",
                "id": new_note.id,
                "shared_with": len(self.shared_with),
                "is_public": self.is_public
            };
        }
    }

    walker get_user_notes {
        has user: str;

        can fetch_accessible_notes with `root entry {
            all_notes = [-->(`?Note)];
            accessible_notes = [];

            for note in all_notes {
                # User can access if they're the author, note is public,
                # or they're in the shared_with list
                if (note.author == self.user or
                    note.is_public or
                    self.user in note.shared_with) {
                    accessible_notes.append({
                        "id": note.id,
                        "title": note.title,
                        "author": note.author,
                        "is_mine": note.author == self.user
                    });
                }
            }

            report {
                "user": self.user,
                "notes": accessible_notes,
                "count": len(accessible_notes)
            };
        }
    }

    walker share_note {
        has note_id: str;
        has user: str;
        has share_with: str;

        can add_share_permission with `root entry {
            target_note = [-->(`?Note)](?id == self.note_id);

            if target_note {
                note = target_note[0];

                # Only author can share
                if note.author == self.user {
                    if self.share_with not in note.shared_with {
                        note.shared_with.append(self.share_with);
                    }

                    report {
                        "message": f"Note shared with {self.share_with}",
                        "shared_with": note.shared_with
                    };
                } else {
                    report {"error": "Only author can share notes"};
                }
            } else {
                report {"error": "Note not found"};
            }
        }
    }
    ```

### Testing Shared Notebook

```bash
# Create a shared note
curl -X POST http://localhost:8000/walker/create_shared_note \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Team Meeting Notes",
    "content": "Discussed project milestones",
    "author": "Alice",
    "shared_with": ["Bob", "Charlie"],
    "is_public": false
  }'

# Get notes for a user
curl -X POST http://localhost:8000/walker/get_user_notes \
  -H "Content-Type: application/json" \
  -d '{"user": "Bob"}'

# Share note with another user
curl -X POST http://localhost:8000/walker/share_note \
  -H "Content-Type: application/json" \
  -d '{
    "note_id": "note_123",
    "user": "Alice",
    "share_with": "David"
  }'
```

---

## Best Practices

!!! summary "API Development Guidelines"
    - **Use descriptive walker names**: Names become part of your API surface
    - **Validate input early**: Check parameters before processing
    - **Provide clear error messages**: Help API consumers understand failures
    - **Keep walkers focused**: Each walker should have a single responsibility
    - **Use consistent response formats**: Standardize success and error responses
    - **Document with types**: Type annotations serve as API documentation

## Key Takeaways

!!! summary "What We've Learned"
    **Automatic API Generation:**

    - **Zero configuration**: Walkers become REST endpoints without setup
    - **Type-safe parameters**: Request validation handled automatically
    - **Natural REST patterns**: CRUD operations map intuitively to walker semantics
    - **Instant deployment**: Deploy APIs with a single command

    **Request/Response Handling:**

    - **JSON mapping**: Request bodies automatically map to walker attributes
    - **Response formatting**: Walker reports become structured JSON responses
    - **Parameter validation**: Type system validates requests before execution
    - **Error handling**: Built-in patterns for graceful error responses

    **Shared Data Applications:**

    - **Persistent nodes**: Data survives between API requests
    - **Graph operations**: Complex data relationships handled naturally
    - **User permissions**: Implement access control with business logic
    - **Multi-user patterns**: Support shared and private data seamlessly

    **Development Benefits:**

    - **Focus on logic**: No HTTP handling, routing, or serialization code
    - **Type safety**: Compile-time validation for API contracts
    - **Rapid iteration**: Changes take effect immediately
    - **Scale-ready**: Same code works from development to production

!!! tip "Try It Yourself"
    Build sophisticated APIs by adding:
    - Authentication and user sessions
    - File upload and processing capabilities
    - Real-time notifications and webhooks
    - Integration with external services

    Remember: Every walker you create automatically becomes an API endpoint when deployed!

---

*Ready to learn about persistent data? Continue to [Chapter 13: Persistence and the Root Node](chapter_13.md)!*

# Chapter 13: Persistence and the Root Node

In this chapter, you will learn about one of Jac's most powerful features: automatic persistence. We will build a simple counter application to show you how Jac can automatically save your program's state when running as a service, eliminating the need for a traditional database setup.

!!! info "What You'll Learn"
    -   What "persistence" means and why it's a common challenge.
    - How Jac automatically saves your data when using the jac serve command.
    - The role of the root node as the anchor for all saved data.
    - How to build stateful applications that remember information between API calls.

Imagine you have a simple calculator application. When you run the program, it works perfectly. But as soon as you close the terminal, it forgets everything. The next time you run it, it starts from scratch. Its memory is temporary; it only exists while the program is running.

Persistence is the solution to this problem. Persistence is the ability of an application to save its state (its data, variables, and objects) so that the information survives after the program has been closed.

In traditional programming, achieving persistence is a significant task. You typically need to,

    1. Set up an external database (like PostgreSQL or MongoDB).
    2. Write code to connect your application to the database.
    3. Write "save" logic to convert your objects into a format the database can store.
    4. Write "load" logic to retrieve that data and turn it back into objects when your application starts again.

This is a lot of extra work just to make your application remember things.

---

## What is Automatic Persistence?

Jac is designed to make this process effortless. When you run your Jac program as a service (using the jac serve command, persistence becomes an automatic feature of the language.

!!! warning "Persistence Requirements"
    - **Database Backend**: Persistence requires `jac serve` with a configured database
    - **Service Mode**: `jac run` executions are stateless and don't persist data
    - **Root Connection**: Nodes must be connected to root to persist
    - **API Context**: Persistence works within the context of API endpoints

!!! success "Persistence Benefits"
    - **Zero Configuration**: No manual database schema or ORM setup
    - **Automatic State**: Data persists without explicit save/load operations
    - **Graph Integrity**: Relationships between nodes are maintained
    - **Type Safety**: Persistent data maintains type information
    - **Instant Recovery**: Services resume exactly where they left off

### Traditional vs Jac Persistence

!!! example "Persistence Comparison"
    === "Traditional Approach"
        ```python
        # counter_api.py - Manual database setup required
        from flask import Flask, jsonify, request
        import sqlite3
        import os

        app = Flask(__name__)

        class Counter:
            def __init__(self):
                self.db_path = "counter.db"
                self.init_db()

            def init_db(self):
                conn = sqlite3.connect(self.db_path)
                cursor = conn.cursor()
                cursor.execute('''
                    CREATE TABLE IF NOT EXISTS counter (
                        id INTEGER PRIMARY KEY,
                        value INTEGER
                    )
                ''')
                cursor.execute('SELECT value FROM counter WHERE id = 1')
                if not cursor.fetchone():
                    cursor.execute('INSERT INTO counter (id, value) VALUES (1, 0)')
                conn.commit()
                conn.close()

            def get_value(self):
                conn = sqlite3.connect(self.db_path)
                cursor = conn.cursor()
                cursor.execute('SELECT value FROM counter WHERE id = 1')
                value = cursor.fetchone()[0]
                conn.close()
                return value

            def increment(self):
                conn = sqlite3.connect(self.db_path)
                cursor = conn.cursor()
                cursor.execute('UPDATE counter SET value = value + 1 WHERE id = 1')
                conn.commit()
                conn.close()
                return self.get_value()

        counter = Counter()

        @app.route('/counter')
        def get_counter():
            return jsonify({"value": counter.get_value()})

        @app.route('/counter/increment', methods=['POST'])
        def increment_counter():
            new_value = counter.increment()
            return jsonify({"value": new_value})

        if __name__ == "__main__":
            app.run(debug=True)
        ```

    === "Jac Automatic Persistence"
        ```jac
        # main.jac - No database setup needed
        node Counter {
            has value: int = 0;

            def increment() -> int {
                self.value += 1;
                return self.value;
            }

            def get_value() -> int {
                return self.value;
            }
        }

        walker get_counter {
            obj __specs__ {
                static has auth: bool = False;
            }

            can get_counter_endpoint with `root entry {
                counter_nodes = [root --> Counter];


                if not counter_nodes {
                    counter = Counter();
                    root ++> counter;
                } else {
                    counter = counter_nodes[0];
                }

                report {"value": counter.get_value()};
            }
        }

        walker increment_counter {
            obj __specs__ {
                static has auth: bool = False;
            }

            can increment_counter_endpoint with `root entry {
                counter_nodes = [root --> Counter];
                if not counter_nodes {
                    counter = Counter();
                    root ++> counter;
                } else {
                    counter = counter_nodes[0];
                }
                new_value = counter.increment();
                report {"value": new_value};
            }
        }
        ```

---

## Setting Up a Jac Cloud Project

To demonstrate persistence, we need to create a proper jac-cloud project structure:

!!! example "Project Structure"
    ```
    counter-app/
    ├── .env                 # Environment configuration
    ├── main.jac            # Main application logic
    ├── server.py           # Optional custom server setup
    └── requirements.txt    # Python dependencies
    ```

Let's create our counter application:

!!! example "Complete Counter Project"
    === ".env"
        ```bash
        # .env - Database configuration
        DATABASE_URL=sqlite:///./app.db
        SECRET_KEY=your-secret-key-here
        ```

    === "main.jac"
        ```jac
        # main.jac
        node Counter {
            has value: int = 0;
            has created_at: str;

            can increment() -> int {
                self.value += 1;
                return self.value;
            }

            can reset() -> int {
                self.value = 0;
                return self.value;
            }
        }

        walker get_counter {
            can get_counter_endpoint with `root entry {
                counter_nodes = [root --> Counter];
                if not counter_nodes {
                    counter = Counter(created_at="2024-01-15");
                    root ++> counter;
                    report {"value": 0, "status": "created"};
                } else {
                    counter = counter_nodes[0];
                    report {"value": counter.value, "status": "existing"};
                }
            }
        }

        walker increment_counter {
            can increment_counter_endpoint with `root entry {
                counter_nodes = [root --> Counter];
                if not counter_nodes {
                    counter = Counter(created_at="2024-01-15");
                    root ++> counter;
                } else {
                    counter = counter_nodes[0];
                }
                new_value = counter.increment();
                report {"value": new_value, "previous": new_value - 1};
            }
        }

        walker reset_counter {
            can reset_counter_endpoint with `root entry {
                counter_nodes = [root --> Counter];
                if counter_nodes {
                    counter = counter_nodes[0];
                    counter.reset();
                    report {"value": 0, "status": "reset"};
                } else {
                    report {"value": 0, "status": "no_counter_found"};
                }
            }
        }
        ```

    === "requirements.txt"
        ```
        jaclang
        fastapi
        uvicorn
        python-dotenv
        ```

---

## The Root Node Concept

The root node is Jac's fundamental concept for persistent data organization. When running with `jac serve`, every request has access to a special `root` node that serves as the entry point for all persistent graph structures.

### Understanding Root Node

!!! info "Root Node Properties"
    - **Request Context**: Available in every API request when using jac serve
    - **Persistence Gateway**: Starting point for all persistent data
    - **Graph Anchor**: All persistent nodes must be reachable from root
    - **Automatic Creation**: Exists automatically with database backend
    - **Transaction Boundary**: Changes persist at the end of each request

### Running the Service

```bash
# Navigate to project directory
cd counter-app

# Install dependencies
pip install -r requirements.txt

# Start the service with database persistence
jac serve main.jac

# Service starts on http://localhost:8000
# API documentation available at http://localhost:8000/docs
```

### Testing Persistence

```bash
# First request - Create counter
curl -X POST http://localhost:8000/walker/get_counter \
  -H "Content-Type: application/json" \
  -d '{}'
# Response: {"returns": [{"value": 0, "status": "created"}]}

# Increment the counter
curl -X POST http://localhost:8000/walker/increment_counter \
  -H "Content-Type: application/json" \
  -d '{}'
# Response: {"returns": [{"value": 1, "previous": 0}]}

# Increment again
curl -X POST http://localhost:8000/walker/increment_counter \
  -H "Content-Type: application/json" \
  -d '{}'
# Response: {"returns": [{"value": 2, "previous": 1}]}

# Check counter value
curl -X POST http://localhost:8000/walker/get_counter \
  -H "Content-Type: application/json" \
  -d '{}'
# Response: {"returns": [{"value": 2, "status": "existing"}]}

# Restart the service (Ctrl+C, then jac serve main.jac again)

# Counter value persists after restart
curl -X POST http://localhost:8000/walker/get_counter \
  -H "Content-Type: application/json" \
  -d '{}'
# Response: {"returns": [{"value": 2, "status": "existing"}]}
```

!!! tip "Persistence in Action"
    Notice how the counter value persists between requests and even service restarts when using `jac serve` with a database!

---

## State Consistency

Jac maintains state consistency through its graph-based persistence model when running as a service. All connected nodes and their relationships are automatically maintained across requests and service restarts.

### Enhanced Counter with History

Let's enhance our counter to track increment history:

!!! example "Counter with History Tracking"
    ```jac
    # main.jac - Enhanced with history
    import from datetime { datetime }

    node Counter {
        has created_at: str;
        has value: int = 0;

        def increment() -> int {
            old_value = self.value;
            self.value += 1;

            # Create history entry
            history = HistoryEntry(
                timestamp=str(datetime.now()),
                old_value=old_value,
                new_value=self.value
            );
            self ++> history;
            return self.value;
        }

        def get_history() -> list[dict] {
            history_nodes = [self --> HistoryEntry];
            return [
                {
                    "timestamp": h.timestamp,
                    "old_value": h.old_value,
                    "new_value": h.new_value
                }
                for h in history_nodes
            ];
        }
    }

    node HistoryEntry {
        has timestamp: str;
        has old_value: int = 0;
        has new_value: int = 0;
    }

    walker get_counter_with_history {
        obj __specs__ {
            static has auth: bool = False;
        }

        can get_counter_with_history_endpoint with `root entry {
            counter_nodes = [root --> Counter];
            if not counter_nodes {
                counter = Counter(created_at=str(datetime.now()));
                root ++> counter;
                report {
                    "value": 0,
                    "status": "created",
                    "history": []
                };
            } else {
                counter = counter_nodes[0];
                report {
                    "value": counter.value,
                    "status": "existing",
                    "history": counter.get_history()
                };
            }
        }
    }

    walker increment_with_history {
        obj __specs__ {
            static has auth: bool = False;
        }

        can increment_with_history_endpoint with `root entry {
            counter_nodes = [root --> Counter];
            if not counter_nodes {
                counter = Counter(created_at=str(datetime.now()));
                root ++> counter;
            } else {
                counter = counter_nodes[0];
            }

            new_value = counter.increment();
            report {
                "value": new_value,
                "history": counter.get_history()
            };
        }
    }
    ```

### Testing History Persistence

```bash
# Start fresh service
jac serve main.jac

# Multiple increments to build history
curl -X POST http://localhost:8000/walker/increment_with_history \
  -H "Content-Type: application/json" \
  -d '{}'

curl -X POST http://localhost:8000/walker/increment_with_history \
  -H "Content-Type: application/json" \
  -d '{}'

curl -X POST http://localhost:8000/walker/increment_with_history \
  -H "Content-Type: application/json" \
  -d '{}'

# Check counter with complete history
curl -X POST http://localhost:8000/walker/get_counter_with_history \
  -H "Content-Type: application/json" \
  -d '{}'
# Response includes value and complete history array

# Restart service - history persists
# jac serve main.jac (after restart)
curl -X POST http://localhost:8000/walker/get_counter_with_history \
  -H "Content-Type: application/json" \
  -d '{}'
# All history entries remain intact
```

---

## Building Stateful Applications

The automatic persistence enables building sophisticated stateful applications. Let's create a multi-counter management system:

!!! example "Multi-Counter Management System"
    ```jac
    # main.jac - Multi-counter system
    import from datetime { datetime }

    node CounterManager {
        has created_at: str;

        def create_counter(name: str) -> dict {
            # Check if counter already exists
            existing = [self --> Counter](?name == name);
            if existing {
                return {"status": "exists", "counter": existing[0].name};
            }

            new_counter = Counter(name=name, value=0);
            self ++> new_counter;
            return {"status": "created", "counter": name};
        }

        def list_counters() -> list[dict] {
            counters = [self --> Counter];
            return [
                {"name": c.name, "value": c.value}
                for c in counters
            ];
        }

        def get_total() -> int {
            counters = [self --> Counter];
            return sum([c.value for c in counters]);
        }
    }

    node Counter {
        has name: str;
        has value: int = 0;

        def increment(amount: int = 1) -> int {
            self.value += amount;
            return self.value;
        }
    }

    walker create_counter {
        has name: str;

        obj __specs__ {
            static has auth: bool = False;
        }

        can create_counter_endpoint with `root entry {
            manager_nodes = [root --> CounterManager];
            if not manager_nodes {
                manager = CounterManager(created_at=str(datetime.now()));
                root ++> manager;
            } else {
                manager = manager_nodes[0];
            }

            result = manager.create_counter(self.name);
            report result;
        }
    }

    walker increment_named_counter {
        has name: str;
        has amount: int = 1;

        obj __specs__ {
            static has auth: bool = False;
        }

        can increment_named_counter_endpoint with `root entry {
            manager_nodes = [root --> CounterManager];
            if not manager_nodes {
                report {"error": "No counter manager found"};
                return;
            }

            manager = manager_nodes[0];
            counters = [manager --> Counter](?name == self.name);

            if not counters {
                report {"error": f"Counter {self.name} not found"};
                return;
            }

            counter = counters[0];
            new_value = counter.increment(self.amount);
            report {"name": self.name, "value": new_value};
        }
    }

    walker get_all_counters {
        obj __specs__ {
            static has auth: bool = False;
        }

        can get_all_counters_endpoint with `root entry {
            manager_nodes = [root --> CounterManager];
            if not manager_nodes {
                report {"counters": [], "total": 0};
                return;
            }

            manager = manager_nodes[0];
            report {
                "counters": manager.list_counters(),
                "total": manager.get_total()
            };
        }
    }
    ```

### API Usage Examples

```bash
# Create multiple counters
curl -X POST "http://localhost:8000/walker/create_counter" \
     -H "Content-Type: application/json" \
     -d '{"name": "page_views"}'

curl -X POST "http://localhost:8000/walker/create_counter" \
     -H "Content-Type: application/json" \
     -d '{"name": "user_signups"}'

# Increment specific counters
curl -X POST "http://localhost:8000/walker/increment_named_counter" \
     -H "Content-Type: application/json" \
     -d '{"name": "page_views", "amount": 5}'

curl -X POST "http://localhost:8000/walker/increment_named_counter" \
     -H "Content-Type: application/json" \
     -d '{"name": "user_signups", "amount": 2}'

# View all counters
curl -X POST http://localhost:8000/walker/get_all_counters \
  -H "Content-Type: application/json" \
  -d '{}'
# Response: {"returns": [{"counters": [{"name": "page_views", "value": 5}, {"name": "user_signups", "value": 2}], "total": 7}]}
```

---

## Best Practices

!!! summary "Persistence Guidelines"
    - **Service mode only**: Use `jac serve` for persistent applications, not `jac run`
    - **Connect to root**: All persistent data must be reachable from root
    - **Initialize gracefully**: Check for existing data before creating new instances
    - **Use proper IDs**: Generate unique identifiers for nodes that need them
    - **Plan for concurrency**: Consider multiple users accessing the same data
    - **Database configuration**: Set up proper database connections for production

## Key Takeaways

!!! summary "What We've Learned"
    **Persistence Fundamentals:**

    - **Service requirement**: Persistence only works with `jac serve` and database backends
    - **Root connection**: All persistent nodes must be connected to the root node
    - **Automatic behavior**: Data persists without explicit save/load operations
    - **Request isolation**: Each API request has access to the same persistent graph

    **Root Node Concept:**

    - **Graph anchor**: Starting point for all persistent data structures
    - **Request context**: Available automatically in every API endpoint
    - **Transaction boundary**: Changes persist at the end of each successful request
    - **State consistency**: Maintains graph integrity across service restarts

    **State Management:**

    - **Automatic persistence**: Connected nodes survive service restarts
    - **Graph integrity**: Relationships between nodes are maintained
    - **Type preservation**: Node properties retain their types across persistence
    - **Concurrent access**: Multiple requests can safely access the same data

    **Development Patterns:**

    - **Initialization checks**: Use filtering to find existing data before creating new
    - **Unique identification**: Generate proper IDs for nodes that need them
    - **Data validation**: Implement business rules at the application level
    - **Error handling**: Graceful handling of missing or invalid data

!!! tip "Try It Yourself"
    Build persistent applications by creating:
    - A todo list API with persistent tasks
    - A blog system with posts and comments
    - An inventory management system
    - A user profile system with preferences

    Remember: Only nodes connected to root (directly or indirectly) will persist when using `jac serve`!

---

*Ready to explore cloud deployment? Continue to [Chapter 14: Jac Cloud Introduction](chapter_14.md)!*

# Chapter 14: Multi-User Architecture and Permissions

In this chapter, we'll explore how to build secure, multi-user applications in Jac Cloud. We'll develop a shared notebook system that demonstrates user isolation, permission systems, and access control strategies through practical examples that evolve throughout the chapter.

!!! info "What You'll Learn"
    - Building secure multi-user applications
    - User isolation and data privacy patterns
    - Permission-based access control
    - Shared data management strategies
    - Security considerations for cloud applications

---

## User Isolation and Permission Systems

Multi-user applications require careful consideration of data access and user permissions. Jac provides built-in patterns for user management that integrate seamlessly with your application logic, allowing you to focus on business rules rather than authentication infrastructure.

!!! success "Multi-User Benefits"
    - **User Context**: Access to user information in walkers
    - **Data Isolation**: Users can only access their authorized data
    - **Flexible Permissions**: Fine-grained access control patterns
    - **Secure by Default**: Application-level security patterns
    - **Shared Data Support**: Controlled sharing between users

### Traditional vs Jac Multi-User Development

!!! example "Multi-User Comparison"
    === "Traditional Approach"
        ```python
        # app.py - Manual user management required
        from flask import Flask, request, jsonify
        from flask_jwt_extended import JWTManager, create_access_token, jwt_required, get_jwt_identity
        from werkzeug.security import generate_password_hash, check_password_hash

        app = Flask(__name__)
        app.config['JWT_SECRET_KEY'] = 'your-secret-key'
        jwt = JWTManager(app)

        # Global storage (in production, use a database)
        users = {}
        notebooks = {}

        @app.route('/register', methods=['POST'])
        def register():
            data = request.get_json()
            username = data.get('username')
            password = data.get('password')

            if username in users:
                return jsonify({'error': 'User already exists'}), 400

            users[username] = {
                'password': generate_password_hash(password),
                'notebooks': []
            }

            return jsonify({'message': 'User created successfully'})

        @app.route('/login', methods=['POST'])
        def login():
            data = request.get_json()
            username = data.get('username')
            password = data.get('password')

            if username not in users or not check_password_hash(users[username]['password'], password):
                return jsonify({'error': 'Invalid credentials'}), 401

            access_token = create_access_token(identity=username)
            return jsonify({'access_token': access_token})

        @app.route('/create_note', methods=['POST'])
        @jwt_required()
        def create_note():
            current_user = get_jwt_identity()
            data = request.get_json()

            # Manual permission checking
            note_id = len(notebooks)
            notebooks[note_id] = {
                'id': note_id,
                'title': data.get('title'),
                'content': data.get('content'),
                'owner': current_user,
                'shared_with': []
            }

            users[current_user]['notebooks'].append(note_id)
            return jsonify({'message': 'Note created', 'id': note_id})

        if __name__ == '__main__':
            app.run()
        ```

    === "Jac Multi-User"
        ```jac
        # shared_notebook.jac - User patterns built-in
        node Note {
            has title: str;
            has content: str;
            has owner: str;
            has shared_with: list[str] = [];
            has created_at: str = "2024-01-15";
        }

        walker create_note {
            has title: str;
            has content: str;
            has owner: str;

            can create_user_note with `root entry {
                # Create note with specified owner
                new_note = Note(
                    title=self.title,
                    content=self.content,
                    owner=self.owner
                );
                here ++> new_note;

                report {
                    "message": "Note created successfully",
                    "id": new_note.id,
                    "owner": new_note.owner
                };
            }
        }

        walker get_my_notes {
            has user_id: str;

            can fetch_user_notes with `root entry {
                # Filter by specified user
                my_notes = [-->(`?Note)](?owner == self.user_id);

                notes_data = [
                    {"id": n.id, "title": n.title, "created_at": n.created_at}
                    for n in my_notes
                ];

                report {"notes": notes_data, "total": len(notes_data)};
            }
        }
        ```

---

## Basic User Authentication

For multi-user applications, you need to implement user identification patterns. Let's start with a simple notebook system that supports multiple users.

### Setting Up User-Aware Notebook

!!! example "User-Isolated Notebook System"
    === "Jac"
        ```jac
        # user_notebook.jac
        import uuid;

        node Note {
            has title: str;
            has content: str;
            has owner: str;
            has is_private: bool = True;
            has id: str = "note_" + str(uuid.uuid4());
        }

        walker create_note {
            has title: str;
            has content: str;
            has owner: str;
            has is_private: bool = True;

            obj __specs__ {
                static has auth: bool = False;
            }

            can add_note with `root entry {
                new_note = Note(
                    title=self.title,
                    content=self.content,
                    owner=self.owner,
                    is_private=self.is_private
                );
                here ++> new_note;

                report {
                    "status": "created",
                    "note_id": new_note.id,
                    "private": new_note.is_private
                };
            }
        }

        walker list_my_notes {
            has user_id: str;

            obj __specs__ {
                static has auth: bool = False;
            }

            can get_user_notes with `root entry {
                # Only get notes owned by specified user
                user_notes = [-->(`?Note)](?owner == self.user_id);

                report {
                    "user": self.user_id,
                    "notes": [
                        {
                            "id": n.id,
                            "title": n.title,
                            "private": n.is_private
                        }
                        for n in user_notes
                    ],
                    "count": len(user_notes)
                };
            }
        }
        ```

    === "Python Equivalent"
        ```python
        # user_notebook.py - Requires manual auth setup
        from flask import Flask, request, jsonify
        from flask_jwt_extended import JWTManager, jwt_required, get_jwt_identity

        app = Flask(__name__)
        app.config['JWT_SECRET_KEY'] = 'secret-key'
        jwt = JWTManager(app)

        notes = []

        @app.route('/create_note', methods=['POST'])
        @jwt_required()
        def create_note():
            current_user = get_jwt_identity()
            data = request.get_json()

            note = {
                'id': len(notes),
                'title': data.get('title'),
                'content': data.get('content'),
                'owner': current_user,
                'is_private': data.get('is_private', True)
            }
            notes.append(note)

            return jsonify({
                'status': 'created',
                'note_id': note['id'],
                'private': note['is_private']
            })

        @app.route('/list_my_notes', methods=['GET'])
        @jwt_required()
        def list_my_notes():
            current_user = get_jwt_identity()
            user_notes = [n for n in notes if n['owner'] == current_user]

            return jsonify({
                'user': current_user,
                'notes': [
                    {'id': n['id'], 'title': n['title'], 'private': n['is_private']}
                    for n in user_notes
                ],
                'count': len(user_notes)
            })
        ```

### Deploying and Testing

Deploy your user-aware application:

```bash
jac serve user_notebook.jac
```

### Testing User Authentication

```bash
# Create a note for Alice
curl -X POST http://localhost:8000/walker/create_note \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Alice Private Note",
    "content": "Secret content",
    "owner": "alice@example.com"
  }'

# Create a note for Bob
curl -X POST http://localhost:8000/walker/create_note \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Bob Note",
    "content": "Bob content",
    "owner": "bob@example.com"
  }'

# Get Alice's notes only
curl -X POST http://localhost:8000/walker/list_my_notes \
  -H "Content-Type: application/json" \
  -d '{"user_id": "alice@example.com"}'
```

---

## Shared Data Patterns

Multi-user applications often need controlled sharing of data between users. Let's enhance our notebook to support sharing notes with specific users.

### Note Sharing Implementation

!!! example "Shared Notebook with Permissions"
    ```jac
    # shared_permissions.jac
    import uuid;

    node Note {
        has title: str;
        has content: str;
        has owner: str;
        has shared_with: list[str] = [];
        has is_public: bool = False;
        has permissions: dict = {"read": True, "write": False};
        has id: str = "note_" + str(uuid.uuid4());
    }

    walker create_note {
        has title: str;
        has content: str;
        has owner: str;
        has is_public: bool = False;

        obj __specs__ {
            static has auth: bool = False;
        }

        can add_note with `root entry {
            new_note = Note(
                title=self.title,
                content=self.content,
                owner=self.owner,
                is_public=self.is_public
            );
            here ++> new_note;

            report {
                "status": "created",
                "note_id": new_note.id,
                "public": new_note.is_public
            };
        }
    }

    walker share_note {
        has note_id: str;
        has current_user: str;
        has target_user: str;
        has permission_level: str = "read";  # "read" or "write"

        obj __specs__ {
            static has auth: bool = False;
        }

        can add_sharing_permission with `root entry {
            target_note = [-->(`?Note)](?id == self.note_id);

            if not target_note {
                report {"error": "Note not found"};
                return;
            }

            note = target_note[0];

            # Only owner can share notes
            if note.owner != self.current_user {
                report {"error": "Only note owner can share"};
                return;
            }

            # Add user to shared list if not already there
            if self.target_user not in note.shared_with {
                note.shared_with.append(self.target_user);
            }

            report {
                "message": f"Note shared with {self.target_user}",
                "permission": self.permission_level,
                "shared_count": len(note.shared_with)
            };
        }
    }

    walker get_accessible_notes {
        has user_id: str;

        obj __specs__ {
            static has auth: bool = False;
        }

        can fetch_all_accessible with `root entry {
            all_notes = [-->(`?Note)];
            accessible_notes = [];

            for note in all_notes {
                # User can access if:
                # 1. They own it
                # 2. It's shared with them
                # 3. It's public
                if (note.owner == self.user_id or
                    self.user_id in note.shared_with or
                    note.is_public) {

                    accessible_notes.append({
                        "id": note.id,
                        "title": note.title,
                        "owner": note.owner,
                        "is_mine": note.owner == self.user_id,
                        "access_type": "owner" if note.owner == self.user_id
                                    else ("shared" if self.user_id in note.shared_with
                                        else "public")
                    });
                }
            }

            report {
                "user": self.user_id,
                "accessible_notes": accessible_notes,
                "total": len(accessible_notes)
            };
        }
    }
    ```

### Testing Note Sharing

```bash
# Alice creates a note
curl -X POST http://localhost:8000/walker/create_note \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Team Project",
    "content": "Project details",
    "owner": "alice@example.com"
  }'

# Alice shares note with Bob
curl -X POST http://localhost:8000/walker/share_note \
  -H "Content-Type: application/json" \
  -d '{
    "note_id": "note_123",
    "current_user": "alice@example.com",
    "target_user": "bob@example.com"
  }'

# Bob views accessible notes
curl -X POST http://localhost:8000/walker/get_accessible_notes \
  -H "Content-Type: application/json" \
  -d '{"user_id": "bob@example.com"}'
```

---

## Security Considerations

When building multi-user systems, security must be a primary concern. Application-level security patterns are essential for protecting user data.

### Secure Data Access Patterns

!!! example "Security-First Note Access"
    ```jac
    # rbac_notebook.jac
    enum Role {
        VIEWER = "viewer",
        EDITOR = "editor",
        ADMIN = "admin"
    }

    node UserProfile {
        has email: str;
        has role: Role = Role.VIEWER;
        has created_at: str = "2024-01-15";
    }

    node Note {
        has title: str;
        has content: str;
        has owner: str;
        has required_role: Role = Role.VIEWER;
        has is_sensitive: bool = False;
    }

    walker check_user_role {
        has user_id: str;

        obj __specs__ {
            static has auth: bool = False;
        }

        can get_current_user_role with `root entry {
            user_profile = [-->(`?UserProfile)](?email == self.user_id);

            if user_profile {
                current_role = user_profile[0].role;
            } else {
                # Create default profile for new user
                new_profile = UserProfile(email=self.user_id);
                here ++> new_profile;
                current_role = Role.VIEWER;
            }

            report {"user": self.user_id, "role": current_role.value};
        }
    }

    walker create_role_based_note {
        has title: str;
        has content: str;
        has owner: str;
        has required_role: str = "viewer";
        has is_sensitive: bool = False;

        obj __specs__ {
            static has auth: bool = False;
        }

        can create_with_role_check with `root entry {
            # Get user's role
            user_profile = [-->(`?UserProfile)](?email == self.owner);

            if not user_profile {
                report {"error": "User profile not found"};
                return;
            }

            user_role = user_profile[0].role;

            # Check if user can create sensitive notes
            if self.is_sensitive and user_role == Role.VIEWER {
                report {"error": "Insufficient permissions for sensitive content"};
                return;
            }

            new_note = Note(
                title=self.title,
                content=self.content,
                owner=self.owner,
                required_role=Role(self.required_role),
                is_sensitive=self.is_sensitive
            );
            here ++> new_note;

            report {
                "message": "Note created with role requirements",
                "id": new_note.id,
                "required_role": self.required_role
            };
        }
    }

    walker get_role_filtered_notes {
        has user_id: str;

        obj __specs__ {
            static has auth: bool = False;
        }

        can fetch_accessible_by_role with `root entry {
            # Get user's role
            user_profile = [-->(`?UserProfile)](?email == self.user_id);

            if not user_profile {
                report {"notes": [], "message": "No user profile found"};
                return;
            }

            user_role = user_profile[0].role;
            all_notes = [-->(`?Note)];
            accessible_notes = [];

            for note in all_notes {
                # Check if user meets role requirement
                can_access = (
                    note.owner == self.user_id or  # Always access own notes
                    (user_role == Role.ADMIN) or  # Admins see everything
                    (user_role == Role.EDITOR and note.required_role != Role.ADMIN) or
                    (user_role == Role.VIEWER and note.required_role == Role.VIEWER)
                );

                if can_access {
                    accessible_notes.append({
                        "id": note.id,
                        "title": note.title,
                        "owner": note.owner,
                        "required_role": note.required_role.value,
                        "is_sensitive": note.is_sensitive
                    });
                }
            }

            report {
                "user_role": user_role.value,
                "notes": accessible_notes,
                "total": len(accessible_notes)
            };
        }
    }
    ```

!!! warning "Security Best Practices"
    - **Always Verify Access**: Check user permissions before any data operation
    - **Validate Input**: Sanitize all user input to prevent injection attacks
    - **Principle of Least Privilege**: Grant minimum necessary permissions
    - **Audit Access**: Log sensitive operations for security monitoring
    - **Secure Defaults**: Make restrictive permissions the default

---

## Access Control Strategies

Different applications require different access control models. Let's implement a role-based access control system for our notebook.

### Role-Based Access Control

!!! example "RBAC Notebook System"
    ```jac
    # rbac_notebook.jac
    enum Role {
        VIEWER = "viewer",
        EDITOR = "editor",
        ADMIN = "admin"
    }

    node UserProfile {
        has email: str;
        has role: Role = Role.VIEWER;
        has created_at: str = "2024-01-15";
    }

    node Note {
        has title: str;
        has content: str;
        has owner: str;
        has required_role: Role = Role.VIEWER;
        has is_sensitive: bool = False;
    }

    walker check_user_role {
        has user_id: str;

        can get_current_user_role with `root entry {
            user_profile = [-->(`?UserProfile)](?email == self.user_id);

            if user_profile {
                current_role = user_profile[0].role;
            } else {
                # Create default profile for new user
                new_profile = UserProfile(email=self.user_id);
                here ++> new_profile;
                current_role = Role.VIEWER;
            }

            report {"user": self.user_id, "role": current_role.value};
        }
    }

    walker create_role_based_note {
        has title: str;
        has content: str;
        has owner: str;
        has required_role: str = "viewer";
        has is_sensitive: bool = False;

        can create_with_role_check with `root entry {
            # Get user's role
            user_profile = [-->(`?UserProfile)](?email == self.owner);

            if not user_profile {
                report {"error": "User profile not found"};
                return;
            }

            user_role = user_profile[0].role;

            # Check if user can create sensitive notes
            if self.is_sensitive and user_role == Role.VIEWER {
                report {"error": "Insufficient permissions for sensitive content"};
                return;
            }

            new_note = Note(
                title=self.title,
                content=self.content,
                owner=self.owner,
                required_role=Role(self.required_role),
                is_sensitive=self.is_sensitive
            );
            here ++> new_note;

            report {
                "message": "Note created with role requirements",
                "id": new_note.id,
                "required_role": self.required_role
            };
        }
    }

    walker get_role_filtered_notes {
        has user_id: str;

        can fetch_accessible_by_role with `root entry {
            # Get user's role
            user_profile = [-->(`?UserProfile)](?email == self.user_id);

            if not user_profile {
                report {"notes": [], "message": "No user profile found"};
                return;
            }

            user_role = user_profile[0].role;
            all_notes = [-->(`?Note)];
            accessible_notes = [];

            for note in all_notes {
                # Check if user meets role requirement
                can_access = (
                    note.owner == self.user_id or  # Always access own notes
                    (user_role == Role.ADMIN) or  # Admins see everything
                    (user_role == Role.EDITOR and note.required_role != Role.ADMIN) or
                    (user_role == Role.VIEWER and note.required_role == Role.VIEWER)
                );

                if can_access {
                    accessible_notes.append({
                        "id": note.id,
                        "title": note.title,
                        "owner": note.owner,
                        "required_role": note.required_role.value,
                        "is_sensitive": note.is_sensitive
                    });
                }
            }

            report {
                "user_role": user_role.value,
                "notes": accessible_notes,
                "total": len(accessible_notes)
            };
        }
    }
    ```

### Testing Role-Based Access

```bash
# Check user role
curl -X POST http://localhost:8000/walker/check_user_role \
  -H "Content-Type: application/json" \
  -d '{"user_id": "alice@example.com"}'

# Create a note requiring editor role
curl -X POST http://localhost:8000/walker/create_role_based_note \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Editor Note",
    "content": "Only editors can see this",
    "owner": "alice@example.com",
    "required_role": "editor",
    "is_sensitive": true
  }'

# Get notes filtered by role
curl -X POST http://localhost:8000/walker/get_role_filtered_notes \
  -H "Content-Type: application/json" \
  -d '{"user_id": "alice@example.com"}'
```

---

## Best Practices

!!! summary "Multi-User Development Guidelines"
    - **Always validate access**: Check user permissions before any data operation
    - **Use consistent user identification**: Establish clear patterns for user IDs
    - **Implement graceful sharing**: Make sharing intuitive and secure
    - **Audit sensitive operations**: Log important user actions for security
    - **Design for privacy**: Default to private data with explicit sharing
    - **Test permission scenarios**: Verify access control works as expected

## Key Takeaways

!!! summary "What We've Learned"
    **Multi-User Patterns:**

    - **User identification**: Implement user context in walker parameters
    - **Data isolation**: Filter data based on ownership and permissions
    - **Permission systems**: Multiple access control strategies for different needs
    - **Shared data management**: Controlled sharing between users with fine-grained permissions

    **Security Considerations:**

    - **Access validation**: Always verify user permissions before data operations
    - **Default privacy**: Make restrictive permissions the default setting
    - **Input validation**: Sanitize all user input to prevent security issues
    - **Audit trails**: Log sensitive operations for security monitoring

    **Application Architecture:**

    - **Role-based access**: Implement hierarchical permission systems
    - **Flexible sharing**: Support various sharing patterns for different use cases
    - **User profiles**: Manage user information and preferences
    - **Data ownership**: Clear patterns for who can access and modify data

    **Development Benefits:**

    - **Built-in isolation**: Graph filtering provides natural data separation
    - **Flexible permissions**: Implement custom access control with business logic
    - **Scalable patterns**: Multi-user code scales automatically with Jac Cloud
    - **Type safety**: User permissions validated through the type system

!!! tip "Try It Yourself"
    Build multi-user systems by adding:
    - Team-based collaboration features
    - Real-time notifications for shared data changes
    - Advanced permission hierarchies with groups and roles
    - Activity feeds showing user actions

    Remember: Always validate user permissions before any data operation!

---

*Ready to learn about advanced cloud features? Continue to [Chapter 16: Advanced Jac Cloud Features](chapter_15.md)!*

# Chapter 15: Advanced Jac Cloud Features

In this chapter, we'll explore advanced Jac Cloud capabilities that enable configuration management, monitoring, and external integrations. We'll build a comprehensive chat room system that demonstrates environment configuration, logging, and webhook integration through practical examples.

!!! info "What You'll Learn"
    - Environment variables and application configuration
    - Logging and monitoring capabilities
    - Webhook integration for external services
    - Advanced deployment patterns
    - Performance optimization strategies

---

## Environment Variables and Configuration

Production applications require flexible configuration management. Jac Cloud provides built-in support for environment variables and configuration patterns that work seamlessly across local and cloud deployments.

!!! success "Configuration Benefits"
    - **Environment Isolation**: Different settings for dev, staging, and production
    - **Security**: Sensitive data kept in environment variables
    - **Flexibility**: Runtime configuration without code changes
    - **Cloud Integration**: Automatic configuration injection in cloud environments

### Traditional vs Jac Configuration

!!! example "Configuration Comparison"
    === "Traditional Approach"
        ```python
        # config.py - Manual configuration management
        import os
        from typing import Optional

        class Config:
            def __init__(self):
                self.database_url = os.getenv('DATABASE_URL', 'sqlite:///app.db')
                self.redis_url = os.getenv('REDIS_URL', 'redis://localhost:6379')
                self.secret_key = os.getenv('SECRET_KEY', 'dev-secret')
                self.debug = os.getenv('DEBUG', 'False').lower() == 'true'

                # Validate required settings
                if not self.secret_key or self.secret_key == 'dev-secret':
                    if not self.debug:
                        raise ValueError("SECRET_KEY must be set in production")

        # app.py
        from flask import Flask
        from config import Config

        config = Config()
        app = Flask(__name__)
        app.config.from_object(config)

        @app.route('/chat')
        def chat():
            return f"Chat room (Debug: {config.debug})"
        ```

    === "Jac Configuration"
        ```jac
        # chat_room.jac - Built-in configuration support
        import from os { getenv }

        glob chat_config = {
            "max_users": int(getenv("MAX_CHAT_USERS", "100")),
            "message_limit": int(getenv("MESSAGE_LIMIT", "1000")),
            "room_timeout": int(getenv("ROOM_TIMEOUT", "3600")),
            "debug_mode": getenv("DEBUG", "false").lower() == "true"
        };

        node ChatRoom {
            has name: str;
            has users: list[str] = [];
            has messages: list[dict] = [];
            has created_at: str;

            can add_user(username: str) -> bool {
                if len(self.users) >= chat_config["max_users"] {
                    return False;
                }
                if username not in self.users {
                    self.users.append(username);
                }
                return True;
            }
        }

        walker create_chat_room {
            has room_name: str;

            can setup_room with `root entry {
                new_room = ChatRoom(
                    name=self.room_name,
                    created_at="2024-01-15"
                );
                here ++> new_room;

                report {
                    "room_id": new_room.id,
                    "name": new_room.name,
                    "max_users": chat_config["max_users"]
                };
            }
        }
        ```

### Basic Chat Room Setup

Let's start with a simple chat room that uses environment configuration:

!!! example "Configurable Chat Room"
    === "Jac"
        ```jac
        # simple_chat.jac
        import from os { getenv }
        import from datetime { datetime }

        glob config = {
            "max_rooms": int(getenv("MAX_ROOMS", "10")),
            "max_users_per_room": int(getenv("MAX_USERS_PER_ROOM", "50")),
            "message_history": int(getenv("MESSAGE_HISTORY", "100"))
        };

        node ChatRoom {
            has name: str;
            has users: list[str] = [];
            has message_count: int = 0;
        }

        walker join_room {
            has room_name: str;
            has username: str;

            can join_chat with `root entry {
                # Find or create room
                room = [-->(`?ChatRoom)](?name == self.room_name);

                if not room {
                    # Check room limit
                    total_rooms = len([-->(`?ChatRoom)]);
                    if total_rooms >= config["max_rooms"] {
                        report {"error": "Maximum rooms reached"};
                        return;
                    }

                    room = ChatRoom(name=self.room_name);
                    here ++> room;
                } else {
                    room = room[0];
                }

                # Check user limit
                if len(room.users) >= config["max_users_per_room"] {
                    report {"error": "Room is full"};
                    return;
                }

                # Add user if not already in room
                if self.username not in room.users {
                    room.users.append(self.username);
                }

                report {
                    "room": room.name,
                    "users": room.users,
                    "user_count": len(room.users)
                };
            }
        }
        ```

    === "Python Equivalent"
        ```python
        # simple_chat.py - Requires manual setup
        import os
        from flask import Flask, request, jsonify

        app = Flask(__name__)

        # Configuration
        MAX_ROOMS = int(os.getenv("MAX_ROOMS", "10"))
        MAX_USERS_PER_ROOM = int(os.getenv("MAX_USERS_PER_ROOM", "50"))
        MESSAGE_HISTORY = int(os.getenv("MESSAGE_HISTORY", "100"))

        # In-memory storage
        chat_rooms = {}

        @app.route('/join_room', methods=['POST'])
        def join_room():
            data = request.get_json()
            room_name = data.get('room_name')
            username = data.get('username')

            # Check room limit
            if len(chat_rooms) >= MAX_ROOMS and room_name not in chat_rooms:
                return jsonify({"error": "Maximum rooms reached"}), 400

            # Create room if doesn't exist
            if room_name not in chat_rooms:
                chat_rooms[room_name] = {
                    "name": room_name,
                    "users": [],
                    "message_count": 0
                }

            room = chat_rooms[room_name]

            # Check user limit
            if len(room["users"]) >= MAX_USERS_PER_ROOM:
                return jsonify({"error": "Room is full"}), 400

            # Add user
            if username not in room["users"]:
                room["users"].append(username)

            return jsonify({
                "room": room["name"],
                "users": room["users"],
                "user_count": len(room["users"])
            })

        if __name__ == '__main__':
            app.run()
        ```

### Environment Setup

Create a `.env` file for local development:

```bash
# .env file
MAX_ROOMS=20
MAX_USERS_PER_ROOM=100
MESSAGE_HISTORY=500
DEBUG=true
```

Deploy with environment variables:

```bash
# Local with environment variables
MAX_ROOMS=20 jac serve simple_chat.jac

# Or using .env file (if supported by your environment)
jac serve simple_chat.jac
```

---

## Message Management and Storage

Instead of WebSockets, let's focus on RESTful message management that works with jac-cloud's current capabilities:

### Message Storage Implementation

!!! example "RESTful Chat Implementation"
    ```jac
        # message_chat.jac
        import from datetime { datetime }
        import from os { getenv}
        import from uuid { uuid4 }

        node ChatMessage {
            has content: str;
            has sender: str;
            has timestamp: str;
            has room_name: str;
            has id: str = "msg_" + str(uuid4());
        }

        node ChatRoom {
            has name: str;
            has users: list[str] = [];
            has message_count: int = 0;

            def add_message(sender: str, content: str) -> ChatMessage {
                new_message = ChatMessage(
                    content=content,
                    sender=sender,
                    timestamp=datetime.now().isoformat(),
                    room_name=self.name
                );
                self ++> new_message;
                self.message_count += 1;
                return new_message;
            }

            def get_recent_messages(limit: int = 20) -> list[dict] {
                messages = [self --> (`?ChatMessage)];
                recent = messages[-limit:] if len(messages) > limit else messages;
                return [
                    {
                        "content": msg.content,
                        "sender": msg.sender,
                        "timestamp": msg.timestamp
                    }
                    for msg in recent
                ];

            }
        }

        walker join_room {
            has room_name: str;
            has username: str;

            obj __specs__ {
                static has auth: bool = False;
            }

            can join_chat with `root entry {
                # Find or create room
                room = [-->(`?ChatRoom)](?name == self.room_name);

                if not room {
                    # Check room limit
                    total_rooms = len([-->(`?ChatRoom)]);
                    if total_rooms >= int(getenv("MAX_ROOMS", "100")) {
                        report {"error": "Maximum rooms reached"};
                        return;
                    }

                    room = ChatRoom(name=self.room_name);
                    here ++> room;
                } else {
                    room = room[0];
                }

                # Check user limit
                if len(room.users) >= int(getenv("MAX_USERS_PER_ROOM", "100")) {
                    report {"error": "Room is full"};
                    return;
                }

                # Add user if not already in room
                if self.username not in room.users {
                    room.users.append(self.username);
                }

                report {
                    "room": room.name,
                    "users": room.users,
                    "user_count": len(room.users)
                };
                # return room;
            }
        }


        walker send_message {
            has room_name: str;
            has username: str;
            has message: str;

            obj __specs__ {
                static has auth: bool = False;
            }

            can process_message with `root entry {
                # Find the room
                room = [-->(`?ChatRoom)](?name == self.room_name);

                if not room {
                    report {"error": "Room not found"};
                    return;
                }

                room = room[0];

                # Check if user is in room
                if self.username not in room.users {
                    report {"error": "User not in room"};
                    return;
                }

                # Add message
                new_message = room.add_message(self.username, self.message);

                report {
                    "status": "message_sent",
                    "message_id": new_message.id,
                    "timestamp": new_message.timestamp
                };
            }
        }

        walker get_chat_history {
            has room_name: str;
            has limit: int = 20;

            obj __specs__ {
                static has auth: bool = False;
            }

            can fetch_history with `root entry {
                room = [-->(`?ChatRoom)](?name == self.room_name);

                if room {
                    messages = room[0].get_recent_messages(self.limit);
                    report {"room": self.room_name, "messages": messages};
                } else {
                    report {"error": "Room not found"};
                }
            }
        }
    ```

### Testing Message API

Deploy the message-enabled chat:

```bash
jac serve message_chat.jac
```

Test with curl (all walker endpoints are POST):

```bash
# Join a room first
curl -X POST http://localhost:8000/walker/join_room \
  -H "Content-Type: application/json" \
  -d '{"room_name": "general", "username": "alice"}'

# Send a message
curl -X POST http://localhost:8000/walker/send_message \
  -H "Content-Type: application/json" \
  -d '{"room_name": "general", "username": "alice", "message": "Hello everyone!"}'

# Get chat history
curl -X POST http://localhost:8000/walker/get_chat_history \
  -H "Content-Type: application/json" \
  -d '{"room_name": "general", "limit": 10}'
```

---

## Webhook Integration

Webhooks enable your Jac applications to receive real-time notifications from external services. This is essential for integrating with third-party APIs and building event-driven architectures.

### Webhook Receiver Implementation

!!! example "Chat Notification Webhooks"
    ```jac
    # webhook_chat.jac
    import from datetime { datetime }

    node WebhookLog {
        has source: str;
        has event_type: str;
        has data: dict;
        has received_at: str;
    }

    # Webhook receiver walker
    walker receive_webhook {
        has source: str = "unknown";
        has event_type: str;
        has data: dict;

        can process_webhook with `root entry {
            # Log the webhook
            webhook_log = WebhookLog(
                source=self.source,
                event_type=self.event_type,
                data=self.data,
                received_at=datetime.now().isoformat()
            );
            here ++> webhook_log;

            # Process different webhook types
            if self.source == "github" and self.event_type == "push" {
                self.handle_github_push();
            } elif self.source == "slack" and self.event_type == "message" {
                self.handle_slack_message();
            } else {
                print(f"Unknown webhook: {self.source}/{self.event_type}");
            }

            report {"status": "webhook_processed", "log_id": webhook_log.id};
        }

        can handle_github_push() {
            # Extract commit information
            commits = self.data.get("commits", []);
            repo_name = self.data.get("repository", {}).get("name", "unknown");

            # Send notification to chat
            for commit in commits {
                message = f"🔨 New commit in {repo_name}: {commit.get('message', 'No message')}";
                self.send_to_chat("dev-updates", "GitBot", message);
            }
        }

        can handle_slack_message() {
            # Forward Slack messages to our chat
            user = self.data.get("user_name", "SlackUser");
            text = self.data.get("text", "");
            channel = self.data.get("channel_name", "general");

            message = f"[Slack] {text}";
            self.send_to_chat(channel, user, message);
        }

        can send_to_chat(room_name: str, sender: str, message: str) {
            # Find or create room
            room = [-->(`?ChatRoom)](?name == room_name);
            if not room {
                room = ChatRoom(name=room_name);
                here ++> room;
            } else {
                room = room[0];
            }

            # Add message
            room.add_message(sender, message);
        }
    }

    walker get_webhook_logs {
        has source: str = "";
        has limit: int = 50;

        can fetch_logs with `root entry {
            all_logs = [-->(`?WebhookLog)];

            # Filter by source if specified
            if self.source {
                filtered_logs = [log for log in all_logs if log.source == self.source];
            } else {
                filtered_logs = all_logs;
            }

            # Get recent logs
            recent_logs = filtered_logs[-self.limit:];

            report {
                "logs": [
                    {
                        "source": log.source,
                        "event_type": log.event_type,
                        "received_at": log.received_at
                    }
                    for log in recent_logs
                ],
                "total": len(filtered_logs)
            };
        }
    }
    ```

### Testing Webhooks

Test webhook locally:

```bash
curl -X POST http://localhost:8000/walker/receive_webhook \
  -H "Content-Type: application/json" \
  -d '{
    "source": "github",
    "event_type": "push",
    "data": {
      "repository": {"name": "my-repo"},
      "commits": [{"message": "Fix critical bug"}]
    }
  }'
```

---

## Logging and Monitoring

Production applications require comprehensive logging and monitoring. Jac Cloud provides built-in observability features that integrate with your application logic.

### Application Logging

!!! example "Structured Logging System"
    ```jac
    # logging_chat.jac
    import from datetime { datetime }
    import from logging { getLogger }

    glob logger = getLogger("chat_app");

    node LogEntry {
        has level: str;
        has message: str;
        has timestamp: str;
        has context: dict = {};
    }

    walker log_activity {
        has level: str = "info";
        has message: str;
        has context: dict = {};

        can record_log with `root entry {
            # Create log entry
            log_entry = LogEntry(
                level=self.level,
                message=self.message,
                timestamp=datetime.now().isoformat(),
                context=self.context
            );
            here ++> log_entry;

            # Also log to system logger
            if self.level == "error" {
                logger.error(f"{self.message} | Context: {self.context}");
            } elif self.level == "warning" {
                logger.warning(f"{self.message} | Context: {self.context}");
            } else {
                logger.info(f"{self.message} | Context: {self.context}");
            }

            report {"log_id": log_entry.id, "logged_at": log_entry.timestamp};
        }
    }

    # Enhanced chat with logging
    walker send_logged_message {
        has room_name: str;
        has username: str;
        has message: str;

        can send_with_logging with `root entry {
            # Log the attempt
            log_activity(
                level="info",
                message="Message send attempt",
                context={
                    "room": self.room_name,
                    "user": self.username,
                    "message_length": len(self.message)
                }
            ) spawn here;

            # Find room
            room = [-->(`?ChatRoom)](?name == self.room_name);

            if not room {
                log_activity(
                    level="warning",
                    message="Message failed - room not found",
                    context={"room": self.room_name, "user": self.username}
                ) spawn here;

                report {"error": "Room not found"};
                return;
            }

            room = room[0];

            # Check if user can send
            if self.username not in room.users {
                log_activity(
                    level="warning",
                    message="Message failed - user not in room",
                    context={"room": self.room_name, "user": self.username}
                ) spawn here;

                report {"error": "User not in room"};
                return;
            }

            # Send message
            new_message = room.add_message(self.username, self.message);

            # Log success
            log_activity(
                level="info",
                message="Message sent successfully",
                context={
                    "room": self.room_name,
                    "user": self.username,
                    "message_id": new_message.id
                }
            ) spawn here;

            report {"status": "sent", "message_id": new_message.id};
        }
    }

    walker get_logs {
        has level: str = "";
        has limit: int = 100;

        can fetch_logs with `root entry {
            all_logs = [-->(`?LogEntry)];

            # Filter by level if specified
            if self.level {
                filtered_logs = [log for log in all_logs if log.level == self.level];
            } else {
                filtered_logs = all_logs;
            }

            # Get recent logs
            recent_logs = filtered_logs[-self.limit:];

            report {
                "logs": [
                    {
                        "level": log.level,
                        "message": log.message,
                        "timestamp": log.timestamp,
                        "context": log.context
                    }
                    for log in recent_logs
                ],
                "total": len(filtered_logs)
            };
        }
    }
    ```

---

## Background Tasks and Cleanup

Automated tasks are essential for maintenance, cleanup, and periodic operations.

### Scheduled Chat Maintenance

!!! example "Chat Room Cleanup Tasks"
    ```jac
    # scheduled_chat.jac
    import from datetime { datetime, timedelta }

    # Cleanup walker for maintenance tasks
    walker cleanup_inactive_rooms {
        has max_age_hours: int = 24;

        can perform_cleanup with `root entry {
            current_time = datetime.now();
            cleanup_count = 0;

            # Find all rooms
            all_rooms = [-->(`?ChatRoom)];

            for room in all_rooms {
                # Check if room has been inactive
                if len(room.users) == 0 {
                    # Get latest message
                    messages = [room --> ChatMessage];

                    if not messages {
                        # No messages, delete empty room
                        del room;
                        cleanup_count += 1;
                    } else {
                        # Check last message age
                        latest_message = messages[-1];
                        message_time = datetime.fromisoformat(latest_message.timestamp);

                        if (current_time - message_time).total_seconds() > (self.max_age_hours * 3600) {
                            # Room is too old, cleanup
                            for msg in messages {
                                del msg;
                            }
                            del room;
                            cleanup_count += 1;
                        }
                    }
                }
            }

            # Log cleanup results
            log_activity(
                level="info",
                message="Cleanup task completed",
                context={
                    "rooms_cleaned": cleanup_count,
                    "total_rooms": len([-->(`?ChatRoom)])
                }
            ) spawn here;

            report {
                "status": "cleanup_completed",
                "rooms_cleaned": cleanup_count,
                "timestamp": current_time.isoformat()
            };
        }
    }

    # Daily statistics walker
    walker generate_daily_stats {
        can collect_stats with `root entry {
            # Count active rooms and users
            all_rooms = [-->(`?ChatRoom)];
            total_rooms = len(all_rooms);
            total_users = sum(len(room.users) for room in all_rooms);

            # Count messages sent today
            today = datetime.now().date();
            all_messages = [-->(`?ChatMessage)];

            today_messages = 0;
            for msg in all_messages {
                msg_date = datetime.fromisoformat(msg.timestamp).date();
                if msg_date == today {
                    today_messages += 1;
                }
            }

            # Create stats report
            stats = {
                "date": today.isoformat(),
                "active_rooms": total_rooms,
                "active_users": total_users,
                "messages_today": today_messages,
                "generated_at": datetime.now().isoformat()
            };

            # Log daily stats
            log_activity(
                level="info",
                message="Daily statistics generated",
                context=stats
            ) spawn here;

            report stats;
        }
    }
    ```

### Manual Task Execution

```bash
# Run cleanup manually
curl -X POST http://localhost:8000/walker/cleanup_inactive_rooms \
  -H "Content-Type: application/json" \
  -d '{"max_age_hours": 48}'

# Generate daily stats
curl -X POST http://localhost:8000/walker/generate_daily_stats \
  -H "Content-Type: application/json" \
  -d '{}'
```

---

## Best Practices

!!! summary "Cloud Development Guidelines"
    - **Use environment variables**: Keep configuration flexible and secure
    - **Structure your logs**: Use consistent logging patterns for debugging
    - **Validate webhooks**: Always verify webhook sources and data
    - **Monitor performance**: Track application metrics and health
    - **Handle failures gracefully**: Implement retry logic and fallback patterns
    - **Secure sensitive data**: Never commit secrets or API keys to code

## Key Takeaways

!!! summary "What We've Learned"
    **Configuration Management:**

    - **Environment variables**: Flexible application configuration without code changes
    - **Security patterns**: Keep sensitive data in environment variables, not code
    - **Multi-environment support**: Different settings for development, staging, production
    - **Runtime configuration**: Adjust application behavior without redeployment

    **Integration Capabilities:**

    - **Webhook support**: Receive real-time notifications from external services
    - **RESTful architecture**: All walkers become scalable API endpoints
    - **External service integration**: Connect with third-party APIs and services
    - **Event-driven patterns**: Build reactive applications that respond to external events

    **Monitoring and Observability:**

    - **Structured logging**: Built-in logging patterns for debugging and monitoring
    - **Performance tracking**: Monitor application health and performance metrics
    - **Error handling**: Graceful error recovery and reporting
    - **Audit trails**: Track important application events and user actions

    **Production Features:**

    - **Background tasks**: Automated maintenance and cleanup operations
    - **Data management**: Efficient patterns for data lifecycle management
    - **Scalability**: Built-in support for horizontal scaling
    - **Reliability**: Robust patterns for production deployment

!!! tip "Try It Yourself"
    Enhance your cloud applications by adding:
    - Real-time chat with webhook integrations
    - Automated data backup and cleanup tasks
    - External service integrations (email, SMS, payments)
    - Comprehensive monitoring and alerting systems

    Remember: All these advanced features work seamlessly with Jac's scale-agnostic architecture!

---

*Ready to master Jac's type system? Continue to [Chapter 17: Type System Deep Dive](chapter_16.md)!*

# Chapter 17: Type System Deep Dive

In this chapter, we'll explore Jac's advanced type system that provides powerful generic programming capabilities, type constraints, and graph-aware type checking. We'll build a generic data processing system that demonstrates type safety, constraints, and runtime validation through practical examples.

!!! info "What You'll Learn"
    - Advanced generic programming with the `any` type
    - Type constraints and validation patterns
    - Graph-aware type checking for nodes and edges
    - Building type-safe, reusable components
    - Runtime type validation and guards

---

## Advanced Type System Features

Jac's type system goes beyond basic types to provide powerful features that work seamlessly with Object-Spatial Programming. The `any` type enables flexible programming while maintaining type safety through runtime validation.

!!! success "Type System Benefits"
    - **Flexible Typing**: Use `any` for maximum flexibility when needed
    - **Runtime Safety**: Validate types at runtime with built-in guards
    - **Graph Integration**: Type safety extends to nodes, edges, and walkers
    - **Constraint Validation**: Enforce business rules through type checking

### Traditional vs Jac Type System

!!! example "Type System Comparison"
    === "Traditional Approach"
        ```python
        # python_generics.py - Complex generic setup
        from typing import TypeVar, Generic, List, Any, Union, Optional
        from abc import ABC, abstractmethod

        T = TypeVar('T')
        U = TypeVar('U')

        class Processable(ABC):
            @abstractmethod
            def process(self) -> str:
                pass

        class DataProcessor(Generic[T]):
            def __init__(self):
                self.items: List[T] = []

            def add(self, item: T) -> None:
                self.items.append(item)

            def process_all(self, func) -> List[Any]:
                return [func(item) for item in self.items]

            def find(self, predicate) -> Optional[T]:
                for item in self.items:
                    if predicate(item):
                        return item
                return None

        # Usage requires explicit type parameters
        processor: DataProcessor[int] = DataProcessor()
        processor.add(42)
        processor.add(24)
        ```

    === "Jac Type System"
        <div class="code-block">
        ```jac
        # data_processor.jac - Simple and flexible
        obj DataProcessor {
            has items: list[any] = [];

            def add(item: any) -> None {
                self.items.append(item);
            }

            def process_all(func: any) -> list[any] {
                return [func(item) for item in self.items];
            }

            def find(predicate: any) -> any | None {
                for item in self.items {
                    if predicate(item) {
                        return item;
                    }
                }
                return None;
            }

            def filter_by_type(target_type: any) -> list[any] {
                return [item for item in self.items if isinstance(item, target_type)];
            }
        }

        with entry {
            # Simple usage with type inference
            processor = DataProcessor();
            processor.add(42);
            processor.add("hello");
            processor.add(3.14);

            # Type-safe operations with runtime validation
            numbers = processor.filter_by_type(int);
            print(f"Numbers: {numbers}");
        }
        ```
        </div>

---

## Runtime Type Validation

Jac provides powerful runtime type checking capabilities that complement the flexible `any` type, enabling robust error handling and dynamic type validation.

### Type Guards and Validation

!!! example "Runtime Type Validation System"
    <div class="code-block">
    ```jac
    # type_validator.jac
    obj TypeValidator {
        has strict_mode: bool = False;

        """Check if value matches expected type."""
        def validate_type(value: any, expected_type: any) -> bool {
            if expected_type == int {
                return isinstance(value, int);
            } elif expected_type == str {
                return isinstance(value, str);
            } elif expected_type == float {
                return isinstance(value, float);
            } elif expected_type == list {
                return isinstance(value, list);
            } elif expected_type == dict {
                return isinstance(value, dict);
            }
            return True;  # Allow any for unknown types
        }

        """Safely cast value to target type."""
        def safe_cast(value: any, target_type: any) -> any | None {
            try {
                if target_type == int {
                    return int(value);
                } elif target_type == str {
                    return str(value);
                } elif target_type == float {
                    return float(value);
                } elif target_type == bool {
                    return bool(value);
                }
                return value;
            } except ValueError {
                if self.strict_mode {
                    raise ValueError(f"Cannot cast {value} to {target_type}");
                }
                return None;
            }
        }

        """Validate value is within specified range."""
        def validate_range(value: any, min_val: any = None, max_val: any = None) -> bool {
            if min_val is not None and value < min_val {
                return False;
            }
            if max_val is not None and value > max_val {
                return False;
            }
            return True;
        }
    }

    with entry {
        validator = TypeValidator(strict_mode=True);

        # Test type validation
        test_values = [42, "hello", 3.14, True, [1, 2, 3]];
        expected_types = [int, str, float, bool, list];

        for i in range(len(test_values)) {
            value = test_values[i];
            expected = expected_types[i];
            is_valid = validator.validate_type(value, expected);
            print(f"{value} is {expected}: {is_valid}");
        }

        # Test safe casting
        cast_result = validator.safe_cast("123", int);
        print(f"Cast '123' to int: {cast_result}");

        # Test range validation
        in_range = validator.validate_range(50, 0, 100);
        print(f"50 in range [0, 100]: {in_range}");
    }
    ```
    </div>

### Advanced Type Guards

!!! example "Complex Type Validation Patterns"
    <div class="code-block">
    ```jac
    # advanced_validator.jac
    obj SchemaValidator {
        has schema: dict[str, any] = {};

        """Define expected type for a field."""
        def set_field_type(field_name: str, field_type: any) -> None {
            self.schema[field_name] = field_type;
        }

        """Validate object against schema."""
        def validate_object(obj: any) -> dict[str, any] {
            results = {
                "valid": True,
                "errors": [],
                "field_results": {}
            };

            if not isinstance(obj, dict) {
                results["valid"] = False;
                results["errors"].append("Object must be a dictionary");
                return results;
            }

            for (field_name, expected_type) in self.schema.items() {
                if field_name not in obj {
                    results["valid"] = False;
                    results["errors"].append(f"Missing required field: {field_name}");
                    results["field_results"][field_name] = False;
                } else {
                    field_value = obj[field_name];
                    is_valid = self.validate_field(field_value, expected_type);
                    results["field_results"][field_name] = is_valid;
                    if not is_valid {
                        results["valid"] = False;
                        results["errors"].append(f"Invalid type for {field_name}: expected {expected_type}, got {type(field_value)}");
                    }
                }
            }

            return results;
        }

        """Validate individual field value."""
        def validate_field(value: any, expected_type: any) -> bool {
            if expected_type == "string" {
                return isinstance(value, str);
            } elif expected_type == "number" {
                return isinstance(value, (int, float));
            } elif expected_type == "boolean" {
                return isinstance(value, bool);
            } elif expected_type == "list" {
                return isinstance(value, list);
            } elif expected_type == "dict" {
                return isinstance(value, dict);
            }
            return True;
        }
    }

    with entry {
        # Create schema for user data
        user_validator = SchemaValidator();
        user_validator.set_field_type("name", "string");
        user_validator.set_field_type("age", "number");
        user_validator.set_field_type("email", "string");
        user_validator.set_field_type("active", "boolean");

        # Test valid user
        valid_user = {
            "name": "Alice",
            "age": 30,
            "email": "alice@example.com",
            "active": True
        };

        result = user_validator.validate_object(valid_user);
        print(f"Valid user validation: {result}");

        # Test invalid user
        invalid_user = {
            "name": "Bob",
            "age": "thirty",  # Wrong type
            "email": "bob@example.com"
            # Missing 'active' field
        };

        result = user_validator.validate_object(invalid_user);
        print(f"Invalid user validation: {result}");
    }
    ```
    </div>

---

## Graph-Aware Type Checking

Jac's type system extends to Object-Spatial Programming constructs, providing compile-time and runtime guarantees about graph structure and walker behavior.

### Node and Edge Type Safety

!!! example "Type-Safe Graph Operations"
    <div class="code-block">
    ```jac
    # typed_graph.jac
    node Person {
        has name: str;
        has age: int;

        def validate_person() -> bool {
            return len(self.name) > 0 and self.age >= 0;
        }
    }

    node Company {
        has company_name: str;
        has industry: str;

        def validate_company() -> bool {
            return len(self.company_name) > 0 and len(self.industry) > 0;
        }
    }

    edge WorksAt {
        has position: str;
        has salary: float;
        has start_date: str;

        def validate_employment() -> bool {
            return len(self.position) > 0 and self.salary > 0;
        }
    }

    edge FriendsWith {
        has since: str;
        has closeness: int;  # 1-10 scale

        def validate_friendship() -> bool {
            return self.closeness >= 1 and self.closeness <= 10;
        }
    }

    obj GraphValidator {
        has validation_errors: list[str] = [];

        """Validate any node type."""
        def validate_node(node: any) -> bool {
            self.validation_errors = [];

            if isinstance(node, Person) {
                if not node.validate_person() {
                    self.validation_errors.append(f"Invalid person: {node.name}");
                    return False;
                }
            } elif isinstance(node, Company) {
                if not node.validate_company() {
                    self.validation_errors.append(f"Invalid company: {node.company_name}");
                    return False;
                }
            } else {
                self.validation_errors.append(f"Unknown node type: {type(node)}");
                return False;
            }

            return True;
        }

        """Validate edge connection between nodes."""
        def validate_edge_connection(from_node: any, edge: any, to_node: any) -> bool {
            # Check if edge type is appropriate for node types
            if isinstance(edge, WorksAt) {
                # Person should work at Company
                if not (isinstance(from_node, Person) and isinstance(to_node, Company)) {
                    self.validation_errors.append("WorksAt edge must connect Person to Company");
                    return False;
                }
                return edge.validate_employment();
            } elif isinstance(edge, FriendsWith) {
                # Both nodes should be Person
                if not (isinstance(from_node, Person) and isinstance(to_node, Person)) {
                    self.validation_errors.append("FriendsWith edge must connect Person to Person");
                    return False;
                }
                return edge.validate_friendship();
            }

            self.validation_errors.append(f"Unknown edge type: {type(edge)}");
            return False;
        }
    }

    with entry {
        # Create graph elements
        alice = Person(name="Alice", age=30);
        bob = Person(name="Bob", age=25);
        tech_corp = Company(company_name="TechCorp", industry="Technology");

        # Create relationships
        works_edge = WorksAt(position="Developer", salary=75000.0, start_date="2023-01-15");
        friend_edge = FriendsWith(since="2020-01-01", closeness=8);

        # Validate graph elements
        validator = GraphValidator();

        # Validate nodes
        alice_valid = validator.validate_node(alice);
        print(f"Alice valid: {alice_valid}");

        # Validate edge connections
        work_connection_valid = validator.validate_edge_connection(alice, works_edge, tech_corp);
        print(f"Work connection valid: {work_connection_valid}");

        friend_connection_valid = validator.validate_edge_connection(alice, friend_edge, bob);
        print(f"Friend connection valid: {friend_connection_valid}");

        # Test invalid connection
        invalid_connection = validator.validate_edge_connection(alice, works_edge, bob);  # Wrong types
        print(f"Invalid connection valid: {invalid_connection}");
        print(f"Validation errors: {validator.validation_errors}");
    }
    ```
    </div>

### Walker Type Validation

!!! example "Type-Safe Walker Patterns"
    <div class="code-block">
    ```jac
    # typed_walkers.jac

    node Person {
        has name: str;
        has age: int;

        def validate_person() -> bool {
            return len(self.name) > 0 and self.age >= 0;
        }
    }

    node Company {
        has company_name: str;
        has industry: str;

        def validate_company() -> bool {
            return len(self.company_name) > 0 and len(self.industry) > 0;
        }
    }

    edge WorksAt {
        has position: str;
        has salary: float;
        has start_date: str;

        def validate_employment() -> bool {
            return len(self.position) > 0 and self.salary > 0;
        }
    }

    edge FriendsWith {
        has since: str;
        has closeness: int;  # 1-10 scale

        def validate_friendship() -> bool {
            return self.closeness >= 1 and self.closeness <= 10;
        }
    }

    walker PersonVisitor {
        has visited_count: int = 0;
        has person_names: list[str] = [];
        has validation_errors: list[str] = [];

        can visit_person with Person entry {
            # Type-safe person processing
            if self.validate_person_node(here) {
                self.visited_count += 1;
                self.person_names.append(here.name);
                print(f"Visited person: {here.name} (age {here.age})");

                # Continue to connected persons
                friends = [->:FriendsWith:->(`?Person)];
                if friends {
                    visit friends;
                }
            } else {
                print(f"Invalid person node encountered: {here.name}");
            }
        }

        can visit_company with Company entry {
            # Companies are not processed by PersonVisitor
            print(f"Skipping company: {here.company_name}");
        }

        """Validate person node before processing."""
        def validate_person_node(person: any) -> bool {
            if not isinstance(person, Person) {
                self.validation_errors.append(f"Expected Person, got {type(person)}");
                return False;
            }

            if not person.validate_person() {
                self.validation_errors.append(f"Invalid person data: {person.name}");
                return False;
            }

            return True;
        }
    }

    walker CompanyAnalyzer {
        has companies_visited: list[str] = [];
        has total_employees: int = 0;

        can analyze_company with Company entry {
            if self.validate_company_node(here) {
                self.companies_visited.append(here.company_name);
                print(f"Analyzing company: {here.company_name} in {here.industry}");

                # Count employees (people working at this company)
                employees = [<-:WorksAt:<-(`?Person)];
                employee_count = len(employees);
                self.total_employees += employee_count;

                print(f"  Employees: {employee_count}");
                for employee in employees {
                    print(f"    - {employee.name}");
                }
            }
        }

        """Validate company node before processing."""
        def validate_company_node(company: any) -> bool {
            if not isinstance(company, Company) {
                return False;
            }
            return company.validate_company();
        }
    }

    with entry {
        # Create network
        alice = root ++> Person(name="Alice", age=30);
        bob = root ++> Person(name="Bob", age=25);
        tech_corp = root ++> Company(company_name="TechCorp", industry="Technology");

        # Create connections
        alice[0] +>:WorksAt(position="Developer", salary=75000.0, start_date="2023-01-15"):+> tech_corp[0];
        bob[0] +>:WorksAt(position="Designer", salary=65000.0, start_date="2023-02-01"):+> tech_corp[0];
        alice[0] +>:FriendsWith(since="2020-01-01", closeness=8):+> bob[0];

        # Test type-safe walkers
        person_visitor = PersonVisitor();
        alice[0] spawn person_visitor;

        print(f"Person visitor results:");
        print(f"  Visited: {person_visitor.visited_count} people");
        print(f"  Names: {person_visitor.person_names}");

        company_analyzer = CompanyAnalyzer();
        tech_corp[0] spawn company_analyzer;

        print(f"Company analyzer results:");
        print(f"  Companies: {company_analyzer.companies_visited}");
        print(f"  Total employees: {company_analyzer.total_employees}");
    }
    ```
    </div>

---

## Building Type-Safe Components

Using Jac's flexible type system, we can build reusable components that are both type-safe and adaptable.

### Generic Data Structures

!!! example "Type-Safe Generic Collections"
    <div class="code-block">
    ```jac
    # generic_collections.jac
    obj SafeList {
        has items: list[any] = [];
        has item_type: any = None;
        has allow_mixed_types: bool = False;

        """Set type constraint for list items."""
        def set_type_constraint(expected_type: any) -> None {
            self.item_type = expected_type;
        }

        """Add item with type checking."""
        def add(item: any) -> bool {
            if self.item_type is not None and not self.allow_mixed_types {
                if not self.check_type(item, self.item_type) {
                    print(f"Type error: expected {self.item_type}, got {type(item)}");
                    return False;
                }
            }

            self.items.append(item);
            return True;
        }

        """Safely get item by index."""
        def get(index: int) -> any | None {
            if 0 <= index < len(self.items) {
                return self.items[index];
            }
            return None;
        }

        """Get all items of specific type."""
        def filter_by_type(target_type: any) -> list[any] {
            return [item for item in self.items if self.check_type(item, target_type)];
        }

        """Check if value matches expected type."""
        def check_type(value: any, expected_type: any) -> bool {
            if expected_type == int {
                return isinstance(value, int);
            } elif expected_type == str {
                return isinstance(value, str);
            } elif expected_type == float {
                return isinstance(value, float);
            } elif expected_type == bool {
                return isinstance(value, bool);
            } elif expected_type == list {
                return isinstance(value, list);
            } elif expected_type == dict {
                return isinstance(value, dict);
            }
            return True;
        }

        """Get summary of types in the list."""
        def get_type_summary() -> dict[str, int] {
            type_counts = {};
            for item in self.items {
                type_name = type(item).__name__;
                type_counts[type_name] = type_counts.get(type_name, 0) + 1;
            }
            return type_counts;
        }
    }

    with entry {
        # Create type-constrained list
        number_list = SafeList();
        number_list.set_type_constraint(int);

        # Add valid items
        success1 = number_list.add(42);
        success2 = number_list.add(24);
        success3 = number_list.add("hello");  # Should fail

        print(f"Added 42: {success1}");
        print(f"Added 24: {success2}");
        print(f"Added 'hello': {success3}");

        # Create mixed-type list
        mixed_list = SafeList(allow_mixed_types=True);
        mixed_list.add(42);
        mixed_list.add("hello");
        mixed_list.add(3.14);
        mixed_list.add(True);

        print(f"Mixed list type summary: {mixed_list.get_type_summary()}");

        # Filter by type
        numbers = mixed_list.filter_by_type(int);
        strings = mixed_list.filter_by_type(str);

        print(f"Numbers: {numbers}");
        print(f"Strings: {strings}");
    }
    ```
    </div>

---

## Best Practices

!!! summary "Type System Guidelines"
    - **Use `any` strategically**: Apply `any` type for maximum flexibility while implementing runtime validation
    - **Validate at boundaries**: Check types when data enters your system from external sources
    - **Leverage runtime checks**: Use isinstance() and custom validation functions for type safety
    - **Design for flexibility**: Build components that can handle multiple types when appropriate
    - **Document type expectations**: Make type requirements clear in function and method documentation
    - **Test with multiple types**: Verify your code works correctly with different type combinations

## Key Takeaways

!!! summary "What We've Learned"
    **Advanced Type Features:**

    - **Flexible typing**: Use `any` type for maximum flexibility when needed
    - **Runtime validation**: Dynamic type checking complements static analysis
    - **Graph-aware types**: Compile-time safety for spatial programming constructs
    - **Type guards**: Runtime validation patterns for dynamic typing

    **Practical Applications:**

    - **Reusable components**: Build libraries that work with multiple data types
    - **Safe graph operations**: Prevent type errors in node and edge relationships
    - **Data validation**: Robust input validation with clear error messages
    - **Performance optimization**: Type information enables better optimization

    **Development Benefits:**

    - **Early error detection**: Catch type mismatches through validation
    - **Better documentation**: Types and validation serve as executable documentation
    - **IDE support**: Enhanced development experience with type information
    - **Refactoring safety**: Type system helps prevent breaking changes

    **Advanced Features:**

    - **Schema validation**: Complex object validation with custom rules
    - **Type constraints**: Enforce business rules through type checking
    - **Generic patterns**: Type-safe graph traversal and processing
    - **Protocol support**: Interface-based programming with validation

!!! tip "Try It Yourself"
    Master the type system by building:

    - A generic data processing pipeline with runtime validation
    - Type-safe graph algorithms with proper node/edge validation
    - Runtime validation systems for API endpoints
    - Generic walker patterns for different graph structures

    Remember: Jac's type system provides flexibility through `any` while enabling powerful runtime validation!

---

*Ready to learn about testing and debugging? Continue to [Chapter 18: Testing and Debugging](chapter_17.md)!*


# Chapter 18: Testing and Debugging

In this chapter, we'll explore Jac's built-in testing framework and debugging strategies for spatial applications. We'll build a comprehensive test suite for a social media system that demonstrates testing nodes, edges, walkers, and complex graph operations.

!!! info "What You'll Learn"
    - Jac's built-in testing framework with `.test.jac` files
    - Testing spatial applications with nodes, edges, and walkers
    - Debugging techniques for graph traversal and walker behavior
    - Performance testing and optimization strategies
    - Test-driven development patterns for OSP

---

## Jac's Built-in Testing Framework

Jac provides a powerful testing framework that automatically discovers and runs tests. When you run `jac test myfile.jac`, it automatically looks for `myfile.test.jac` and executes all test blocks within it.

!!! success "Testing Framework Benefits"
    - **Automatic Discovery**: `.test.jac` files are automatically found and executed
    - **Graph-Aware Testing**: Native support for testing spatial relationships
    - **Walker Testing**: Test mobile computation patterns naturally
    - **Type-Safe Assertions**: Leverage Jac's type system in test validation
    - **Zero Configuration**: No external testing frameworks required

### Traditional vs Jac Testing

!!! example "Testing Comparison"
    === "Traditional Approach"
        ```python
        # test_social_media.py - External framework required
        import unittest
        from social_media import Profile, Tweet, Comment

        class TestSocialMedia(unittest.TestCase):
            def setUp(self):
                self.profile = Profile("test_user")
                self.tweet = Tweet("Hello world!")

            def test_create_profile(self):
                self.assertEqual(self.profile.username, "test_user")
                self.assertIsInstance(self.profile, Profile)

            def test_create_tweet(self):
                self.profile.add_tweet(self.tweet)
                self.assertEqual(len(self.profile.tweets), 1)
                self.assertEqual(self.profile.tweets[0].content, "Hello world!")

            def test_follow_user(self):
                other_user = Profile("other_user")
                self.profile.follow(other_user)
                self.assertIn(other_user, self.profile.following)

        if __name__ == '__main__':
            unittest.main()
        ```

    === "Jac Testing"
        ```jac
        # social_media.test.jac - Built-in testing

        test create_profile {
            root spawn visit_profile();
            profile = [root --> Profile][0];
            check isinstance(profile, Profile);
            check profile.username == "";  # Default value
        }

        test update_profile {
            root spawn update_profile(new_username="test_user");
            profile = [root --> Profile][0];
            check profile.username == "test_user";
        }

        test create_tweet {
            root spawn create_tweet(content="Hello world!");
            tweet = [root --> Profile --> Tweet][0];
            check tweet.content == "Hello world!";
            check isinstance(tweet, Tweet);
        }

        test follow_user {
            # Create another profile to follow
            other_profile = Profile(username="other_user");
            other_profile spawn follow_request();

            # Check follow relationship exists
            followed = [root --> Profile ->:Follow:-> Profile][0];
            check followed.username == "other_user";
        }
        ```

---

## Testing Graph Structures

Testing spatial applications requires verifying both node properties and relationship integrity. Let's build a comprehensive social media system and test it thoroughly.

### Basic Social Media System

!!! example "Social Media Implementation"
    === "social_media.jac"
        ```jac
        # social_media.jac
        import from datetime { datetime }

        node Profile {
            has username: str = "";
            has bio: str = "";
            has follower_count: int = 0;

            can update with update_profile entry;
            can follow with follow_request entry;
            can unfollow with unfollow_request entry;
        }

        node Tweet {
            has content: str;
            has created_at: str = datetime.now().strftime("%Y-%m-%d %H:%M:%S");
            has like_count: int = 0;

            can update with update_tweet exit;
            can delete with remove_tweet exit;
            can like with like_tweet entry;
            can unlike with unlike_tweet entry;
        }

        node Comment {
            has content: str;
            has created_at: str = datetime.now().strftime("%Y-%m-%d %H:%M:%S");

            can update with update_comment entry;
            can delete with remove_comment entry;
        }

        edge Follow {
            has followed_at: str = datetime.now().strftime("%Y-%m-%d %H:%M:%S");
        }

        edge Post {
            has posted_at: str = datetime.now().strftime("%Y-%m-%d %H:%M:%S");
        }

        edge Like {
            has liked_at: str = datetime.now().strftime("%Y-%m-%d %H:%M:%S");
        }

        edge CommentOn {}

        walker visit_profile {
            can visit_profile with `root entry {
                visit [-->Profile] else {
                    new_profile = here ++> Profile();
                    visit new_profile;
                }
            }
        }

        walker update_profile(visit_profile) {
            has new_username: str;
            has new_bio: str = "";
        }

        walker follow_request {
            can follow_user with Profile entry {
                current_profile = [root --> Profile][0];
                if current_profile != here {
                    current_profile +>:Follow:+> here;
                    here.follower_count += 1;
                    report {"message": f"Now following {here.username}"};
                } else {
                    report {"error": "Cannot follow yourself"};
                }
            }
        }

        walker unfollow_request {
            can unfollow_user with Profile entry {
                current_profile = [root --> Profile][0];
                follow_edges = [edge current_profile ->:Follow:-> here];
                if follow_edges {
                    del follow_edges[0];
                    here.follower_count -= 1;
                    report {"message": f"Unfollowed {here.username}"};
                } else {
                    report {"error": "Not following this user"};
                }
            }
        }

        walker create_tweet(visit_profile) {
            has content: str;

            can post_tweet with Profile entry {
                tweet = here +>:Post:+> Tweet(content=self.content);
                report {"message": "Tweet created", "tweet_id": tweet[0].id};
            }
        }

        walker update_tweet {
            has updated_content: str;
        }

        walker remove_tweet {}

        walker like_tweet {
            can like_post with Tweet entry {
                current_profile = [root --> Profile][0];
                existing_likes = [edge current_profile ->:Like:-> here];

                if not existing_likes {
                    current_profile +>:Like:+> here;
                    here.like_count += 1;
                    report {"message": "Tweet liked"};
                } else {
                    report {"error": "Already liked this tweet"};
                }
            }
        }

        walker unlike_tweet {
            can unlike_post with Tweet entry {
                current_profile = [root --> Profile][0];
                like_edges = [edge current_profile ->:Like:-> here];

                if like_edges {
                    del like_edges[0];
                    here.like_count -= 1;
                    report {"message": "Tweet unliked"};
                } else {
                    report {"error": "Haven't liked this tweet"};
                }
            }
        }

        walker comment_on_tweet {
            has content: str;

            can add_comment with Tweet entry {
                current_profile = [root --> Profile][0];
                comment = current_profile ++> Comment(content=self.content);
                here +>:CommentOn:+> comment[0];
                report {"message": "Comment added", "comment_id": comment[0].id};
            }
        }
        ```

    === "Python Equivalent"
        ```python
        # social_media.py - Manual implementation
        from datetime import datetime
        from typing import List, Optional

        class Profile:
            def __init__(self, username: str = "", bio: str = ""):
                self.username = username
                self.bio = bio
                self.follower_count = 0
                self.following = []
                self.followers = []
                self.tweets = []

            def follow(self, other_profile):
                if other_profile not in self.following and other_profile != self:
                    self.following.append(other_profile)
                    other_profile.followers.append(self)
                    other_profile.follower_count += 1
                    return True
                return False

            def unfollow(self, other_profile):
                if other_profile in self.following:
                    self.following.remove(other_profile)
                    other_profile.followers.remove(self)
                    other_profile.follower_count -= 1
                    return True
                return False

        class Tweet:
            def __init__(self, content: str, author: Profile):
                self.content = content
                self.author = author
                self.created_at = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
                self.like_count = 0
                self.liked_by = []
                self.comments = []

            def like(self, user: Profile):
                if user not in self.liked_by:
                    self.liked_by.append(user)
                    self.like_count += 1
                    return True
                return False

            def unlike(self, user: Profile):
                if user in self.liked_by:
                    self.liked_by.remove(user)
                    self.like_count -= 1
                    return True
                return False

        class Comment:
            def __init__(self, content: str, author: Profile, tweet: Tweet):
                self.content = content
                self.author = author
                self.tweet = tweet
                self.created_at = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        ```

### Comprehensive Test Suite

!!! example "Complete Test Coverage"
    === "social_media.test.jac"
        ```jac
        # social_media.test.jac

        # Test basic profile creation and updates
        test create_and_update_profile {
            # Test profile creation
            root spawn visit_profile();
            profile = [root --> Profile][0];
            check isinstance(profile, Profile);
            check profile.username == "";
            check profile.follower_count == 0;

            # Test profile update
            root spawn update_profile(
                new_username="alice",
                new_bio="Software developer"
            );
            updated_profile = [root --> Profile][0];
            check updated_profile.username == "alice";
            check updated_profile.bio == "Software developer";
        }

        # Test following functionality
        test follow_and_unfollow_users {
            # Create main user profile
            root spawn visit_profile();
            root spawn update_profile(new_username="alice");

            # Create another user to follow
            bob_profile = Profile(username="bob");

            # Test following
            bob_profile spawn follow_request();
            follow_edge = [root --> Profile ->:Follow:-> Profile][0];
            check follow_edge.username == "bob";
            check bob_profile.follower_count == 1;

            # Test follow edge properties
            follow_edges = [edge [root --> Profile] ->:Follow:-> bob_profile];
            check len(follow_edges) == 1;
            check hasattr(follow_edges[0], "followed_at");

            # Test unfollowing
            bob_profile spawn unfollow_request();
            remaining_follows = [root --> Profile ->:Follow:-> Profile];
            check len(remaining_follows) == 0;
            check bob_profile.follower_count == 0;
        }

        # Test tweet creation and management
        test tweet_lifecycle {
            # Ensure we have a profile
            root spawn visit_profile();
            root spawn update_profile(new_username="alice");

            # Test tweet creation
            root spawn create_tweet(content="Hello world!");
            tweet = [root --> Profile ->:Post:-> Tweet][0];
            check tweet.content == "Hello world!";
            check isinstance(tweet, Tweet);
            check hasattr(tweet, "created_at");

            # Test tweet update
            tweet spawn update_tweet(updated_content="Hello updated world!");
            check tweet.content == "Hello updated world!";

            # Test multiple tweets
            root spawn create_tweet(content="Second tweet");
            all_tweets = [root --> Profile ->:Post:-> Tweet];
            check len(all_tweets) == 2;

            # Test tweet deletion
            tweet spawn remove_tweet();
            remaining_tweets = [root --> Profile ->:Post:-> Tweet];
            check len(remaining_tweets) == 1;
            check remaining_tweets[0].content == "Second tweet";
        }

        # Test liking functionality
        test like_and_unlike_tweets {
            # Setup: Create profile and tweet
            root spawn visit_profile();
            root spawn update_profile(new_username="alice");
            root spawn create_tweet(content="Likeable tweet");

            tweet = [root --> Profile ->:Post:-> Tweet][0];
            check tweet.like_count == 0;

            # Test liking
            tweet spawn like_tweet();
            check tweet.like_count == 1;

            # Verify like relationship exists
            like_edges = [edge [root --> Profile] ->:Like:-> tweet];
            check len(like_edges) == 1;

            # Test double-liking (should fail)
            result = tweet spawn like_tweet();
            check tweet.like_count == 1;  # Should remain 1

            # Test unliking
            tweet spawn unlike_tweet();
            check tweet.like_count == 0;

            # Verify like relationship removed
            remaining_likes = [edge [root --> Profile] ->:Like:-> tweet];
            check len(remaining_likes) == 0;
        }

        # Test commenting functionality
        test comment_system {
            # Setup: Create profile and tweet
            root spawn visit_profile();
            root spawn update_profile(new_username="alice");
            root spawn create_tweet(content="Tweet for comments");

            tweet = [root --> Profile ->:Post:-> Tweet][0];

            # Test commenting
            tweet spawn comment_on_tweet(content="Great tweet!");
            comments = [tweet ->:CommentOn:-> Comment];
            check len(comments) == 1;
            check comments[0].content == "Great tweet!";

            # Test multiple comments
            tweet spawn comment_on_tweet(content="I agree!");
            all_comments = [tweet ->:CommentOn:-> Comment];
            check len(all_comments) == 2;

            # Test comment update
            first_comment = all_comments[0];
            first_comment spawn update_comment(updated_content="Updated comment");
            check first_comment.content == "Updated comment";

            # Test comment deletion
            first_comment spawn remove_comment();
            remaining_comments = [tweet ->:CommentOn:-> Comment];
            check len(remaining_comments) == 1;
        }

        # Test complex graph relationships
        test complex_social_graph {
            # Create multiple users
            root spawn visit_profile();
            root spawn update_profile(new_username="alice");

            bob = Profile(username="bob");
            charlie = Profile(username="charlie");

            # Create follow relationships: alice -> bob -> charlie
            bob spawn follow_request();
            charlie spawn follow_request();  # alice follows charlie too

            # Alice creates a tweet
            root spawn create_tweet(content="Alice's tweet");
            alice_tweet = [root --> Profile ->:Post:-> Tweet][0];

            # Bob likes Alice's tweet
            # (Note: In a real system, you'd switch user context)
            alice_tweet spawn like_tweet();

            # Verify complex relationships
            alice_profile = [root --> Profile][0];
            alice_following = [alice_profile ->:Follow:-> Profile];
            check len(alice_following) == 2;  # follows bob and charlie

            alice_tweets = [alice_profile ->:Post:-> Tweet];
            check len(alice_tweets) == 1;

            tweet_likes = [alice_tweets[0] <-:Like:<- Profile];
            check len(tweet_likes) == 1;  # liked by alice (herself)
        }

        # Test error conditions and edge cases
        test error_conditions {
            # Test operations without profile
            try {
                root spawn create_tweet(content="No profile tweet");
                check False;  # Should not reach here
            } except Exception {
                check True;  # Expected behavior
            }

            # Create profile for other tests
            root spawn visit_profile();
            root spawn update_profile(new_username="test_user");

            # Test self-follow prevention
            alice_profile = [root --> Profile][0];
            result = alice_profile spawn follow_request();

            # Should not create self-follow
            self_follows = [alice_profile ->:Follow:-> alice_profile];
            check len(self_follows) == 0;
        }

        # Performance and stress testing
        test performance_operations {
            # Setup
            root spawn visit_profile();
            root spawn update_profile(new_username="performance_user");

            # Create multiple tweets quickly
            for i in range(10) {
                root spawn create_tweet(content=f"Tweet number {i}");
            }

            all_tweets = [root --> Profile ->:Post:-> Tweet];
            check len(all_tweets) == 10;

            # Like all tweets
            for tweet in all_tweets {
                tweet spawn like_tweet();
            }

            # Verify all likes
            for tweet in all_tweets {
                check tweet.like_count == 1;
            }

            # Test batch operations work correctly
            total_likes = sum([tweet.like_count for tweet in all_tweets]);
            check total_likes == 10;
        }
        ```

    === "Python Test Equivalent"
        ```python
        # test_social_media.py
        import unittest
        from social_media import Profile, Tweet, Comment

        class TestSocialMedia(unittest.TestCase):
            def setUp(self):
                self.alice = Profile("alice", "Software developer")
                self.bob = Profile("bob", "Designer")

            def test_create_and_update_profile(self):
                profile = Profile()
                self.assertEqual(profile.username, "")
                self.assertEqual(profile.follower_count, 0)

                profile.username = "alice"
                profile.bio = "Software developer"
                self.assertEqual(profile.username, "alice")

            def test_follow_and_unfollow_users(self):
                # Test following
                success = self.alice.follow(self.bob)
                self.assertTrue(success)
                self.assertIn(self.bob, self.alice.following)
                self.assertEqual(self.bob.follower_count, 1)

                # Test unfollowing
                success = self.alice.unfollow(self.bob)
                self.assertTrue(success)
                self.assertNotIn(self.bob, self.alice.following)
                self.assertEqual(self.bob.follower_count, 0)

            def test_tweet_lifecycle(self):
                tweet = Tweet("Hello world!", self.alice)
                self.alice.tweets.append(tweet)

                self.assertEqual(tweet.content, "Hello world!")
                self.assertEqual(len(self.alice.tweets), 1)

                # Update tweet
                tweet.content = "Hello updated world!"
                self.assertEqual(tweet.content, "Hello updated world!")

            def test_like_and_unlike_tweets(self):
                tweet = Tweet("Likeable tweet", self.alice)

                # Test liking
                success = tweet.like(self.bob)
                self.assertTrue(success)
                self.assertEqual(tweet.like_count, 1)

                # Test double-liking
                success = tweet.like(self.bob)
                self.assertFalse(success)
                self.assertEqual(tweet.like_count, 1)

                # Test unliking
                success = tweet.unlike(self.bob)
                self.assertTrue(success)
                self.assertEqual(tweet.like_count, 0)

        if __name__ == '__main__':
            unittest.main()
        ```

---

## Debugging Spatial Applications

Debugging spatial applications requires understanding graph state and walker movement patterns.

### Debug Output and Tracing

!!! example "Debug Walker for Graph Inspection"
    ```jac
    # debug_walker.jac
    walker debug_graph {
        has visited_nodes: list[str] = [];
        has visited_edges: list[str] = [];
        has max_depth: int = 3;
        has current_depth: int = 0;

        can debug_node with Profile entry {
            if self.current_depth >= self.max_depth {
                print(f"Max depth {self.max_depth} reached at {here.username}");
                return;
            }

            node_info = f"Profile: {here.username} (followers: {here.follower_count})";
            self.visited_nodes.append(node_info);
            print(f"Depth {self.current_depth}: {node_info}");

            # Debug outgoing relationships
            following = [->:Follow:->];
            tweets = [->:Post:->];

            print(f"  Following: {len(following)} users");
            print(f"  Posted: {len(tweets)} tweets");

            # Visit connected nodes
            self.current_depth += 1;
            visit following;
            visit tweets;
            self.current_depth -= 1;
        }

        can debug_tweet with Tweet entry {
            tweet_info = f"Tweet: '{here.content[:30]}...' (likes: {here.like_count})";
            self.visited_nodes.append(tweet_info);
            print(f"Depth {self.current_depth}: {tweet_info}");

            # Debug tweet relationships
            likes = [<-:Like:<-];
            comments = [->:CommentOn:->];

            print(f"  Liked by: {len(likes)} users");
            print(f"  Comments: {len(comments)}");
        }

        can debug_comment with Comment entry {
            comment_info = f"Comment: '{here.content[:20]}...'";
            self.visited_nodes.append(comment_info);
            print(f"Depth {self.current_depth}: {comment_info}");
        }
    }

    # Usage in tests
    test debug_graph_structure {
        # Setup complex graph
        root spawn visit_profile();
        root spawn update_profile(new_username="alice");
        root spawn create_tweet(content="Alice's first tweet");

        bob = Profile(username="bob");
        bob spawn follow_request();

        # Debug the graph
        debugger = debug_graph(max_depth=2);
        root spawn debugger;

        print("=== Debug Summary ===");
        print(f"Visited {len(debugger.visited_nodes)} nodes");
        for node in debugger.visited_nodes {
            print(f"  {node}");
        }
    }
    ```

### Walker State Inspection

!!! example "Walker State Testing"
    ```jac
    # walker_testing.jac
    walker feed_loader {
        has user_id: str;
        has loaded_tweets: list[dict] = [];
        has users_visited: set[str] = set();
        has errors: list[str] = [];

        can load_user_feed with Profile entry {
            if here.username in self.users_visited {
                self.errors.append(f"Duplicate visit to {here.username}");
                return;
            }

            self.users_visited.add(here.username);

            # Load user's tweets
            user_tweets = [->:Post:-> Tweet];
            for tweet in user_tweets {
                tweet_data = {
                    "author": here.username,
                    "content": tweet.content,
                    "likes": tweet.like_count,
                    "created_at": tweet.created_at
                };
                self.loaded_tweets.append(tweet_data);
            }

            # Visit followed users
            following = [->:Follow:-> Profile];
            visit following;
        }
    }

    test walker_state_management {
        # Setup test data
        root spawn visit_profile();
        root spawn update_profile(new_username="alice");
        root spawn create_tweet(content="Alice tweet 1");
        root spawn create_tweet(content="Alice tweet 2");

        bob = Profile(username="bob");
        bob spawn follow_request();
        bob spawn create_tweet(content="Bob's tweet");

        # Test walker state
        loader = feed_loader(user_id="alice");
        root spawn loader;

        # Verify walker state
        check len(loader.loaded_tweets) >= 2;  # At least Alice's tweets
        check "alice" in loader.users_visited;
        check "bob" in loader.users_visited;
        check len(loader.errors) == 0;

        # Verify tweet data structure
        alice_tweets = [t for t in loader.loaded_tweets if t["author"] == "alice"];
        check len(alice_tweets) == 2;

        for tweet in alice_tweets {
            check "content" in tweet;
            check "likes" in tweet;
            check "created_at" in tweet;
        }
    }
    ```

---

## Performance Testing and Optimization

Performance testing ensures your spatial applications scale effectively.

### Benchmark Testing

!!! example "Performance Benchmarks"
    ```jac
    # performance_tests.jac
    import time;

    test large_graph_performance {
        start_time = time.time();

        # Create large social network
        root spawn visit_profile();
        root spawn update_profile(new_username="central_user");

        # Create many users and connections
        num_users = 100;
        users = [];

        for i in range(num_users) {
            user = Profile(username=f"user_{i}");
            users.append(user);

            # Every 10th user follows central user
            if i % 10 == 0 {
                user spawn follow_request();
            }
        }

        creation_time = time.time() - start_time;
        print(f"Created {num_users} users in {creation_time:.2f} seconds");

        # Test graph traversal performance
        start_time = time.time();

        # Count all followers
        central_user = [root --> Profile][0];
        followers = [central_user <-:Follow:<- Profile];

        traversal_time = time.time() - start_time;
        print(f"Traversed {len(followers)} followers in {traversal_time:.4f} seconds");

        # Performance assertions
        check creation_time < 5.0;  # Should create 100 users in under 5 seconds
        check traversal_time < 0.1;  # Should traverse quickly
        check len(followers) == 10;  # Every 10th user = 10 followers
    }

    test memory_efficiency {
        # Test memory usage with large datasets
        initial_profiles = len([root --> Profile]);

        # Create and delete many objects
        for batch in range(5) {
            # Create batch of tweets
            for i in range(20) {
                root spawn create_tweet(content=f"Batch {batch} tweet {i}");
            }

            # Delete half of them
            tweets = [root --> Profile ->:Post:-> Tweet];
            for i in range(10) {
                if len(tweets) > i {
                    tweets[i] spawn remove_tweet();
                }
            }
        }

        # Check memory cleanup
        final_tweets = [root --> Profile ->:Post:-> Tweet];
        check len(final_tweets) <= 50;  # Should not accumulate indefinitely

        final_profiles = len([root --> Profile]);
        check final_profiles == initial_profiles + 1;  # Only the test profile added
    }

    test concurrent_operations {
        # Simulate concurrent-like operations
        root spawn visit_profile();
        root spawn update_profile(new_username="concurrent_user");

        # Create multiple walkers that operate simultaneously
        walkers = [];
        for i in range(10) {
            walker = create_tweet(content=f"Concurrent tweet {i}");
            walkers.append(walker);
        }

        # Execute all walkers
        start_time = time.time();
        for walker in walkers {
            root spawn walker;
        }
        execution_time = time.time() - start_time;

        # Verify all operations completed
        all_tweets = [root --> Profile ->:Post:-> Tweet];
        check len(all_tweets) == 10;

        # Performance check
        check execution_time < 1.0;  # Should complete quickly

        print(f"Executed {len(walkers)} operations in {execution_time:.4f} seconds");
    }
    ```

### Memory and Resource Testing

!!! example "Resource Usage Tests"
    ```jac
    # resource_tests.jac
    import gc;
    import psutil;
    import os;

    test memory_usage_monitoring {
        # Get initial memory usage
        process = psutil.Process(os.getpid());
        initial_memory = process.memory_info().rss;

        # Create large graph structure
        root spawn visit_profile();
        root spawn update_profile(new_username="memory_test_user");

        # Create many interconnected objects
        for i in range(1000) {
            root spawn create_tweet(content=f"Memory test tweet {i}");
        }

        # Force garbage collection
        gc.collect();

        # Check memory after creation
        after_creation_memory = process.memory_info().rss;
        memory_increase = after_creation_memory - initial_memory;

        print(f"Memory increase: {memory_increase / 1024 / 1024:.2f} MB");

        # Clean up
        tweets = [root --> Profile ->:Post:-> Tweet];
        for tweet in tweets {
            tweet spawn remove_tweet();
        }

        # Force garbage collection again
        gc.collect();

        # Check memory after cleanup
        final_memory = process.memory_info().rss;
        memory_recovered = after_creation_memory - final_memory;

        print(f"Memory recovered: {memory_recovered / 1024 / 1024:.2f} MB");

        # Memory should not grow indefinitely
        check memory_increase < 100 * 1024 * 1024;  # Less than 100MB increase
        check memory_recovered > memory_increase * 0.5;  # At least 50% recovered
    }
    ```

---

## Test-Driven Development with OSP

TDD works naturally with Jac's spatial programming model.

### TDD Example: Building a Recommendation System

!!! example "TDD Recommendation System"
    ```jac
    # recommendation_system.test.jac

    # Test 1: Basic recommendation structure
    test recommendation_system_structure {
        # Red: This will fail initially
        root spawn visit_profile();
        root spawn update_profile(new_username="test_user");

        # Should be able to get recommendations
        recommendations = root spawn get_recommendations(limit=5);
        check isinstance(recommendations, list);
        check len(recommendations) <= 5;
    }

    # Test 2: Friend-based recommendations
    test friend_based_recommendations {
        # Setup: Create network
        root spawn visit_profile();
        root spawn update_profile(new_username="alice");

        bob = Profile(username="bob");
        charlie = Profile(username="charlie");

        # Alice follows Bob, Bob follows Charlie
        bob spawn follow_request();
        # Switch context to Bob and follow Charlie
        # (In real system, would handle user context switching)

        # Alice should get Charlie recommended (friend of friend)
        recommendations = root spawn get_recommendations(limit=5);
        recommended_usernames = [rec["username"] for rec in recommendations];

        # Charlie should be recommended as friend of friend
        check "charlie" in recommended_usernames;
    }

    # Test 3: Interest-based recommendations
    test interest_based_recommendations {
        # Setup users with similar interests (tweets)
        root spawn visit_profile();
        root spawn update_profile(new_username="alice");
        root spawn create_tweet(content="I love programming");

        bob = Profile(username="bob");
        bob spawn create_tweet(content="Programming is awesome");

        charlie = Profile(username="charlie");
        charlie spawn create_tweet(content="I hate programming");

        # Get recommendations
        recommendations = root spawn get_recommendations(
            limit=5,
            algorithm="interest_based"
        );

        # Bob should rank higher than Charlie due to similar interests
        bob_score = 0;
        charlie_score = 0;

        for rec in recommendations {
            if rec["username"] == "bob" {
                bob_score = rec["score"];
            }
            if rec["username"] == "charlie" {
                charlie_score = rec["score"];
            }
        }

        check bob_score > charlie_score;
    }

    # Test 4: Recommendation filtering
    test recommendation_filtering {
        # Setup
        root spawn visit_profile();
        root spawn update_profile(new_username="alice");

        # Create users alice already follows
        bob = Profile(username="bob");
        bob spawn follow_request();

        # Create users alice doesn't follow
        charlie = Profile(username="charlie");
        diana = Profile(username="diana");

        # Get recommendations
        recommendations = root spawn get_recommendations(limit=10);
        recommended_usernames = [rec["username"] for rec in recommendations];

        # Should not recommend users already followed
        check "bob" not in recommended_usernames;

        # Should recommend unfollowed users
        check "charlie" in recommended_usernames or "diana" in recommended_usernames;
    }
    ```

    Now implement the actual recommendation system to make tests pass:

    ```jac
    # recommendation_system.jac
    walker get_recommendations(visit_profile) {
        has limit: int = 5;
        has algorithm: str = "hybrid";
        has recommendations: list[dict] = [];

        can generate_recommendations with Profile entry {
            current_user = here;
            followed_users = [->:Follow:-> Profile];
            followed_usernames = set([user.username for user in followed_users]);

            # Get all users except current and already followed
            all_users = [root --> Profile](?username != current_user.username);
            candidate_users = [user for user in all_users
                             if user.username not in followed_usernames];

            # Score each candidate
            for candidate in candidate_users {
                score = self.calculate_recommendation_score(
                    current_user, candidate, followed_users
                );

                if score > 0 {
                    self.recommendations.append({
                        "username": candidate.username,
                        "score": score,
                        "reason": self.get_recommendation_reason(
                            current_user, candidate, followed_users
                        )
                    });
                }
            }

            # Sort by score and limit results
            self.recommendations.sort(key=lambda x: x["score"], reverse=True);
            self.recommendations = self.recommendations[:self.limit];

            report self.recommendations;
        }

        def calculate_recommendation_score(
            current_user: Profile,
            candidate: Profile,
            followed_users: list[Profile]
        ) -> float {
            score = 0.0;

            if self.algorithm in ["friend_based", "hybrid"] {
                # Friend of friend scoring
                candidate_followers = [candidate <-:Follow:<- Profile];
                mutual_connections = set([u.username for u in followed_users]) &
                                   set([u.username for u in candidate_followers]);
                score += len(mutual_connections) * 2.0;
            }

            if self.algorithm in ["interest_based", "hybrid"] {
                # Interest-based scoring using tweet content
                current_tweets = [current_user ->:Post:-> Tweet];
                candidate_tweets = [candidate ->:Post:-> Tweet];

                # Simple keyword matching (in real system, use embeddings)
                current_words = set();
                for tweet in current_tweets {
                    current_words.update(tweet.content.lower().split());
                }

                candidate_words = set();
                for tweet in candidate_tweets {
                    candidate_words.update(tweet.content.lower().split());
                }

                common_words = current_words & candidate_words;
                score += len(common_words) * 0.5;
            }

            return score;
        }

        def get_recommendation_reason(
            current_user: Profile,
            candidate: Profile,
            followed_users: list[Profile]
        ) -> str {
            # Determine primary reason for recommendation
            candidate_followers = [candidate <-:Follow:<- Profile];
            mutual_connections = set([u.username for u in followed_users]) &
                               set([u.username for u in candidate_followers]);

            if mutual_connections {
                return f"Friends with {list(mutual_connections)[0]}";
            }

            return "Similar interests";
        }
    }
    ```

---

## Best Practices

!!! summary "Testing Best Practices"
    - **Write tests first**: Use test-driven development for complex walker logic
    - **Test graph structures**: Verify node and edge relationships are correct
    - **Use descriptive names**: Make test intentions clear from the test name
    - **Test edge cases**: Include boundary conditions and error scenarios
    - **Isolate test data**: Ensure tests don't interfere with each other
    - **Mock external dependencies**: Test walker logic independently of external services

## Key Takeaways

!!! summary "What We've Learned"
    **Testing Framework:**

    - **Built-in testing**: Native test blocks eliminate external framework dependencies
    - **Graph testing**: Specialized patterns for testing spatial relationships
    - **Walker testing**: Comprehensive testing of mobile computation patterns
    - **Type-safe assertions**: Leverage Jac's type system in test validation

    **Debugging Techniques:**

    - **Debug output**: Strategic print statements and debug flags
    - **Walker tracing**: Track walker movement through graph structures
    - **State inspection**: Examine node and edge states during execution
    - **Error handling**: Graceful handling of edge cases and failures

    **Test Organization:**

    - **Modular testing**: Organize tests by functionality and complexity
    - **Helper functions**: Reusable setup code for consistent test environments
    - **Performance testing**: Monitor execution time and resource usage
    - **Integration testing**: Test interactions between multiple walkers

    **Quality Assurance:**

    - **Comprehensive coverage**: Test all code paths and error conditions
    - **Regression prevention**: Automated tests prevent breaking changes
    - **Documentation value**: Tests serve as executable specifications
    - **Continuous validation**: Automated testing in CI/CD pipelines

!!! tip "Try It Yourself"
    Enhance your testing skills by:
    - Writing comprehensive test suites for existing walker logic
    - Implementing performance benchmarks for graph operations
    - Creating integration tests for multi-walker scenarios
    - Adding debug instrumentation to complex graph traversals

    Remember: Good tests make development faster and more reliable!

---

*Ready to learn about deployment strategies? Continue to [Chapter 19: Deployment Strategies](chapter_18.md)!*

# Chapter 19: Deployment Strategies

In this chapter, we'll explore how to deploy Jac applications to production environments. We'll take our weather API from development to production using various deployment strategies including Docker, Kubernetes, and Jac Cloud.

!!! info "What You'll Learn"
    - Local vs cloud deployment comparison
    - Docker containerization for Jac applications
    - Kubernetes orchestration and scaling
    - Jac Cloud deployment with real examples
    - Production monitoring and maintenance

---

## Local to Cloud Deployment

One of Jac's most powerful features is its scale-agnostic nature - the same code that runs locally can be deployed to production without changes.

!!! success "Deployment Benefits"
    - **Zero Code Changes**: Same application runs locally and in production
    - **Automatic Scaling**: Built-in support for horizontal scaling
    - **Container Ready**: Applications naturally containerize
    - **Kubernetes Native**: Seamless Kubernetes integration
    - **Production Features**: Built-in monitoring, logging, and health checks

### Development to Production Pipeline

Let's start with our weather API and show how it progresses from development to production:

!!! example "Weather API Deployment Journey"
    === "Development (Local)"
        ```jac
        # weather_api.jac - Same code at every stage
        import from os { getenv }

        glob config = {
            "api_key": getenv("WEATHER_API_KEY", "dev-key"),
            "cache_timeout": int(getenv("CACHE_TIMEOUT", "300")),
            "debug": getenv("DEBUG", "true").lower() == "true"
        };

        node WeatherData {
            has city: str;
            has temperature: float;
            has description: str;
            has last_updated: str;
        }

        walker get_weather {
            has city: str;

            can fetch_weather with `root entry {
                # Check cache first
                cached = [-->(`?WeatherData)](?city == self.city);

                if cached {
                    weather = cached[0];
                    report {
                        "city": weather.city,
                        "temperature": weather.temperature,
                        "description": weather.description,
                        "cached": True
                    };
                } else {
                    # Simulate API call
                    new_weather = WeatherData(
                        city=self.city,
                        temperature=22.5,
                        description="Sunny",
                        last_updated="2024-01-15T10:00:00Z"
                    );
                    here ++> new_weather;

                    report {
                        "city": self.city,
                        "temperature": 22.5,
                        "description": "Sunny",
                        "cached": False
                    };
                }
            }
        }

        walker health_check {
            can check_health with `root entry {
                weather_count = len([-->(`?WeatherData)]);
                report {
                    "status": "healthy",
                    "cached_cities": weather_count,
                    "debug_mode": config["debug"]
                };
            }
        }
        ```

    === "Local Development"
        ```bash
        # Development workflow
        export DEBUG=true
        export WEATHER_API_KEY=dev-key

        # Run locally for development
        jac run weather_api.jac

        # Test as service locally
        jac serve weather_api.jac --port 8000

        # Test the endpoints
        curl -X POST http://localhost:8000/walker/get_weather \
          -H "Content-Type: application/json" \
          -d '{"city": "New York"}'
        ```

---

## Docker Containerization

Docker packaging makes your Jac applications portable and consistent across environments.

### Basic Dockerfile for Jac Applications

!!! example "Jac Application Dockerfile"
    === "Dockerfile"
        ```dockerfile
        # Dockerfile
        FROM python:3.11-slim

        # Set working directory
        WORKDIR /app

        # Install system dependencies
        RUN apt-get update && apt-get install -y \
            curl \
            && rm -rf /var/lib/apt/lists/*

        # Install Jac
        RUN pip install jaclang

        # Copy application files
        COPY weather_api.jac .
        COPY requirements.txt .

        # Install Python dependencies if any
        RUN pip install -r requirements.txt

        # Expose port
        EXPOSE 8000

        # Set environment variables
        ENV JAC_FILE=weather_api.jac
        ENV PORT=8000
        ENV DEBUG=false

        # Health check
        HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
            CMD curl -f http://localhost:$PORT/walker/health_check -X POST -H "Content-Type: application/json" -d '{}' || exit 1

        # Run the application
        CMD jac serve $JAC_FILE --port $PORT
        ```

    === "requirements.txt"
        ```txt
        # requirements.txt
        jaclang
        requests
        python-dotenv
        ```

    === "docker-compose.yml"
        ```yaml
        # docker-compose.yml
        version: '3.8'

        services:
          weather-api:
            build: .
            ports:
              - "8000:8000"
            environment:
              - DEBUG=false
              - WEATHER_API_KEY=${WEATHER_API_KEY}
              - CACHE_TIMEOUT=600
            volumes:
              - weather_data:/app/data
            restart: unless-stopped
            healthcheck:
              test: ["CMD", "curl", "-f", "http://localhost:8000/walker/health_check", "-X", "POST", "-H", "Content-Type: application/json", "-d", "{}"]
              interval: 30s
              timeout: 10s
              retries: 3

        volumes:
          weather_data:
        ```

### Building and Testing Docker Images

```bash
# Build the image
docker build -t weather-api:latest .

# Test locally
docker run -p 8000:8000 \
  -e WEATHER_API_KEY=your-key \
  -e DEBUG=true \
  weather-api:latest

# Test with docker-compose
echo "WEATHER_API_KEY=your-key" > .env
docker-compose up -d

# View logs
docker-compose logs -f weather-api

# Test the containerized API
curl -X POST http://localhost:8000/walker/get_weather \
  -H "Content-Type: application/json" \
  -d '{"city": "London"}'

# Check health
curl -X POST http://localhost:8000/walker/health_check \
  -H "Content-Type: application/json" \
  -d '{}'
```

---

## Kubernetes Deployment

Kubernetes provides orchestration, scaling, and reliability for production deployments.

### Kubernetes Manifests

!!! example "Complete Kubernetes Deployment"
    === "namespace.yaml"
        ```yaml
        # k8s/namespace.yaml
        apiVersion: v1
        kind: Namespace
        metadata:
          name: weather-app
          labels:
            app: weather-api
        ```

    === "configmap.yaml"
        ```yaml
        # k8s/configmap.yaml
        apiVersion: v1
        kind: ConfigMap
        metadata:
          name: weather-config
          namespace: weather-app
        data:
          DEBUG: "false"
          CACHE_TIMEOUT: "600"
          PORT: "8000"
        ```

    === "secret.yaml"
        ```yaml
        # k8s/secret.yaml
        apiVersion: v1
        kind: Secret
        metadata:
          name: weather-secrets
          namespace: weather-app
        type: Opaque
        data:
          weather-api-key: eW91ci1iYXNlNjQtZW5jb2RlZC1hcGkta2V5  # Base64 encoded
        ```

    === "deployment.yaml"
        ```yaml
        # k8s/deployment.yaml
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: weather-api
          namespace: weather-app
          labels:
            app: weather-api
        spec:
          replicas: 3
          selector:
            matchLabels:
              app: weather-api
          template:
            metadata:
              labels:
                app: weather-api
            spec:
              containers:
              - name: weather-api
                image: weather-api:latest
                ports:
                - containerPort: 8000
                env:
                - name: WEATHER_API_KEY
                  valueFrom:
                    secretKeyRef:
                      name: weather-secrets
                      key: weather-api-key
                envFrom:
                - configMapRef:
                    name: weather-config
                resources:
                  limits:
                    cpu: "1"
                    memory: "1Gi"
                  requests:
                    cpu: "500m"
                    memory: "512Mi"
                livenessProbe:
                  httpPost:
                    path: /walker/health_check
                    port: 8000
                    httpHeaders:
                    - name: Content-Type
                      value: application/json
                  initialDelaySeconds: 30
                  periodSeconds: 30
                readinessProbe:
                  httpPost:
                    path: /walker/health_check
                    port: 8000
                    httpHeaders:
                    - name: Content-Type
                      value: application/json
                  initialDelaySeconds: 5
                  periodSeconds: 10
        ```

    === "service.yaml"
        ```yaml
        # k8s/service.yaml
        apiVersion: v1
        kind: Service
        metadata:
          name: weather-api-service
          namespace: weather-app
        spec:
          selector:
            app: weather-api
          ports:
          - protocol: TCP
            port: 80
            targetPort: 8000
          type: ClusterIP
        ```

    === "ingress.yaml"
        ```yaml
        # k8s/ingress.yaml
        apiVersion: networking.k8s.io/v1
        kind: Ingress
        metadata:
          name: weather-api-ingress
          namespace: weather-app
          annotations:
            kubernetes.io/ingress.class: "nginx"
            nginx.ingress.kubernetes.io/rewrite-target: /
        spec:
          rules:
          - host: weather-api.yourdomain.com
            http:
              paths:
              - path: /
                pathType: Prefix
                backend:
                  service:
                    name: weather-api-service
                    port:
                      number: 80
        ```

### Deploying to Kubernetes

```bash
# Deploy everything in order
kubectl apply -f k8s/namespace.yaml
kubectl apply -f k8s/configmap.yaml

# Create secret with your actual API key
echo -n "your-actual-api-key" | base64
# Update secret.yaml with the base64 value
kubectl apply -f k8s/secret.yaml

kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
kubectl apply -f k8s/ingress.yaml

# Check deployment status
kubectl get all -n weather-app

# Watch pods come online
kubectl get pods -n weather-app -w

# Check logs
kubectl logs -f deployment/weather-api -n weather-app

# Test the service
kubectl port-forward service/weather-api-service 8080:80 -n weather-app

# Test from another terminal
curl -X POST http://localhost:8080/walker/get_weather \
  -H "Content-Type: application/json" \
  -d '{"city": "Tokyo"}'
```

---

## Jac Cloud Deployment

Jac Cloud provides a Kubernetes-based deployment template to easily deploy your service into your cluster.

### Jac Cloud Setup

!!! example "Jac Cloud Deployment"
    === "Directory Structure"
        ```
        jac-cloud/
        ├── scripts/
        │   ├── Dockerfile
        │   ├── init_jac_cloud.sh
        │   └── jac-cloud.yml
        └── weather_api.jac
        ```

    === "Prerequisites"
        ```bash
        # Prerequisites for Jac Cloud deployment
        # 1. Kubernetes cluster access
        kubectl cluster-info

        # 2. Docker for building images
        docker --version

        # 3. kubectl configured
        kubectl config current-context

        # 4. Target namespace should be created before deployment
        kubectl create namespace littlex

        # 5. Optional: OpenAI API key
        echo "OPENAI_API_KEY=your-key-here" > .env
        ```

    === "Build and Deploy"
        ```bash
        # 1. Build and push Docker image
        docker build -t your-dockerhub-username/jac-cloud:latest -f jac-cloud/scripts/Dockerfile .
        docker push your-dockerhub-username/jac-cloud:latest

        # 2. Update image reference in jac-cloud.yml
        sed -i 's|image: .*|image: your-dockerhub-username/jac-cloud:latest|' jac-cloud/scripts/jac-cloud.yml


        # 4. Deploy Jac Cloud application
        kubectl apply -f jac-cloud/scripts/jac-cloud.yml

        # 5. Verify deployment
        kubectl get all -n littlex
        ```

### Jac Cloud Configuration Files

!!! example "Complete Jac Cloud Configuration"
    === "jac-cloud.yml"
        ```yaml
        # jac-cloud/scripts/jac-cloud.yml
        apiVersion: v1
        kind: Namespace
        metadata:
          name: littlex
        ---
        apiVersion: v1
        kind: ServiceAccount
        metadata:
          name: jac-cloud-sa
          namespace: littlex
        ---
        apiVersion: rbac.authorization.k8s.io/v1
        kind: ClusterRole
        metadata:
          name: jac-cloud-role
        rules:
        - apiGroups: [""]
          resources: ["pods", "services", "configmaps"]
          verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
        - apiGroups: ["apps"]
          resources: ["deployments"]
          verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
        ---
        apiVersion: rbac.authorization.k8s.io/v1
        kind: ClusterRoleBinding
        metadata:
          name: jac-cloud-binding
        roleRef:
          apiGroup: rbac.authorization.k8s.io
          kind: ClusterRole
          name: jac-cloud-role
        subjects:
        - kind: ServiceAccount
          name: jac-cloud-sa
          namespace: littlex
        ---
        apiVersion: v1
        kind: Secret
        metadata:
          name: openai-secret
          namespace: littlex
        type: Opaque
        data:
          openai-key: eW91ci1iYXNlNjQtZW5jb2RlZC1rZXk=  # Replace with your base64 encoded key
        ---
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: jac-cloud
          namespace: littlex
        spec:
          replicas: 1
          selector:
            matchLabels:
              app: jac-cloud
          template:
            metadata:
              labels:
                app: jac-cloud
            spec:
              serviceAccountName: jac-cloud-sa
              containers:
              - name: jac-cloud
                image: your-dockerhub-username/jac-cloud:latest
                ports:
                - containerPort: 8000
                env:
                - name: NAMESPACE
                  value: "littlex"
                - name: FILE_NAME
                  value: "weather_api.jac"
                - name: OPENAI_API_KEY
                  valueFrom:
                    secretKeyRef:
                      name: openai-secret
                      key: openai-key
                volumeMounts:
                - name: config-volume
                  mountPath: /app/config
                resources:
                  limits:
                    cpu: "1"
                    memory: "2Gi"
                  requests:
                    cpu: "500m"
                    memory: "1Gi"
        ---
        apiVersion: v1
        kind: Service
        metadata:
          name: jac-cloud-service
          namespace: littlex
        spec:
          selector:
            app: jac-cloud
          ports:
          - protocol: TCP
            port: 80
            targetPort: 8000
          type: LoadBalancer
        ```

    === "Dockerfile"
        ```dockerfile
        # jac-cloud/scripts/Dockerfile
        FROM python:3.11-slim

        # Set working directory
        WORKDIR /app

        # Install system dependencies
        RUN apt-get update && apt-get install -y \
            curl \
            git \
            && rm -rf /var/lib/apt/lists/*

        # Install Jac and required packages
        RUN pip install jaclang

        # Copy application files
        COPY weather_api.jac .
        COPY jac-cloud/scripts/init_jac_cloud.sh .

        # Make scripts executable
        RUN chmod +x init_jac_cloud.sh

        # Expose port
        EXPOSE 8000

        # Set environment variables
        ENV JAC_FILE=weather_api.jac
        ENV PORT=8000
        ENV NAMESPACE=littlex

        # Health check
        HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
            CMD curl -f http://localhost:$PORT/walker/health_check -X POST -H "Content-Type: application/json" -d '{}' || exit 1

        # Run the application
        CMD ["./init_jac_cloud.sh"]
        ```

### Environment Variables and Configuration

The Jac Cloud deployment supports the following environment variables:

| Variable          | Description                              | Default Value |
|--------------------|------------------------------------------|---------------|
| `NAMESPACE`        | Target namespace for the deployment     | `default`     |
| `FILE_NAME`        | JAC file to execute in the pod          | `example.jac` |
| `OPENAI_API_KEY`   | OpenAI API key (from secret)            | None          |

### Step-by-Step Deployment Guide

Follow these steps to deploy your Jac application to Kubernetes:

```bash
# 1. Build and push Docker image
docker build -t your-dockerhub-username/jac-cloud:latest -f jac-cloud/scripts/Dockerfile .
docker push your-dockerhub-username/jac-cloud:latest

# 2. Update image reference in jac-cloud.yml
sed -i 's|image: .*|image: your-dockerhub-username/jac-cloud:latest|' jac-cloud/scripts/jac-cloud.yml

# 4. Deploy Jac Cloud application
kubectl apply -f jac-cloud/scripts/jac-cloud.yml

# 5. Verify deployment
kubectl get all -n littlex
```

### Monitoring and Updating

```bash
# Monitor logs in real-time
kubectl logs -f deployment/jac-cloud -n littlex

# Update configurations
# 1. Modify the YAML files as needed
# 2. Apply the changes:
kubectl apply -f jac-cloud/scripts/jac-cloud.yml

# Scale for handling more traffic
kubectl scale deployment jac-cloud --replicas=3 -n littlex

# Configure resource limits (edit jac-cloud.yml first)
# Then apply:
kubectl apply -f jac-cloud/scripts/jac-cloud.yml
```

### Troubleshooting and Validation

```bash
# Verify namespace exists
kubectl get namespaces

# Verify ConfigMap is properly applied
kubectl get configmap -n littlex

# Verify deployment status
kubectl get pods -n littlex

# View logs for troubleshooting
kubectl logs -f deployment/jac-cloud -n littlex

# Check all resources in the namespace
kubectl get all -n littlex
```

### Advanced Configuration Management

```bash
# Restart deployment to pick up config changes
kubectl rollout restart deployment/jac-cloud -n littlex

# Scale the deployment for increased capacity
kubectl scale deployment jac-cloud --replicas=3 -n littlex

# Check rollout status
kubectl rollout status deployment/jac-cloud -n littlex

# Adjust CPU and memory limits in jac-cloud.yml
# Then apply the changes:
kubectl apply -f jac-cloud/scripts/jac-cloud.yml
```

---

### Cleanup

To remove all deployed resources when you're done:

```bash
# Remove the entire namespace and all resources
kubectl delete namespace littlex

# This will delete all resources associated with your Jac Cloud deployment
```

---

## Production Monitoring and Maintenance

### Enhanced Health Checks and Metrics

!!! example "Production-Ready Weather API"
    ```jac
    # production_weather.jac
    import from datetime { datetime }
    import from time { time }

    glob metrics = {
        "requests_total": 0,
        "requests_per_city": {},
        "cache_hits": 0,
        "cache_misses": 0,
        "start_time": time()
    };

    node WeatherData {
        has city: str;
        has temperature: float;
        has description: str;
        has last_updated: str;
    }

    walker get_weather {
        has city: str;

        can fetch_weather with `root entry {
            # Update metrics
            metrics["requests_total"] += 1;
            metrics["requests_per_city"][self.city] = metrics["requests_per_city"].get(self.city, 0) + 1;

            # Check cache first
            cached = [-->(`?WeatherData)](?city == self.city);

            if cached {
                metrics["cache_hits"] += 1;
                weather = cached[0];
                report {
                    "city": weather.city,
                    "temperature": weather.temperature,
                    "description": weather.description,
                    "cached": True
                };
            } else {
                metrics["cache_misses"] += 1;
                # Simulate external API call
                new_weather = WeatherData(
                    city=self.city,
                    temperature=22.5,
                    description="Sunny",
                    last_updated=datetime.now().isoformat()
                );
                here ++> new_weather;

                report {
                    "city": self.city,
                    "temperature": 22.5,
                    "description": "Sunny",
                    "cached": False
                };
            }
        }
    }

    walker health_check {
        can check_health with `root entry {
            uptime = time() - metrics["start_time"];
            cache_hit_rate = metrics["cache_hits"] / max(metrics["requests_total"], 1) * 100;

            report {
                "status": "healthy",
                "uptime_seconds": uptime,
                "total_requests": metrics["requests_total"],
                "cache_hit_rate_percent": round(cache_hit_rate, 2),
                "cached_cities": len([-->(`?WeatherData)]),
                "timestamp": datetime.now().isoformat()
            };
        }
    }

    walker metrics_endpoint {
        can get_metrics with `root entry {
            uptime = time() - metrics["start_time"];
            cache_hit_rate = metrics["cache_hits"] / max(metrics["requests_total"], 1) * 100;

            report {
                "uptime_seconds": uptime,
                "total_requests": metrics["requests_total"],
                "cache_hit_rate_percent": round(cache_hit_rate, 2),
                "requests_by_city": metrics["requests_per_city"],
                "timestamp": datetime.now().isoformat()
            };
        }
    }

    walker detailed_health_check {
        can comprehensive_health with `root entry {
            cached_cities = len([-->(`?WeatherData)]);
            memory_usage = "healthy";  # Simplified for demo

            # Check if service is responding normally
            status = "healthy";
            if metrics["requests_total"] == 0 and time() - metrics["start_time"] > 300 {
                status = "warning";  # No requests in 5 minutes
            }

            report {
                "status": status,
                "cached_cities": cached_cities,
                "total_requests": metrics["requests_total"],
                "memory_status": memory_usage,
                "uptime_seconds": time() - metrics["start_time"],
                "version": "1.0.0"
            };
        }
    }
    ```

### Scaling and Performance

```bash
# Manual scaling
kubectl scale deployment jac-cloud --replicas=3 -n littlex

# Horizontal Pod Autoscaler
kubectl apply -f - <<EOF
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: jac-cloud-hpa
  namespace: littlex
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: jac-cloud
  minReplicas: 1
  maxReplicas: 5
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
EOF

# Monitor scaling
kubectl get hpa -n littlex -w

# Load testing (using hey or similar)
hey -n 1000 -c 10 -m POST \
  -H "Content-Type: application/json" \
  -d '{"city":"London"}' \
  http://your-service-url/walker/get_weather

# Check resource usage during load test
kubectl top pods -n littlex
```

---

## Best Practices

!!! summary "Deployment Guidelines"
    - **Create namespaces beforehand**: Ensure target namespaces exist before deployment
    - **Monitor resource usage**: Track CPU and memory consumption for optimal scaling
    - **Secure API keys**: Use Kubernetes secrets for sensitive configuration
    - **Plan for module dependencies**: Configure dependencies correctly in the module configuration
    - **Test locally first**: Verify your JAC application works before deploying to the cloud
    - **Version your deployments**: Tag Docker images with specific versions for reproducible deployments
    - **Clean up resources**: Always clean up test deployments to avoid unnecessary costs

## Key Takeaways

!!! summary "What We've Learned"
    **Deployment Strategies:**

    - **Local development**: Same code runs everywhere without modifications
    - **Docker containerization**: Package applications for consistent deployment
    - **Kubernetes orchestration**: Production-grade scaling and reliability
    - **Jac Cloud integration**: Kubernetes-based deployment with built-in module management

    **Production Features:**

    - **Module management**: Dynamic loading of Python modules with resource control
    - **RBAC security**: Proper service accounts and role-based access control
    - **Health checks**: Monitor application health and readiness
    - **Resource management**: Control CPU and memory usage effectively
    - **Scaling strategies**: Manual and automatic scaling based on demand
    - **Configuration management**: Secure and flexible environment configuration

    **Monitoring and Maintenance:**

    - **Metrics collection**: Track application performance and usage
    - **Log aggregation**: Centralized logging for debugging and analysis
    - **Resource monitoring**: Track module resource usage and optimization
    - **Performance optimization**: Identify and resolve bottlenecks
    - **Troubleshooting tools**: Use kubectl commands for debugging deployments

    **Best Practices:**

    - **Infrastructure as Code**: Version control your deployment configurations
    - **Module optimization**: Configure appropriate resource limits for each module
    - **Security hardening**: Implement security best practices at every layer
    - **Namespace isolation**: Use dedicated namespaces for different environments
    - **Progressive deployment**: Test in staging before production rollout

!!! tip "Try It Yourself"
    Practice deployment by:
    - Setting up the Jac Cloud deployment with your own Docker registry
    - Experimenting with different module configurations and resource limits
    - Implementing comprehensive monitoring and alerting systems
    - Testing scaling behavior under different load conditions
    - Creating CI/CD pipelines for automated deployment workflows

    Remember: Jac Cloud provides a production-ready platform with built-in best practices for JAC applications!

---

*Ready to optimize performance? Continue to [Chapter 20: Performance Optimization](chapter_19.md)!*

# Chapter 20: Performance Optimization

In this chapter, we'll explore techniques for optimizing Jac applications to achieve better performance in both local and distributed environments. We'll build and progressively optimize a friend-finding algorithm that demonstrates graph structure optimization, traversal efficiency, and memory management strategies.

!!! info "What You'll Learn"
    - Graph structure optimization techniques
    - Efficient traversal patterns and algorithms
    - Memory management strategies in Jac
    - Performance monitoring and profiling
    - Distributed performance considerations

---

## Graph Structure Optimization

The foundation of Jac performance lies in how you structure your graph data. Efficient graph design can dramatically improve traversal performance and reduce memory usage.

!!! success "Optimization Benefits"
    - **Faster Traversals**: Well-structured graphs enable efficient pathfinding
    - **Reduced Memory**: Optimized node relationships minimize storage overhead
    - **Better Scaling**: Efficient structures handle larger datasets gracefully
    - **Improved Caching**: Predictable access patterns enhance cache performance

### Traditional vs Optimized Graph Design

!!! example "Graph Design Comparison"
    === "Traditional Approach"
        ```python
        # inefficient_friends.py - Nested loops and redundant data
        class Person:
            def __init__(self, name, age):
                self.name = name
                self.age = age
                self.friends = []  # Direct list storage
                self.friend_data = {}  # Redundant friend information

            def add_friend(self, friend):
                self.friends.append(friend)
                friend.friends.append(self)  # Bidirectional
                # Store redundant data
                self.friend_data[friend.name] = {
                    'age': friend.age,
                    'mutual_friends': []
                }

            def find_mutual_friends(self, other_person):
                mutual = []
                # Inefficient nested loop
                for my_friend in self.friends:
                    for their_friend in other_person.friends:
                        if my_friend.name == their_friend.name:
                            mutual.append(my_friend)
                return mutual

            def friends_of_friends(self, max_depth=2):
                found = set()
                # Inefficient recursive traversal
                def traverse(person, depth):
                    if depth <= 0:
                        return
                    for friend in person.friends:
                        if friend.name != self.name:
                            found.add(friend.name)
                            traverse(friend, depth - 1)

                traverse(self, max_depth)
                return list(found)
        ```

    === "Jac Optimized Structure"
        ```jac
        # optimized_friends.jac - Efficient graph-native design
        node Person {
            has name: str;
            has age: int;
            has friend_count: int = 0;  # Cached for quick access

            def add_friend(friend: Person) -> bool {
                # Check if already connected to avoid duplicates
                existing = [self --> Friend --> Person](?name == friend.name);
                if existing {
                    return false;
                }

                # Create bidirectional connection efficiently
                friendship = Friend(since="2024-01-15");
                self ++> friendship ++> friend;

                # Update cached counters
                self.friend_count += 1;
                friend.friend_count += 1;
                return true;
            }
        }

        edge Friend {
            has since: str;
            has strength: int = 1;  # Relationship strength for weighted algorithms
        }

        walker find_mutual_friends {
            has person1_name: str;
            has person2_name: str;

            can find_efficiently with `root entry {
                # Direct graph traversal - no nested loops
                person1 = [-->(`?Person)](?name == self.person1_name);
                person2 = [-->(`?Person)](?name == self.person2_name);

                if not person1 or not person2 {
                    report {"error": "Person not found"};
                    return;
                }

                # Get friends using graph navigation
                person1_friends = [person1[0] --> Friend --> Person];
                person2_friends = [person2[0] --> Friend --> Person];

                # Efficient set intersection
                mutual_names = {f.name for f in person1_friends} & {f.name for f in person2_friends};

                report {
                    "mutual_friends": list(mutual_names),
                    "count": len(mutual_names)
                };
            }
        }
        ```
---

## Traversal Efficiency

Efficient graph traversal is crucial for performance in Object-Spatial Programming. Let's examine different approaches to finding friends-of-friends and optimize them progressively.

### Basic Friend-Finding Algorithm

!!! example "Simple Friend Discovery"
    === "Jac"
        ```jac
        # basic_friend_finder.jac
        walker find_friends_of_friends {
            has person_name: str;
            has max_depth: int = 2;
            has visited: set[str] = set();
            has results: set[str] = set();

            can traverse_network with `root entry {
                start_person = [-->(`?Person)](?name == self.person_name);

                if not start_person {
                    report {"error": "Person not found"};
                    return;
                }

                # Start traversal from the person
                self.traverse_from_person(start_person[0], self.max_depth);

                # Remove the starting person from results
                self.results.discard(self.person_name);

                report {
                    "person": self.person_name,
                    "friends_of_friends": list(self.results),
                    "total_found": len(self.results)
                };
            }

            def traverse_from_person(person: Person, remaining_depth: int) {
                if remaining_depth <= 0 or person.name in self.visited {
                    return;
                }

                self.visited.add(person.name);
                self.results.add(person.name);

                # Navigate to friends and continue traversal
                friends = [person --> Friend --> Person];
                for friend in friends {
                    self.traverse_from_person(friend, remaining_depth - 1);
                }
            }
        }
        ```

    === "Python Equivalent"
        ```python
        # basic_friend_finder.py - Less efficient traversal
        def find_friends_of_friends(person_name, people_data, max_depth=2):
            visited = set()
            results = set()

            def traverse(current_name, depth):
                if depth <= 0 or current_name in visited:
                    return

                visited.add(current_name)
                results.add(current_name)

                # Manual lookup in data structure
                if current_name in people_data:
                    friends = people_data[current_name].get('friends', [])
                    for friend_name in friends:
                        traverse(friend_name, depth - 1)

            traverse(person_name, max_depth)
            results.discard(person_name)  # Remove starting person

            return {
                "person": person_name,
                "friends_of_friends": list(results),
                "total_found": len(results)
            }
        ```

### Optimized Breadth-First Traversal

!!! example "Performance-Optimized Friend Finding"
    ```jac
    # optimized_friend_finder.jac
    import from collections { deque }

    walker find_friends_optimized {
        has person_name: str;
        has max_depth: int = 2;

        can breadth_first_search with `root entry {
            start_person = [-->(`?Person)](?name == self.person_name);

            if not start_person {
                report {"error": "Person not found"};
                return;
            }

            # Use BFS for more predictable performance
            queue = deque([(start_person[0], 0)]);  # (person, depth)
            visited = {self.person_name};
            results = set();

            while queue {
                current_person, depth = queue.popleft();

                if depth >= self.max_depth {
                    continue;
                }

                # Get friends efficiently
                friends = [current_person --> Friend --> Person];

                for friend in friends {
                    if friend.name not in visited {
                        visited.add(friend.name);
                        results.add(friend.name);
                        queue.append((friend, depth + 1));
                    }
                }
            }

            report {
                "person": self.person_name,
                "friends_of_friends": list(results),
                "total_found": len(results),
                "algorithm": "breadth_first"
            };
        }
    }

    # Cached version for repeated queries
    walker find_friends_cached {
        has person_name: str;
        has max_depth: int = 2;

        can cached_search with `root entry {
            start_person = [-->(`?Person)](?name == self.person_name);

            if not start_person {
                report {"error": "Person not found"};
                return;
            }

            person = start_person[0];

            # Check if we have cached results
            cache_nodes = [person --> CacheEntry](?depth == self.max_depth);

            if cache_nodes {
                cache = cache_nodes[0];
                report {
                    "person": self.person_name,
                    "friends_of_friends": cache.friend_names,
                    "total_found": len(cache.friend_names),
                    "cached": true
                };
                return;
            }

            # Compute and cache results
            queue = deque([(person, 0)]);
            visited = {self.person_name};
            results = set();

            while queue {
                current_person, depth = queue.popleft();

                if depth >= self.max_depth {
                    continue;
                }

                friends = [current_person --> Friend --> Person];
                for friend in friends {
                    if friend.name not in visited {
                        visited.add(friend.name);
                        results.add(friend.name);
                        queue.append((friend, depth + 1));
                    }
                }
            }

            # Cache the results
            cache = CacheEntry(
                depth=self.max_depth,
                friend_names=list(results),
                computed_at="2024-01-15"
            );
            person ++> cache;

            report {
                "person": self.person_name,
                "friends_of_friends": list(results),
                "total_found": len(results),
                "cached": false
            };
        }
    }

    node CacheEntry {
        has depth: int;
        has friend_names: list[str];
        has computed_at: str;
    }
    ```

---

## Memory Management

Efficient memory usage is critical for large-scale graph applications. Let's explore techniques to minimize memory footprint while maintaining performance.

### Memory-Efficient Data Structures

!!! example "Optimized Memory Usage"
    ```jac
    # memory_optimized.jac
    # Use lightweight nodes for large-scale networks
    node LightPerson {
        has name: str;
        has age: int;
        # Remove unnecessary cached data to save memory

        def get_friend_count() -> int {
            # Calculate on-demand instead of caching
            return len([self --> (`?Friend) --> (`?LightPerson)]);
        }

        def get_connections_summary() -> dict {
            friends = [self --> (`?Friend) --> (`?LightPerson)];

            return {
                "friend_count": len(friends),
                "avg_age": sum(f.age for f in friends) / len(friends) if friends else 0,
                "friend_names": [f.name for f in friends[:5]]  # Limit for memory
            };
        }
    }

    # Memory-conscious walker with cleanup
    walker find_friends_memory_efficient {
        has person_name: str;
        has max_depth: int = 2;
        has batch_size: int = 100;  # Process in batches

        can memory_conscious_search with `root entry {
            start_person = [-->](`?LightPerson)(?name == self.person_name);

            if not start_person {
                report {"error": "Person not found"};
                return;
            }

            results = [];
            processed = 0;
            queue = deque([(start_person[0], 0)]);
            visited = {self.person_name};

            while queue and processed < self.batch_size {
                current_person, depth = queue.popleft();
                processed += 1;

                if depth >= self.max_depth {
                    continue;
                }

                # Get only essential friend data
                friends = [current_person --> Friend --> LightPerson];

                for friend in friends[:10] {  # Limit friends per iteration
                    if friend.name not in visited {
                        visited.add(friend.name);
                        results.append({
                            "name": friend.name,
                            "age": friend.age,
                            "depth": depth + 1
                        });

                        if depth + 1 < self.max_depth {
                            queue.append((friend, depth + 1));
                        }
                    }
                }

                # Periodic cleanup of references
                if processed % 50 == 0 {
                    # Force cleanup of temporary variables
                    friends = None;
                }
            }

            report {
                "person": self.person_name,
                "friends_found": results,
                "total_processed": processed,
                "memory_optimized": true
            };
        }
    }
    ```

### Performance Monitoring

!!! example "Performance Tracking Walker"
    ```jac
    # performance_monitor.jac
    import from time { time }
    import from psutil { Process }

    walker benchmark_friend_finding {
        has person_name: str;
        has algorithm: str = "optimized";  # "basic", "optimized", "cached"

        can run_benchmark with `root entry {
            start_time = time();
            process = Process();
            start_memory = process.memory_info().rss / 1024 / 1024;  # MB

            # Run the specified algorithm
            if self.algorithm == "basic" {
                result = find_friends_of_friends(person_name=self.person_name) spawn here;
            } elif self.algorithm == "optimized" {
                result = find_friends_optimized(person_name=self.person_name) spawn here;
            } elif self.algorithm == "cached" {
                result = find_friends_cached(person_name=self.person_name) spawn here;
            } else {
                report {"error": "Unknown algorithm"};
                return;
            }

            end_time = time();
            end_memory = process.memory_info().rss / 1024 / 1024;  # MB

            # Performance metrics
            execution_time = end_time - start_time;
            memory_used = end_memory - start_memory;

            report {
                "algorithm": self.algorithm,
                "execution_time_ms": round(execution_time * 1000, 2),
                "memory_used_mb": round(memory_used, 2),
                "friends_found": len(result[0].get("friends_of_friends", [])),
                "performance_ratio": round(len(result[0].get("friends_of_friends", [])) / execution_time, 2)
            };
        }
    }

    # Batch benchmarking for statistical analysis
    walker run_performance_suite {
        has test_count: int = 10;
        has test_persons: list[str] = ["Alice", "Bob", "Charlie"];

        can comprehensive_benchmark with `root entry {
            algorithms = ["basic", "optimized", "cached"];
            results = [];

            for algorithm in algorithms {
                algorithm_results = [];

                for person in self.test_persons {
                    for i in range(self.test_count) {
                        benchmark = benchmark_friend_finding(
                            person_name=person,
                            algorithm=algorithm
                        );
                        result = benchmark spawn here;
                        algorithm_results.append(result[0]);
                    }
                }

                # Calculate averages
                avg_time = sum(r["execution_time_ms"] for r in algorithm_results) / len(algorithm_results);
                avg_memory = sum(r["memory_used_mb"] for r in algorithm_results) / len(algorithm_results);
                avg_friends = sum(r["friends_found"] for r in algorithm_results) / len(algorithm_results);

                results.append({
                    "algorithm": algorithm,
                    "avg_execution_time_ms": round(avg_time, 2),
                    "avg_memory_used_mb": round(avg_memory, 2),
                    "avg_friends_found": round(avg_friends, 2),
                    "total_tests": len(algorithm_results)
                });
            }

            report {
                "benchmark_suite": results,
                "test_configuration": {
                    "test_count": self.test_count,
                    "test_persons": self.test_persons
                }
            };
        }
    }
    ```

---

## Distributed Performance

When deploying to Jac Cloud, consider performance implications of distributed execution and data locality.

### Cloud-Optimized Friend Finding

!!! example "Distributed Performance Patterns"
    ```jac
    # distributed_friends.jac
    walker find_friends_distributed {
        has person_name: str;
        has max_depth: int = 2;
        has partition_size: int = 50;  # Optimize for cloud chunks

        can cloud_optimized_search with `root entry {
            start_person = [-->](`?LightPerson)(?name == self.person_name);

            if not start_person {
                report {"error": "Person not found"};
                return;
            }

            # Partition the search for distributed processing
            person = start_person[0];
            immediate_friends = [person --> Friend --> LightPerson];

            # Process in chunks optimized for cloud deployment
            friend_chunks = [
                immediate_friends[i:i + self.partition_size]
                for i in range(0, len(immediate_friends), self.partition_size)
            ];

            all_results = [];
            chunk_count = 0;

            for chunk in friend_chunks {
                chunk_results = [];
                chunk_count += 1;

                for friend in chunk {
                    # Second-degree friends
                    if self.max_depth > 1 {
                        second_degree = [friend --> Friend --> LightPerson];
                        for second_friend in second_degree {
                            if second_friend.name != self.person_name {
                                chunk_results.append({
                                    "name": second_friend.name,
                                    "age": second_friend.age,
                                    "via": friend.name,
                                    "depth": 2
                                });
                            }
                        }
                    }

                    chunk_results.append({
                        "name": friend.name,
                        "age": friend.age,
                        "via": "direct",
                        "depth": 1
                    });
                }

                all_results.extend(chunk_results);
            }

            # Remove duplicates efficiently
            unique_results = {};
            for result in all_results {
                unique_results[result["name"]] = result;
            }

            report {
                "person": self.person_name,
                "friends_network": list(unique_results.values()),
                "total_found": len(unique_results),
                "chunks_processed": chunk_count,
                "distributed": true
            };
        }
    }

    # Health check for performance monitoring
    walker performance_health_check {
        can check_system_health with `root entry {
            # Count total nodes and relationships
            all_persons = [-->](`?LightPerson);
            total_friendships = [-->](`?Friend);

            # Calculate network density
            max_possible_connections = len(all_persons) * (len(all_persons) - 1) / 2;
            density = len(total_friendships) / max_possible_connections if max_possible_connections > 0 else 0;

            # Sample performance with a quick test
            start_time = time();
            if all_persons {
                sample_person = all_persons[0];
                sample_friends = [sample_person --> Friend --> LightPerson];
            }
            sample_time = time() - start_time;

            report {
                "total_persons": len(all_persons),
                "total_friendships": len(total_friendships),
                "network_density": round(density, 4),
                "sample_query_time_ms": round(sample_time * 1000, 2),
                "health_status": "good" if sample_time < 0.1 else "degraded"
            };
        }
    }
    ```

---

## Performance Testing and Deployment

### Creating Test Data

!!! example "Performance Test Setup"
    ```jac
    # test_data_generator.jac
    import from random { randint, choice }

    walker generate_test_network {
        has person_count: int = 100;
        has avg_friends: int = 5;

        can create_test_network with `root entry {
            # Clear existing test data
            existing_persons = [-->](`?LightPerson);
            existing_friends = [-->](`?Friend);

            for person in existing_persons {
                del person;
            }
            for friendship in existing_friends {
                del friendship;
            }

            # Create people
            people = [];
            for i in range(self.person_count) {
                person = LightPerson(
                    name=f"Person{i}",
                    age=randint(18, 65)
                );
                here ++> person;
                people.append(person);
            }

            # Create friendships
            total_friendships = 0;
            for person in people {
                friends_to_add = randint(1, self.avg_friends * 2);

                for _ in range(friends_to_add) {
                    potential_friend = choice(people);
                    if potential_friend != person {
                        # Check if friendship already exists
                        existing = [person --> Friend --> LightPerson](?name == potential_friend.name);
                        if not existing {
                            friendship = Friend(since="2024-01-15");
                            person ++> friendship ++> potential_friend;
                            total_friendships += 1;
                        }
                    }
                }
            }

            report {
                "people_created": len(people),
                "friendships_created": total_friendships,
                "avg_friends_per_person": round(total_friendships * 2 / len(people), 2)
            };
        }
    }
    ```

### Testing Performance

```bash
# Deploy the optimized version
jac serve distributed_friends.jac

# Generate test data
curl -X POST http://localhost:8000/walker/generate_test_network \
  -H "Content-Type: application/json" \
  -d '{"person_count": 1000, "avg_friends": 10}'

# Run performance benchmarks
curl -X POST http://localhost:8000/walker/run_performance_suite \
  -H "Content-Type: application/json" \
  -d '{"test_count": 5, "test_persons": ["Person1", "Person50", "Person100"]}'

# Check system health
curl -X POST http://localhost:8000/walker/performance_health_check \
  -H "Content-Type: application/json" \
  -d '{}'
```

---

## Best Practices

!!! summary "Performance Optimization Guidelines"
    - **Measure before optimizing**: Always profile before making performance changes
    - **Optimize hot paths**: Focus on frequently executed code sections
    - **Use appropriate data structures**: Choose the right graph patterns for your use case
    - **Cache intelligently**: Cache expensive computations but avoid memory leaks
    - **Batch operations**: Process multiple items together when possible
    - **Monitor continuously**: Track performance metrics in production

## Key Takeaways

!!! summary "What We've Learned"
    **Graph Structure Optimization:**

    - **Efficient relationships**: Well-designed edges and nodes improve traversal speed
    - **Index strategy**: Cache frequently accessed data for faster retrieval
    - **Memory management**: Batch processing and cleanup reduce memory footprint
    - **Connection patterns**: Optimize graph topology for common access patterns

    **Algorithm Optimization:**

    - **Traversal strategies**: Choose BFS vs DFS based on your specific requirements
    - **Caching patterns**: Strategic caching reduces redundant computations
    - **Batch processing**: Handle large datasets efficiently with chunking
    - **Early termination**: Stop processing when results are sufficient

    **Performance Monitoring:**

    - **Built-in metrics**: Track execution time, memory usage, and throughput
    - **Benchmarking**: Compare different implementation strategies objectively
    - **Real-world testing**: Test with production-scale data and load patterns
    - **Continuous profiling**: Monitor performance trends over time

    **Distributed Performance:**

    - **Cloud optimization**: Design for distributed execution and data locality
    - **Scaling patterns**: Horizontal scaling through partitioning and parallelization
    - **Resource efficiency**: Optimize for cloud computing cost and performance
    - **Load balancing**: Distribute work evenly across available resources

!!! tip "Try It Yourself"
    Apply performance optimization by:
    - Profiling your graph applications to identify bottlenecks
    - Implementing different caching strategies and measuring their impact
    - Testing with larger datasets to understand scaling characteristics
    - Optimizing walker traversal patterns for your specific use cases

    Remember: Performance optimization is an iterative process - measure, optimize, and verify!

---

*Ready to learn about migration strategies? Continue to [Chapter 21: Python to Jac Migration](chapter_20.md)!*

# Chapter 21: Python to Jac Migration

In this chapter, we'll explore practical strategies for migrating Python applications to Jac. We'll progressively convert a simple library management system from Python to Jac, demonstrating migration patterns, integration strategies, and common pitfalls to avoid.

!!! info "What You'll Learn"
    - Strategic approaches to Python-to-Jac migration
    - Converting Python classes to Jac objects and nodes
    - Incremental adoption patterns for existing codebases
    - Python integration patterns within Jac applications
    - Common migration pitfalls and how to avoid them

---

## Migration Strategies

Migrating from Python to Jac doesn't require rewriting everything from scratch. Jac's Python compatibility enables gradual migration, allowing you to adopt Object-Spatial Programming incrementally while maintaining existing functionality.

!!! success "Migration Benefits"
    - **Gradual Transition**: Migrate components incrementally
    - **Python Compatibility**: Existing Python libraries work seamlessly
    - **Improved Performance**: Benefit from Jac's optimizations
    - **Modern Patterns**: Adopt Object-Spatial Programming gradually
    - **Risk Mitigation**: Test new features alongside existing code

### Migration Approaches

!!! tip "Recommended Migration Strategies"
    1. **Top-Down**: Start with high-level architecture, then migrate details
    2. **Bottom-Up**: Begin with utility functions and data structures
    3. **Feature-by-Feature**: Migrate complete features one at a time
    4. **Hybrid Integration**: Run Python and Jac code side-by-side

---

## Starting Point: Python Library System

Let's begin with a traditional Python library management system that we'll progressively migrate to Jac.

### Original Python Implementation

!!! example "Python Library System"
    === "Python Original"
        ```python
        # library.py - Traditional Python implementation
        from datetime import datetime
        from typing import List, Optional

        class Book:
            def __init__(self, title: str, author: str, isbn: str):
                self.title = title
                self.author = author
                self.isbn = isbn
                self.is_borrowed = False
                self.borrowed_by = None
                self.borrowed_date = None

            def borrow(self, member_id: str) -> bool:
                if not self.is_borrowed:
                    self.is_borrowed = True
                    self.borrowed_by = member_id
                    self.borrowed_date = datetime.now()
                    return True
                return False

            def return_book(self) -> bool:
                if self.is_borrowed:
                    self.is_borrowed = False
                    self.borrowed_by = None
                    self.borrowed_date = None
                    return True
                return False

        class Member:
            def __init__(self, name: str, member_id: str):
                self.name = name
                self.member_id = member_id
                self.borrowed_books: List[str] = []

            def add_borrowed_book(self, isbn: str):
                if isbn not in self.borrowed_books:
                    self.borrowed_books.append(isbn)

            def remove_borrowed_book(self, isbn: str):
                if isbn in self.borrowed_books:
                    self.borrowed_books.remove(isbn)

        class Library:
            def __init__(self, name: str):
                self.name = name
                self.books: List[Book] = []
                self.members: List[Member] = []

            def add_book(self, book: Book):
                self.books.append(book)

            def add_member(self, member: Member):
                self.members.append(member)

            def find_book(self, isbn: str) -> Optional[Book]:
                for book in self.books:
                    if book.isbn == isbn:
                        return book
                return None

            def borrow_book(self, isbn: str, member_id: str) -> bool:
                book = self.find_book(isbn)
                member = self.find_member(member_id)

                if book and member and book.borrow(member_id):
                    member.add_borrowed_book(isbn)
                    return True
                return False

            def find_member(self, member_id: str) -> Optional[Member]:
                for member in self.members:
                    if member.member_id == member_id:
                        return member
                return None
        ```

    === "Jac Modern Equivalent"
        ```jac
        # library.jac - Modern Jac implementation preview
        import from datetime { datetime }

        node Book {
            has title: str;
            has author: str;
            has isbn: str;
            has is_borrowed: bool = False;
            has borrowed_date: str = "";

            def borrow(member_id: str) -> bool {
                if not self.is_borrowed {
                    self.is_borrowed = True;
                    self.borrowed_date = datetime.now().isoformat();
                    return True;
                }
                return False;
            }

            def return_book() -> bool {
                if self.is_borrowed {
                    self.is_borrowed = False;
                    self.borrowed_date = "";
                    return True;
                }
                return False;
            }
        }

        node Member {
            has name: str;
            has member_id: str;
        }

        edge BorrowedBy {
            has borrowed_date: str;
        }

        node Library {
            has name: str;

            def add_book(book: Book) -> None {
                self ++> book;
            }

            def add_member(member: Member) -> None {
                self ++> member;
            }

            def borrow_book(isbn: str, member_id: str) -> bool {
                book = [self --> Book](?isbn == isbn);
                member = [self --> Member](?member_id == member_id);

                if book and member and book[0].borrow(member_id) {
                    member[0] +:BorrowedBy:borrowed_date=datetime.now().isoformat():+> book[0];
                    return True;
                }
                return False;
            }
        }
        ```

---

## Step 1: Converting Classes to Objects

The first migration step involves converting Python classes to Jac objects while maintaining similar functionality.

### Basic Class to Object Migration

!!! example "Class to Object Conversion"
    === "Python Class"
        ```python
        # book.py - Python class
        class Book:
            def __init__(self, title: str, author: str, isbn: str):
                self.title = title
                self.author = author
                self.isbn = isbn
                self.is_borrowed = False

            def get_info(self) -> str:
                status = "Available" if not self.is_borrowed else "Borrowed"
                return f"{self.title} by {self.author} - {status}"

            def borrow(self) -> bool:
                if not self.is_borrowed:
                    self.is_borrowed = True
                    return True
                return False
        ```

    === "Jac Object"
        ```jac
        # book.jac - Jac object
        obj Book {
            has title: str;
            has author: str;
            has isbn: str;
            has is_borrowed: bool = False;

            def get_info() -> str {
                status = "Available" if not self.is_borrowed else "Borrowed";
                return f"{self.title} by {self.author} - {status}";
            }

            def borrow() -> bool {
                if not self.is_borrowed {
                    self.is_borrowed = True;
                    return True;
                }
                return False;
            }
        }
        ```

!!! tip "Key Migration Changes"
    - `class` → `obj`
    - `__init__` → automatic constructor with `has`
    - `:` → `;` for statement termination
    - `{}` for code blocks instead of indentation

### Testing the Migration

!!! example "Migration Testing"
    === "Python Usage"
        ```python
        # test_book.py
        book = Book("The Great Gatsby", "F. Scott Fitzgerald", "123456789")
        print(book.get_info())  # The Great Gatsby by F. Scott Fitzgerald - Available

        success = book.borrow()
        print(f"Borrowed: {success}")  # Borrowed: True
        print(book.get_info())  # The Great Gatsby by F. Scott Fitzgerald - Borrowed
        ```

    === "Jac Usage"
        ```jac
        # test_book.jac
        with entry {
            book = Book(title="The Great Gatsby", author="F. Scott Fitzgerald", isbn="123456789");
            print(book.get_info());  # The Great Gatsby by F. Scott Fitzgerald - Available

            success = book.borrow();
            print(f"Borrowed: {success}");  # Borrowed: True
            print(book.get_info());  # The Great Gatsby by F. Scott Fitzgerald - Borrowed
        }
        ```

---

## Step 2: Introducing Spatial Relationships

The next step leverages Jac's Object-Spatial Programming by converting relationships into nodes and edges.

### From Collections to Graph Structures

!!! example "Spatial Relationship Migration"
    === "Python Relationships"
        ```python
        # library_python.py - List-based relationships
        class Library:
            def __init__(self, name: str):
                self.name = name
                self.books = []  # List of books
                self.members = []  # List of members
                self.borrowed_books = {}  # Dict mapping book_isbn -> member_id

            def add_book(self, book):
                self.books.append(book)

            def add_member(self, member):
                self.members.append(member)

            def find_available_books(self):
                return [book for book in self.books if not book.is_borrowed]

            def find_member_books(self, member_id: str):
                member_isbns = [isbn for isbn, mid in self.borrowed_books.items() if mid == member_id]
                return [book for book in self.books if book.isbn in member_isbns]
        ```

    === "Jac Spatial Relationships"
        ```jac
        # library_spatial.jac - Graph-based relationships
        node Book {
            has title: str;
            has author: str;
            has isbn: str;
        }

        node Member {
            has name: str;
            has member_id: str;
        }

        edge Contains;  # Library contains books/members
        edge BorrowedBy {
            has borrowed_date: str;
        }

        node Library {
            has name: str;

            def add_book(book: Book) -> None {
                self +:Contains:+> book;
            }

            def add_member(member: Member) -> None {
                self +:Contains:+> member;
            }

            def find_available_books() -> list[Book] {
                all_books = [self --Contains--> Book];
                borrowed_books = [self --Contains--> Book --BorrowedBy--> Member];
                # Return books not in borrowed list
                return [book for book in all_books if book not in borrowed_books];
            }

            def find_member_books(member_id: str) -> list[Book] {
                target_member = [self --Contains--> Member](?member_id == member_id);
                if target_member {
                    return [target_member[0] <--BorrowedBy-- Book];
                }
                return [];
            }
        }
        ```
---

## Incremental Adoption Patterns

Real-world migration often requires running Python and Jac code together. Let's explore hybrid approaches.

### Python-Jac Integration

!!! example "Hybrid Integration Approach"
    === "Python Wrapper"
        ```python
        # hybrid_library.py - Python wrapper for Jac code
        import subprocess
        import json

        class JacLibraryWrapper:
            def __init__(self, library_name: str):
                self.library_name = library_name
                # Initialize Jac library through subprocess or API

            def call_jac_walker(self, walker_name: str, params: dict):
                """Call Jac walker from Python"""
                # In practice, this would use jac-cloud API or subprocess
                cmd = f"jac run library.jac --walker {walker_name} --ctx '{json.dumps(params)}'"
                result = subprocess.run(cmd, shell=True, capture_output=True, text=True)
                return json.loads(result.stdout) if result.stdout else None

            def add_book_via_jac(self, title: str, author: str, isbn: str):
                """Add book using Jac walker"""
                params = {"title": title, "author": author, "isbn": isbn}
                return self.call_jac_walker("add_book", params)

            def get_available_books(self):
                """Get available books using Jac walker"""
                return self.call_jac_walker("get_available_books", {})

        # Traditional Python usage
        class PythonBook:
            def __init__(self, title: str, author: str):
                self.title = title
                self.author = author

        # Hybrid usage
        if __name__ == "__main__":
            # Use existing Python classes
            python_book = PythonBook("Old Book", "Old Author")

            # Use new Jac functionality
            jac_library = JacLibraryWrapper("My Library")
            jac_library.add_book_via_jac("New Book", "New Author", "123456")
        ```

    === "Jac Side"
        ```jac
        # library.jac - Jac implementation
        node Book {
            has title: str;
            has author: str;
            has isbn: str;
        }

        node Library {
            has name: str;
        }

        walker add_book {
            has title: str;
            has author: str;
            has isbn: str;

            can add_book_to_library with `root entry {
                # Find or create library
                libraries = [-->](`?Library);
                if not libraries {
                    library = Library(name="Default Library");
                    here ++> library;
                } else {
                    library = libraries[0];
                }

                # Create and add book
                new_book = Book(title=self.title, author=self.author, isbn=self.isbn);
                library ++> new_book;

                report {"message": f"Added book: {self.title}", "isbn": self.isbn};
            }
        }

        walker get_available_books {
            can fetch_available_books with `root entry {
                all_books = [-->](`?Book);
                books_data = [
                    {"title": book.title, "author": book.author, "isbn": book.isbn}
                    for book in all_books
                ];
                report {"books": books_data, "count": len(books_data)};
            }
        }
        ```

---

## Common Migration Pitfalls

Understanding common pitfalls helps ensure smooth migration from Python to Jac.

### Pitfall 1: Direct Syntax Translation

!!! warning "Avoid Direct Translation"
    Don't directly translate Python syntax without considering Jac's spatial capabilities.

!!! example "Poor vs Good Migration"
    === "Poor Migration (Direct Translation)"
        ```jac
        # poor_migration.jac - Direct syntax translation
        obj LibraryManager {
            has books: list[dict] = [];  # Still thinking in lists
            has members: list[dict] = [];

            def add_book(book_data: dict) -> None {
                self.books.append(book_data);  # Missing spatial benefits
            }

            def find_book(isbn: str) -> dict | None {
                for book in self.books {  # Manual iteration
                    if book["isbn"] == isbn {
                        return book;
                    }
                }
                return None;
            }
        }
        ```

    === "Good Migration (Spatial Thinking)"
        ```jac
        # good_migration.jac - Embracing spatial programming
        node Book {
            has title: str;
            has author: str;
            has isbn: str;
        }

        node Library {
            has name: str;

            def add_book(title: str, author: str, isbn: str) -> Book {
                new_book = Book(title=title, author=author, isbn=isbn);
                self ++> new_book;  # Spatial relationship
                return new_book;
            }

            def find_book(isbn: str) -> Book | None {
                # Spatial filtering - much cleaner
                found_books = [self --> Book](?isbn == isbn);
                return found_books[0] if found_books else None;
            }
        }
        ```
        </div>

### Pitfall 2: Ignoring Type Safety

!!! example "Type Safety Migration"
    === "Weak Typing (Python Style)"
        ```jac
        # weak_typing.jac - Avoiding Jac's type benefits
        walker process_data {
            has data: dict;  # Too generic

            can process with `root entry {
                # Uncertain about data structure
                if "title" in self.data {
                    title = self.data["title"];
                } else {
                    title = "Unknown";
                }
                report {"processed": title};
            }
        }
        ```

    === "Strong Typing (Jac Style)"
        ```jac
        # strong_typing.jac - Leveraging Jac's type system
        obj BookData {
            has title: str;
            has author: str;
            has isbn: str;
        }

        walker process_book_data {
            has book_data: BookData;  # Clear, type-safe structure

            can process with `root entry {
                # Type safety guarantees
                new_book = Book(
                    title=self.book_data.title,
                    author=self.book_data.author,
                    isbn=self.book_data.isbn
                );
                here ++> new_book;
                report {"processed": self.book_data.title};
            }
        }
        ```
        </div>

---

## Migration Checklist

!!! tip "Successful Migration Steps"
    1. **Start Small**: Begin with utility functions and simple classes
    2. **Embrace Types**: Use Jac's type system for better code quality
    3. **Think Spatially**: Convert relationships to nodes and edges
    4. **Test Incrementally**: Validate each migration step
    5. **Leverage Python**: Keep using Python libraries where beneficial
    6. **Document Changes**: Track migration decisions and patterns

### Final Migration Example

!!! example "Complete Library Migration"
    === "Before (Python)"
        ```python
        # Original complex Python code
        library = Library("City Library")

        book1 = Book("1984", "George Orwell", "111")
        book2 = Book("Brave New World", "Aldous Huxley", "222")
        member = Member("Alice", "M001")

        library.add_book(book1)
        library.add_book(book2)
        library.add_member(member)

        # Manual relationship management
        success = library.borrow_book("111", "M001")
        available = library.find_available_books()
        ```

    === "After (Jac)"
        ```jac
        # Modern Jac implementation
        with entry {
            library = Library(name="City Library");

            book1 = Book(title="1984", author="George Orwell", isbn="111");
            book2 = Book(title="Brave New World", author="Aldous Huxley", isbn="222");
            member = Member(name="Alice", member_id="M001");

            library.add_book(book1);
            library.add_book(book2);
            library.add_member(member);

            # Spatial relationship management
            success = library.borrow_book("111", "M001");
            available = library.find_available_books();

            print(f"Borrowed: {success}, Available: {len(available)}");
        }
        ```
        </div>

---

## Best Practices

!!! summary "Migration Best Practices"
    - **Start small**: Begin with isolated components rather than entire applications
    - **Maintain compatibility**: Keep existing Python code running during migration
    - **Test thoroughly**: Validate each migration step with comprehensive tests
    - **Document changes**: Track migration decisions and patterns for team consistency
    - **Train the team**: Ensure all developers understand Object-Spatial Programming concepts
    - **Plan rollback**: Have strategies for reverting changes if issues arise

## Key Takeaways

!!! summary "What We've Learned"
    **Migration Strategies:**

    - **Incremental approach**: Gradual migration reduces risk and allows learning
    - **Syntax translation**: Converting Python classes to Jac objects with automatic constructors
    - **Spatial transformation**: Moving from collections to graph-based relationships
    - **Hybrid integration**: Running Python and Jac code together during transition

    **Technical Benefits:**

    - **Automatic constructors**: Eliminate boilerplate code with `has` declarations
    - **Type safety**: Mandatory typing catches errors earlier in development
    - **Graph relationships**: Natural representation of connected data
    - **Performance gains**: Optimized execution for both local and distributed environments

    **Common Challenges:**

    - **Paradigm shift**: Moving from object-oriented to spatial thinking
    - **Team adoption**: Training developers on new concepts and patterns
    - **Integration complexity**: Managing hybrid Python-Jac applications
    - **Testing changes**: Ensuring equivalent behavior after migration

    **Success Factors:**

    - **Clear planning**: Structured approach to migration with defined milestones
    - **Comprehensive testing**: Validation at every step of the migration process
    - **Team alignment**: Consistent understanding of goals and benefits
    - **Iterative improvement**: Continuous refinement of migration patterns

!!! tip "Try It Yourself"
    Practice migration by:
    - Converting a simple Python class to a Jac object
    - Transforming list-based relationships into graph structures
    - Creating hybrid applications that use both Python libraries and Jac features
    - Building comprehensive test suites to validate migration correctness

    Remember: Successful migration is about embracing spatial thinking, not just syntax conversion!

---

<div align="center">
    <img src="../docs/docs/assets/byLLM_name_logo.png" height="150">

  [About byLLM] | [Get started] | [Usage docs] | [Research Paper]
</div>

[About byLLM]: https://www.jac-lang.org/learn/jac-byllm/with_llm/
[Get started]: https://www.jac-lang.org/learn/jac-byllm/quickstart/
[Usage docs]: https://www.jac-lang.org/learn/jac-byllm/usage/
[Research Paper]: https://arxiv.org/abs/2405.08965

# byLLM : Less Prompting! More Coding!

[![PyPI version](https://img.shields.io/pypi/v/byllm.svg)](https://pypi.org/project/byllm/) [![tests](https://github.com/jaseci-labs/jaseci/actions/workflows/test-jaseci.yml/badge.svg?branch=main)](https://github.com/jaseci-labs/jaseci/actions/workflows/test-jaseci.yml)

byLLM is an innovative AI integration framework built for the Jaseci ecosystem, implementing the cutting-edge Meaning Typed Programming (MTP) paradigm. MTP revolutionizes AI integration by embedding prompt engineering directly into code semantics, making AI interactions more natural and maintainable. While primarily designed to complement the Jac programming language, byLLM also provides a powerful Python library interface.

Installation is simple via PyPI:

```bash
pip install byllm
```

## Basic Example

Consider building an application that translates english to other languages using an LLM. This can be simply built as follows:

```python
import from byllm { Model }

glob llm = Model(model_name="gpt-4o");

def translate_to(language: str, phrase: str) -> str by llm();

with entry {
    output = translate_to(language="Welsh", phrase="Hello world");
    print(output);
}
```

This simple piece of code replaces traditional prompt engineering without introducing additional complexity.

## Power of Types with LLMs

Consider a program that detects the personality type of a historical figure from their name. This can eb built in a way that LLM picks from an enum and the output strictly adhere this type.

```python
import from byllm { Model }
glob llm = Model(model_name="gemini/gemini-2.0-flash");

enum Personality {
    INTROVERT, EXTROVERT, AMBIVERT
}

def get_personality(name: str) -> Personality by llm();

with entry {
    name = "Albert Einstein";
    result = get_personality(name);
    print(f"{result} personality detected for {name}");
}
```

> Similarly, custom types can be used as output types which force the LLM to adhere to the specified type and produce a valid result.

## Control! Control! Control!

Even if we are elimination prompt engineering entierly, we allow specific ways to enrich code semantics through **docstrings** and **semstrings**.

```python
"""Represents the personal record of a person"""
obj Person {
    has name: str;
    has dob: str;
    has ssn: str;
}

sem Person.name = "Full name of the person";
sem Person.dob = "Date of Birth";
sem Person.ssn = "Last four digits of the Social Security Number of a person";

"""Calculate eligibility for various services based on person's data."""
def check_eligibility(person: Person, service_type: str) -> bool by llm();

```

Docstrings naturally enhance the semantics of their associated code constructs, while the `sem` keyword provides an elegant way to enrich the meaning of class attributes and function arguments. Our research shows these concise semantic strings are more effective than traditional multi-line prompts.

## How well does byLLM work?

byLLM is built using the underline priciple of Meaning Typed Programming and we shown our evaluation data compared with two such AI integration frameworks for python, such as DSPy and LMQL. We show significant performance gain against LMQL while allowing on par or better performance to DSPy, while having a lower cost and faster runtime.

<div align="center">
    <img src="../docs/docs/assets/correctness_comparison.png" alt="Correctness Comparison" width="600" style="max-width: 100%;">
    <br>
    <em>Figure: Correctness comparison of byLLM with DSPy and LMQL on benchmark tasks.</em>
</div>

**📚 Full Documentation**: [Jac byLLM Documentation](https://www.jac-lang.org/learn/jac-byllm/with_llm/)

**🎮 Complete Examples**:
- [Fantasy Trading Game](https://www.jac-lang.org/learn/examples/mtp_examples/fantasy_trading_game/) - Interactive RPG with AI-generated characters
- [RPG Level Generator](https://www.jac-lang.org/learn/examples/mtp_examples/rpg_game/) - AI-powered game level creation
- [RAG Chatbot Tutorial](https://www.jac-lang.org/learn/examples/rag_chatbot/Overview/) - Building chatbots with document retrieval

**🔬 Research**: The research journey of MTP is available on [Arxiv](https://arxiv.org/abs/2405.08965) and accepted for OOPSLA 2025.

## Quick Links

- [Getting Started Guide](https://www.jac-lang.org/learn/jac-byllm/quickstart/)
- [Jac Language Documentation](https://www.jac-lang.org/)
- [GitHub Repository](https://github.com/jaseci-labs/jaseci)

## Contributing

We welcome contributions to byLLM! Whether you're fixing bugs, improving documentation, or adding new features, your help is appreciated.

Areas we actively seek contributions:
- 🐛 Bug fixes and improvements
- 📚 Documentation enhancements
- ✨ New examples and tutorials
- 🧪 Test cases and benchmarks

Please see our [Contributing Guide](https://www.jac-lang.org/internals/contrib/) for detailed instructions.

If you find a bug or have a feature request, please [open an issue](https://github.com/jaseci-labs/jaseci/issues/new/choose).

## Community

Join our vibrant community:
- [Discord Server](https://discord.gg/6j3QNdtcN6) - Chat with the team and community

## License

This project is licensed under the MIT License.

### Third-Party Dependencies

byLLM integrates with various LLM providers (OpenAI, Anthropic, Google, etc.) through LiteLLM.

## Cite our research


> Jayanaka L. Dantanarayana, Yiping Kang, Kugesan Sivasothynathan, Christopher Clarke, Baichuan Li, Savini
Kashmira, Krisztian Flautner, Lingjia Tang, and Jason Mars. 2025. MTP: A Meaning-Typed Language Ab-
straction for AI-Integrated Programming. Proc. ACM Program. Lang. 9, OOPSLA2, Article 314 (October 2025),
29 pages. https://doi.org/10.1145/3763092


## Jaseci Contributors

<a href="https://github.com/jaseci-labs/jaseci/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=jaseci-labs/jaseci" />
</a>

# Quick Start Guide

## Installation

To get started with byLLM, install the base package:

```bash
pip install byllm
```

## AI-Integrated Function Example

Let's consider an example program where we attempt to categorize a person by their personality using an LLM. For simplicity we will be using names of historical figures.

### Standard Implementation

Traditional implementation of this program with a manual algorithm:

```jac linenums="1"

enum Personality {
    INTROVERT,
    EXTROVERT,
    AMBIVERT
}

def get_personality(name: str) -> Personality {
    # Traditional approach: manual algorithm, prompt-engineered LLM call, etc.
}

with entry {
    name = "Albert Einstein";
    result = get_personality(name);
    print(f"{result} personality detected for {name}");
}
```

**Limitations**: Defining an algorithm in code for this problem is difficult, while integrating an LLM to perform the task would require manual prompt engineering, response parsing, and type conversion to be implemented by the developer.

### byLLM Implementation

The `by` keyword abstraction enables functions to process inputs of any type and generate contextually appropriate outputs of the specified type:

#### Step 1: Configure LLM Model

```jac linenums="1"
import from byllm {Model}

glob llm = Model(model_name="gemini/gemini-2.0-flash");
```

#### Step 2: Implement LLM-Integrated Function

Add `by llm` to enable LLM integration:

```jac linenums="1"
def get_personality(name: str) -> Personality by llm();
```

This will auto-generate a prompt for performing the task and provide an output that strictly adheres to the type `Personality`.

#### Step 3: Execute the Application

Set your API key and run:

```bash
export GEMINI_API_KEY="your-api-key-here"
jac run personality.jac
```

For complete usage methodologies of the `by` abstraction, refer to the [Usage Guide](./usage.md) for documentation on object methods, object instantiation, and multi-agent workflows.


### byLLM Usage in Python

As byLLM is a python package, it can be natively used in jac. The following code show the above example application built in native python with byLLM.

```python linenums="1"
import jaclang
from byllm import Model, by
from enum import Enum

llm = Model(model_name="gemini/gemini-2.0-flash")

class Personality(Enum):
    INTROVERT
    EXTROVERT
    AMBIVERT

@by(model=llm)
def get_personality(name: str) -> Personality: ...

name = "Albert Einstein"
result = get_personality(name)
print(f"{result} personality detected for {name}")
```

To learn more about usage of `by` in python, please refer to [byLLM python Interface](./python_integration.md).

# AI-Integrated Programming with byLLM

This guide covers different ways to use byLLM for AI-integrated software development in Jaclang. byLLM provides language-level abstractions for integrating Large Language Models into applications, from basic AI-powered functions to complex multi-agent systems. For agentic behavior capabilities, byLLM includes the ReAct method with tool integration.

## Supported Models

byLLM uses [LiteLLM](https://docs.litellm.ai/docs) to provide integration with a wide range of models.

=== "OpenAI"
    ```jac linenums="1"
    import from byllm {Model}

    glob llm = Model(model_name = "gpt-4o")
    ```
=== "Gemini"
    ```jac linenums="1"
    import from byllm {Model}

    glob llm = Model(model_name = "gemini/gemini-2.0-flash")
    ```
=== "Anthropic"
    ```jac linenums="1"
    import from byllm {Model}

    glob llm = Model(model_name = "claude-3-5-sonnet-20240620")
    ```
=== "Ollama"
    ```jac linenums="1"
    import from byllm {Model}

    glob llm = Model(model_name = "ollama/llama3:70b")
    ```
=== "HuggingFace Models"
    ```jac linenums="1"
    import from byllm {Model}

    glob llm = Model(model_name = "huggingface/meta-llama/Llama-3.3-70B-Instruct")
    ```

??? Note
    Additional supported models and model serving platforms are available with LiteLLM. Refer to their [documentation](https://docs.litellm.ai/docs/providers) for model names.

## byLLM for Functions

### Basic Functions

Functions can be integrated with LLM capabilities by adding the `by llm` declaration. This eliminates the need for manual API calls and prompt engineering:

```jac linenums="1"
import from byllm.llm { Model }

glob llm = Model(model_name="gpt-4o");

def translate(text: str, target_language: str) -> str by llm();

def analyze_sentiment(text: str) -> str by llm();

def summarize(content: str, max_words: int) -> str by llm();
```

These functions process natural language inputs and generate contextually appropriate outputs.

### Functions with Reasoning

The `method='Reason'` parameter enables step-by-step reasoning for complex tasks:

```jac linenums="1"
import from byllm.llm { Model }

glob llm = Model(model_name="gpt-4o");

def analyze_sentiment(text: str) -> str by llm(method='Reason');

def generate_response(original_text: str, sentiment: str) -> str by llm();

with entry {
    customer_feedback = "I'm really disappointed with the product quality. The delivery was late and the item doesn't match the description at all.";

    # Function performs step-by-step sentiment analysis
    sentiment = analyze_sentiment(customer_feedback);

    # Function generates response based on sentiment
    response = generate_response(customer_feedback, sentiment);

    print(f"Customer sentiment: {sentiment}");
    print(f"Suggested response: {response}");
}
```

### Structured Output Functions

byLLM supports generation of structured outputs. Functions can return complex types:

```jac linenums="1"
obj Person {
    has name: str;
    has age: int;
    has description: str | None;
}

def generate_random_person() -> Person by llm();

with entry {
    person = generate_random_person();
    assert isinstance(person, Person);
    print(f"Generated Person: {person.name}, Age: {person.age}, Description: {person.description}");
}
```

A more complex example using object schema for context and structured output generation is demonstrated in the [game level generation](../examples/mtp_examples/rpg_game.md) example.


## Context-Aware byLLM Methods

Methods can be integrated with LLM capabilities to process object state and context:

<!-- ### Basic Methods -->

When integrating LLMs for methods of a class, byLLM automatically adds attributes of the initialized object into the prompt of the LLM, adding extra context to the LLM.

```jac linenums="1"
import from byllm.llm { Model }

glob llm = Model(model_name="gpt-4o");

obj Person {
    has name: str;
    has age: int;

    def introduce() -> str by llm();
    def suggest_hobby() -> str by llm();
}

with entry {
    alice = Person("Alice", 25);
    print(alice.introduce());
    print(alice.suggest_hobby());
}
```

<!-- ### Complex AI-Integrated Workflows with Objects -->

<!-- Multi-step workflows can be implemented using object methods: -->

<!-- ```jac linenums="1"
import from byllm.llm { Model }

glob llm = Model(model_name="gpt-4o");

obj Essay {
    has essay: str;

    def get_essay_judgement(criteria: str) -> str by llm();

    def get_reviewer_summary(judgements: dict) -> str by llm();

    "Grade should be 'A' to 'D'"
    def give_grade(summary: str) -> str by llm();
}

with entry {
    essay = "With a population of approximately 45 million Spaniards and 3.5 million immigrants,"
        "Spain is a country of contrasts where the richness of its culture blends it up with"
        "the variety of languages and dialects used. Being one of the largest economies worldwide,"
        "and the second largest country in Europe, Spain is a very appealing destination for tourists"
        "as well as for immigrants from around the globe. Almost all Spaniards are used to speaking at"
        "least two different languages, but protecting and preserving that right has not been"
        "easy for them.Spaniards have had to struggle with war, ignorance, criticism and the governments,"
        "in order to preserve and defend what identifies them, and deal with the consequences.";
    essay = Essay(essay);
    criterias = ["Clarity", "Originality", "Evidence"];
    judgements = {};
    for criteria in criterias {
        judgement = essay.get_essay_judgement(criteria);
        judgements[criteria] = judgement;
    }
    summary = essay.get_reviewer_summary(judgements);
    grade = essay.give_grade(summary);
    print("Reviewer Notes: ", summary);
    print("Grade: ", grade);
}
``` -->

## Adding Explicit Context for Functions, Methods and Objects

Providing appropriate context is essential for optimal LLM performance. byLLM provides multiple methods to add context to functions and objects.

### Adding Context with Docstrings

Docstrings provide context for LLM-integrated functions. byLLM uses docstrings to understand function purpose and expected behavior.

```jac linenums="1"
import from byllm.llm { Model }

glob llm = Model(model_name="gpt-4o");

"""Translate text to the target language."""
def translate(text: str, target_language: str) -> str by llm();

"""Generate a professional email response based on the input message tone."""
def generate_email_response(message: str, recipient_type: str) -> str by llm();
```

### Adding Context with Semantic Strings (Semstrings)

Jaclang provides semantic strings using the `sem` keyword for describing object attributes and function parameters. This is useful for:

- Describing object attributes with domain-specific meaning
- Adding context to parameters
- Providing semantic information while maintaining clean code

```jac linenums="1"
obj Person {
    has name;
    has dob;
    has ssn;
}

sem Person = "Represents the personal record of a person";
sem Person.name = "Full name of the person";
sem Person.dob = "Date of Birth";
sem Person.ssn = "Last four digits of the Social Security Number of a person";

"""Calculate eligibility for various services based on person's data."""
def check_eligibility(person: Person, service_type: str) -> bool by llm();
```

### Additional Context with `incl_info`

The `incl_info` parameter provides additional context to LLM methods for context-aware processing:

```jac linenums="1"
import from byllm.llm { Model }
import from datetime { datetime }

glob llm = Model(model_name="gpt-4o");

obj Person {
    has name: str;
    has date_of_birth: str;

    # Uses the date_of_birth attribute and "today" information
    # from incl_info to calculate the person's age
    def calculate_age() -> str by llm(
        incl_info={
            "today": datetime.now().strftime("%d-%m-%Y"),
        }
    );
}
```


### When to Use Each Approach

- **Docstrings**: Use for function-level context and behavior description
- **Semstrings**: Use for attribute-level descriptions and domain-specific terminology
- **incl_info**: Use to selectively include relevant object state in method calls

The `sem` keyword can be used in [separate implementation files](../../jac_book/chapter_5.md#declaring-interfaces-vs-implementations) for improved code organization and maintainability.

In this example:

<!-- - `greet("Alice")` executes the normal function and returns `"Hello Alice"`
- `greet("Alice") by llm()` overrides the function with LLM behavior
- `format_data(user_data) by llm()` transforms data formatting into human-readable presentation -->


## Tool-Calling Agents with ReAct

The ReAct (Reasoning and Acting) method enables agentic behavior by allowing functions to reason about problems and use external tools. Functions can be made agentic by adding the `by llm(tools=[...])` declaration.

```jac linenums="1"
import from byllm { Model }
import from datetime { datetime }

glob llm = Model(model_name="gpt-4o");

obj Person {
    has name: str;
    has dob: str;
}

"""Calculate the age of the person where current date can be retrieved by the get_date tool."""
def calculate_age(person: Person) -> int by llm(tools=[get_date]);

"""Get the current date in DD-MM-YYYY format."""
def get_date() -> str {
    return datetime.now().strftime("%d-%m-%Y");
}

with entry {
    mars = Person("Mars", "27-05-1983");
    print("Age of Mars =", calculate_age(mars));
}
```

A comprehensive tutorial on [building an agentic application is available here.](../examples/mtp_examples/fantasy_trading_game.md)


## Streaming Outputs

The streaming feature enables real-time token reception from LLM functions, useful for generating content where results should be displayed as they are produced.

Set `stream=True` in the invoke parameters to enable streaming:

```jac linenums="1"
import from byllm { Model }

glob llm = Model(model_name="gpt-4o-mini");

""" Generate short essay (less than 300 words) about the given topic """
def generate_essay(topic: str) -> str by llm(stream=True);


with entry {
    topic = "The orca whale and it's hunting techniques";
    for tok in generate_essay(topic) {
        print(tok, end='', flush=True);
    }
    print(end='\n');
}
```

??? example "NOTE:"
    "The `stream=True` parameter only supports `str` output type. Tool calling is not currently supported in streaming mode but will be available in future releases."



























