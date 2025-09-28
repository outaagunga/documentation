
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



