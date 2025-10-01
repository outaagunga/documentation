# The Jac Programming Language and Jaseci Stack
```jac
https://www.jac-lang.org/learn/introduction/
```
Tour of Jac
Python Superset Philosophy: All of Python Plus More#
Jac is a drop-in replacement for Python and supersets Python, much like Typescript supersets Javascript or C++ supersets C. It extends Python's semantics while maintaining full interoperability with the Python ecosystem, introducing cutting-edge abstractions designed to minimize complexity and embrace AI-forward development.

1234567891011121314151617
Run
This snippet natively imports Python packages math and random and runs identically to its Python counterpart. Jac targets Python bytecode, so all Python libraries work with Jac.

Programming Abstractions for AI#
Jac provides novel constructs for integrating LLMs into code. A function body can simply be replaced with a call to an LLM, removing the need for prompt engineering or extensive use of new libraries.


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
How To Run
Output
by llm() delegates execution to an LLM without any extra library code.

Beyond OOP: An Agentic Programming Model#
In addtion to traditional python classes (class or Jac's dataclass-like obj), Jac programmers can also use node classes (node), edge classes (edge), and walker classes (walker) for a new type of problem solving and agentic programming.

Instances of node and edge classes allow for assembling objects in a graph structure to express semantic relationships between objects. This goes beyond only modeling objects in memory as a disconnected soup of instances.

Walker classes inverts the traditional relationship between data and computation. Rather than moving data to computation with parameter passing, walkers enable moving computation to data as they represent computational units that moves through the topology of node and edge objects.

These new constructs gives rise to a new paradigm for problem solving and implementation we call Object-Spatial Programming (OSP).

In this example, nodes represent meaningful entities (like Weights, Cardio Machines), while walkers (agents) traverse these nodes, collect contextual information, and collaborate with an LLM to generate a personalized workout plan.


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
How To Run
Output
This MTP example demonstrates how Jac seamlessly integrates LLMs with structured node-walker logic, enabling intelligent, context-aware agents with just a few lines of code.

Zero to Infinite Scale without any Code Changes#
Jac's cloud-native abstractions make persistence and user concepts part of the language so that simple programs can run unchanged locally or in the cloud. Much like every object instance has a self referencial this or self reference. Every instance of a Jac program invocation has a root node reference that is unique to every user and for which any ohter node or edge objeccts connected to root will persist across code invocations. Thats it. Using root to access presistant user state and data, Jac deployments can be scaled from local enviornments infinitely into to the cloud with no code changes..


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
How To Run
Output
Fast API Server

Better Organized and Well Typed Codebases#
Jac focuses on type safety and readability. Type hints are required and the built-in typing system eliminates boilerplate imports. Code structure can be split across multiple files, allowing definitions and implementations to be organized separately while still being checked by Jac's native type system.


tweet.jac
tweet.impl.jac

obj Tweet {
    has content: str, author: str, timestamp: str, likes: int = 0;

    def like() -> None;
    def unlike() -> None;
    def get_preview(max_length: int) -> str;
    def get_like_count() -> int;
}

Next Steps#
