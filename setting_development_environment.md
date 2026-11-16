
## Suggested structure

**Part 1: Set up your computer (Windows)**
1. Install VS Code
2. Install WSL and Ubuntu, with first-launch username and password
3. Open Ubuntu (Start menu and VS Code terminal)
4. Recommended VS Code extensions (core five first, nice-to-haves later)
5. Move between Windows and Ubuntu files (`/mnt/c/...`, `\\wsl$\...`)

**Part 2: Linux command basics**
1. Navigation: `pwd`, `ls`, `cd`, `cd ..`, `code .`
2. Files: `mkdir`, `touch`, `mv`, `cp`, `rm` and `rm -r` (with a warning)
3. Zip and unzip
4. `sudo` and `apt`: update, upgrade, install
5. Mini-practice: create a folder, make a file, rename it, delete it

**Part 3: Python environment**
1. Python in Ubuntu (`python3 --version`, `python3-pip`, `python3-venv`)
2. Virtual environments: **venv as the main method**
3. Installing packages and saving them (`pip freeze > requirements.txt`, `pip install -r requirements.txt`)
4. Environment variables and `.env`
5. Optional: pipenv, pipreqs, Java for tools like language-tool-python

**Part 4: Git and GitHub** (moved earlier, since everything after it should be committed)
1. Install and configure Git (placeholders for name and email)
2. Create a repo, clone it, check the remote
3. The daily loop: `pull` → `add` → `commit` → `push`
4. `.gitignore` (`.env`, `venv`, `node_modules`)
5. SSH keys: set up and remove
6. Branching and basic collaboration (a gap you noted)
7. Generate a project structure file with `tree`

**Part 5: Backend (FastAPI)**
1. Project structure
2. Install FastAPI and uvicorn
3. Write a minimal `/` and `/analyze` endpoint (add this so readers have something to run)
4. Run the server and read the `/docs` page
5. Test with Thunder Client
6. Add CORS for the frontend
7. Troubleshooting: port busy, module not found, wrong directory

**Part 6: Frontend (React + Vite)**
1. Install Node via nvm (**one** main method) and check Vite's current Node requirement
2. Create the Vite project and install dependencies
3. Add Bootstrap and Axios
4. Environment variables (`VITE_` prefix, never secrets)
5. Call the API with `fetch` or Axios
6. Run both servers side by side (two terminals)
7. Troubleshooting: CORS errors, wrong port, `.env` not loading

**Part 7: How it all fits together**
- Request/response flow diagram: React → HTTP/JSON → FastAPI → database or AI model → JSON → React
- HTTP methods and status codes (200, 201, 400, 401, 403, 404, 422, 500)

**Part 8: Next-level skills** (short intro and links each, not full tutorials yet)
- Databases (SQL vs NoSQL, CRUD, migrations)
- Security (password hashing, JWT, input validation, XSS, rate limiting)
- Testing and debugging (pytest, TestClient, Vitest, dev tools)
- Deployment (Docker, Render, Vercel, Railway, GitHub Actions, Nginx)
- AI development (API keys, prompts, structured outputs, RAG, agents, MCP)

---
---
---
---
# Chakruok

Dev Setup Follow-Along Guide: Windows, Ubuntu, Python, Git, FastAPI and React

A step-by-step to-do list for beginners and returning coders. Tick each box as you go.
Part 0: How to Use This Guide
Who this is for
Anyone who wants to set up a full-stack development environment on a Windows PC, including people who are brand new to coding or coming back after a long break. Follow the parts in order and do not skip the checkpoints.
What you will build
By the end you will have a small FastAPI (Python) backend and a React (Vite) frontend running side by side on your computer, saved to GitHub. We will call the example project writewise: the React page sends some text to an /analyze endpoint, and the backend sends a result back.
How every step is written
Each step follows the same pattern:
1.  Goal: what you are doing and why
2.  Where to run it: Windows PowerShell, the Ubuntu terminal, or VS Code
3.  Commands: in a code block you can copy
4.  Expected result: what you should see
5.  If something goes wrong: the most common error and its fix
6.  Checkpoint: a quick check before moving on
Reading the formatting
• Text in code style is something you type or a file/folder name.
• Anything in <angle_brackets> is a placeholder. Replace it with your own value, and do not type the brackets.
• Lines starting with # inside a code block are comments. You do not type them.
• Use your_username wherever you see it as a stand-in for your real name or username.
Quick glossary
Word
Meaning
Terminal
A window where you type commands instead of clicking
Shell
The program inside the terminal that understands your commands (Ubuntu uses Bash)
Directory
Another word for folder
Package
Code written by someone else that you install and reuse
Dependency
A package your project needs in order to run
Server
A program that waits for requests and sends back responses
Virtual environment
A private folder of packages for one project
Warnings to remember
• rm -r deletes folders permanently. There is no recycle bin.
• sudo runs a command with full power. Only use it for commands you trust.
• Never commit a .env file to GitHub. It holds your secrets.
Part 1: Set Up Your Computer (Windows)
Step 1.1: Install VS Code
Goal: Get the code editor you will use for everything. Where: Windows
[ ] Download VS Code from code.visualstudio.com and run the installer.
[ ] Open VS Code once to confirm it starts.
Checkpoint: VS Code opens to a Welcome tab.
Step 1.2: Install WSL and Ubuntu
Goal: WSL (Windows Subsystem for Linux) lets you run a real Ubuntu Linux system inside Windows. Most development tutorials assume Linux commands. Where: Windows PowerShell (as Administrator)
[ ] Click Start, type PowerShell, right-click it and choose Run as administrator.
[ ] Run:
wsl --install
[ ] Restart your computer when prompted.
[ ] After restarting, Ubuntu opens and asks you to create a username and password. Pick something you will remember.
Expected result: You see a prompt like your_username@PC-NAME:~$.
If something goes wrong:
• Nothing happens after the restart: search for Ubuntu in the Start menu and open it.
• The password does not show as you type: this is normal. Linux hides password characters. Type it and press Enter.
• wsl is not recognized: update Windows, then try again.
Checkpoint: In the Ubuntu window, run whoami. It should print your username.
Step 1.3: Open the Ubuntu terminal
Goal: Know the two ways to reach Ubuntu. Where: Windows / VS Code
[ ] Way 1: Search for Ubuntu in the Start menu and open it.
[ ] Way 2 (recommended): In VS Code, install the WSL extension (see Step 1.4). Press F1, type WSL: Connect to WSL, and press Enter. Then open Terminal > New Terminal. The bottom-left corner should say WSL: Ubuntu.
Checkpoint: The VS Code terminal prompt looks like your_username@PC-NAME:~$.
Step 1.4: Install VS Code extensions
Goal: Add tools that make coding easier. Where: VS Code, Extensions tab (Ctrl + Shift + X)
Core five (install now):
[ ] WSL (connects VS Code to Ubuntu)
[ ] Python (code completion and running Python)
[ ] Prettier (automatic code formatting)
[ ] ESLint (catches JavaScript and React mistakes)
[ ] Thunder Client (tests your API; appears in the left sidebar after install)
Nice-to-haves (install later, see the Appendix): Live Server, Tailwind CSS IntelliSense, Auto Rename Tag, Auto Close Tag, Path IntelliSense, GitLens.
Checkpoint: All five extensions show an installed status.
Step 1.5: Move between Windows and Ubuntu files
Goal: Know where your files live.
You want to...
Use this
Reach Windows files from Ubuntu
/mnt/c/Users/<your_windows_username>/
Reach Ubuntu files from Windows
\\wsl$\Ubuntu\home\your_username\ in File Explorer, or Linux > Ubuntu > home > your_username in the left sidebar
Open the current Ubuntu folder in File Explorer
explorer.exe .
Tip: Create your projects inside the Ubuntu home folder (~), not under /mnt/c. It is much faster and avoids permission problems.
Checkpoint: In Ubuntu, run explorer.exe . and confirm a File Explorer window opens showing your Ubuntu home folder.
Part 2: Linux Command Basics
Where to run everything in this part: the Ubuntu terminal.
Step 2.1: Navigation
Command
What it does
pwd
Shows the folder you are in right now
ls
Lists files and folders here
cd <folder>
Moves into a folder
cd ..
Moves one folder up
cd ~
Jumps back to your home folder
code .
Opens the current folder in VS Code
pwd
ls
cd Documents
cd ..
Tip: Press Tab to auto-complete names and the Up arrow to repeat a previous command.
Step 2.2: Create, copy, rename and delete
# Make a folder
mkdir my_project
cd my_project

# Make an empty file
touch main.py

# Copy a file
cp main.py main_backup.py

# Rename (or move) a file or folder
mv main_backup.py notes.py

# Show the full path of a file
realpath main.py

# Delete a file
rm notes.py

# Delete a folder and everything in it (CAREFUL)
rm -r <folder_name>
Don't do this: never run rm -r on a folder name you have not double-checked, and never type rm -rf /. Run pwd and ls first to be sure where you are.
Step 2.3: Zip and unzip
# One-time install
sudo apt install zip unzip -y

# Zip a folder
zip -r folder_name.zip folder_name/

# Look inside a zip without extracting
unzip -l folder_name.zip

# Extract a zip
unzip folder_name.zip
Step 2.4: sudo and apt
sudo means "run as administrator". apt is Ubuntu's app installer.
sudo apt update          # refresh the list of available software
sudo apt upgrade -y      # install available updates
sudo apt install <package_name> -y
Run the update and upgrade commands now. It may take a few minutes.
Step 2.5: Mini-practice
[ ] Go to your home folder with cd ~.
[ ] Create a folder called practice and move into it.
[ ] Create a file called hello.txt.
[ ] Rename it to hi.txt.
[ ] Run ls to confirm.
[ ] Go up one level and delete the folder with rm -r practice.
Checkpoint: ls no longer shows practice.
Part 3: Python Environment
Step 3.1: Python in Ubuntu
Goal: Use Ubuntu's own Python.
Why two Pythons? Python installed on Windows and Python installed in Ubuntu are completely separate. Code you run in the Ubuntu terminal uses Ubuntu's Python. You do not need the Windows version for this guide.
python3 --version
sudo apt install python3-pip python3-venv -y
Expected result: A version like Python 3.12.x.
Checkpoint: python3 --version and pip3 --version both print a version.
Step 3.2: Create a virtual environment (venv)
Goal: Keep each project's packages separate so projects do not break each other.
cd ~
mkdir writewise
cd writewise
mkdir backend
cd backend
python3 -m venv venv
source venv/bin/activate
Expected result: Your prompt now starts with (venv).
[ ] To leave the environment later, run deactivate.
[ ] Each time you open a new terminal, run source venv/bin/activate again from the backend folder.
If something goes wrong:
• ensurepip is not available: run sudo apt install python3-venv -y and try again.
Checkpoint: The prompt shows (venv).
Step 3.3: Install and save packages
Goal: Install what the project needs and record it so anyone can repeat it.
pip install fastapi uvicorn python-dotenv

# Save the exact list of installed packages
pip freeze > requirements.txt

# Later, on a new machine, install everything from the list
pip install -r requirements.txt
Common error: externally-managed-environment. Recent Ubuntu versions block installing packages globally. This is why we always use a virtual environment. If you see this error, check that your prompt starts with (venv), then run the command again.
Version problems: If a package version does not work with your project, install the version that works, then run pip freeze > requirements.txt again to save it.
Checkpoint: pip list shows fastapi and uvicorn.
Step 3.4: Environment variables and .env
Goal: Keep secrets (API keys, passwords) out of your code.
[ ] In the backend folder, create a file named .env:
DATABASE_URL=your_database_url
SECRET_KEY=your_secret_key
API_KEY=your_api_key
[ ] Read the values in Python:
import os
from dotenv import load_dotenv

load_dotenv()
SECRET_KEY = os.getenv("SECRET_KEY")
[ ] Also create .env.example with the same names but fake values. This one is safe to share.
Don't do this: never upload .env to GitHub. Part 4 shows how to block it.
Keep backend secrets separate from frontend variables. Anything in the frontend can be seen by users.
Step 3.5 (optional): Extra tools
• pipenv: an alternative to venv. Install with pip install --user pipenv, then pipenv shell. If the command is not found, add ~/.local/bin to your PATH with echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc and source ~/.bashrc.
• pipreqs: builds requirements.txt from only the packages your code imports. Run pip install pipreqs then pipreqs . --force.
• Java: some tools (for example language-tool-python) need Java 8 or higher. Install with sudo apt install default-jre -y and check with java -version. For a specific version, run sudo apt remove default-jre, then sudo apt install openjdk-21-jre.
Part 4: Git and GitHub
Step 4.1: Install and configure Git
Where: Ubuntu terminal
sudo apt install git -y
git --version

git config --global user.name "your_name"
git config --global user.email "your_email@example.com"
git config --list
Use the same email as your GitHub account.
Checkpoint: git config --list shows your name and email.
Step 4.2: Create a repository and clone it
[ ] On GitHub, click New repository, name it writewise, and create it.
[ ] Click the green Code button, choose HTTPS, and copy the URL.
[ ] In Ubuntu, go to the folder where projects should live and clone:
cd ~
git clone https://github.com/your-username/writewise.git
cd writewise

# Confirm the link to GitHub
git remote -v
Expected result: Two lines starting with origin pointing to your repository.
Note: git clone already sets up the repository, so you do not need git init. If you already created a writewise folder in Part 3, clone into a different location or move your backend folder into the cloned writewise folder.
Checkpoint: git status runs without an error.
Step 4.3: The daily loop
git pull origin main     # 1. get the latest changes FIRST
git add .                # 2. stage your changes
git commit -m "Describe what you changed"   # 3. save a snapshot
git push origin main     # 4. upload to GitHub
Use master instead of main if your repository uses that name (check with git branch).
Step 4.4: .gitignore
Goal: Tell Git which files to never upload.
[ ] In the root of your project, create a file named .gitignore containing:
.env
venv/
__pycache__/
node_modules/
dist/
Checkpoint: After running git add ., git status does not list .env, venv or node_modules.
Step 4.5: SSH keys (optional but convenient)
Goal: Stop typing your GitHub credentials.
ssh-keygen -t ed25519 -C "your_email@example.com"
# Press Enter to accept the defaults

cat ~/.ssh/id_ed25519.pub
[ ] Copy the full output of the last command.
[ ] On GitHub go to Settings > SSH and GPG keys > New SSH key, give it a title (for example My Laptop), paste the key and save.
[ ] Test it:
ssh -T git@github.com
[ ] To clone with SSH, choose SSH on the repository's Code button and use the URL that starts with git@github.com:.
To remove an old key: GitHub > Settings > SSH and GPG keys, find the key, click Delete and confirm.
Step 4.6: Branching and basic collaboration
Goal: Work on new features without breaking the main code.
git branch                     # list branches
git checkout -b feature/login  # create and switch to a new branch
# ...make changes, then:
git add .
git commit -m "Add login form"
git push -u origin feature/login
[ ] On GitHub, click Compare & pull request and ask a teammate to review it.
[ ] After the pull request is merged, switch back and update:
git checkout main
git pull origin main
If you get a merge conflict: open the marked file in VS Code, choose which version to keep, save, then run git add . and git commit.
Step 4.7: Save your project structure to a file
sudo apt install tree -y
tree -I 'node_modules|venv|__pycache__|.git' > PROJECT_STRUCTURE.txt
If you cannot install tree, use ls -R > PROJECT_STRUCTURE.txt instead. When reading the file, look for entry points (main.py, index.js), configuration (package.json, requirements.txt), source code (src/) and assets (public/, static/).
Part 5: Backend (FastAPI)
Where to run everything in this part: the Ubuntu terminal (or the VS Code terminal connected to WSL), inside writewise/backend with (venv) active.
Step 5.1: Project structure
writewise/
├── backend/
│   ├── venv/            (ignored by Git)
│   ├── main.py
│   ├── requirements.txt
│   ├── .env             (ignored by Git)
│   └── .env.example
├── frontend/            (created in Part 6)
├── .gitignore
└── PROJECT_STRUCTURE.txt
[ ] Run touch main.py in the backend folder if you have not already.
[ ] Open the folder in VS Code with code ..
Step 5.2: Install FastAPI and uvicorn
Skip this if you already did it in Step 3.3.
pip install fastapi uvicorn
pip freeze > requirements.txt
Checkpoint: pip list shows fastapi and uvicorn.
Step 5.3: Write a minimal API
Goal: Have something real to run and test.
[ ] Open main.py and paste:
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class TextIn(BaseModel):
    text: str


@app.get("/")
def home():
    return {"message": "API is running"}


@app.post("/analyze")
def analyze(data: TextIn):
    words = data.text.split()
    return {"word_count": len(words), "characters": len(data.text)}
What this does: app = FastAPI() creates your API. @app.get("/") answers browser visits to the home address. @app.post("/analyze") accepts text sent as JSON and returns counts.
Step 5.4: Run the server and open the docs page
uvicorn main:app --reload
How to read it: main is the file name (main.py), app is the FastAPI object inside it, and --reload restarts the server whenever you save changes.
Expected result: The terminal says Uvicorn running on http://127.0.0.1:8000.
[ ] Open http://127.0.0.1:8000 in your browser. You should see the "API is running" message.
[ ] Open http://127.0.0.1:8000/docs. This interactive page confirms the API is ready, and you can try endpoints there.
[ ] To stop the server, press Ctrl + C in the terminal.
Checkpoint: Both pages load without errors.
Step 5.5: Test with Thunder Client
Goal: Check that the endpoint works before you build the frontend. Where: VS Code
[ ] Click the Thunder Client icon in the left sidebar and choose New Request.
[ ] Set the method to POST.
[ ] Set the URL to http://127.0.0.1:8000/analyze.
[ ] Open the Body tab, select JSON and paste:
{
  "text": "This is a sample text that may contain grammar issues."
}
[ ] Click Send.
Expected result: Status 200 OK and a response like {"word_count": 9, "characters": 54}.
If something goes wrong:
• Status 422: the JSON body is wrong. Check the field is named text and the quotes are straight quotes.
• Connection refused: the server is not running. Start it again.
Step 5.6: Add CORS so the frontend can connect
Goal: Browsers block a page on one address (the frontend) from calling another address (the backend) unless the backend allows it. CORS is that permission.
[ ] Add this to main.py, right after the line app = FastAPI():
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:5173"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
Checkpoint: The server reloads without errors.
Step 5.7: Troubleshooting the backend
Problem
Fix
Address already in use (port busy)
Run on another port: uvicorn main:app --reload --port 8001, or stop the old server with Ctrl + C
ModuleNotFoundError
Activate the venv (source venv/bin/activate) and run pip install -r requirements.txt
Error loading ASGI app
You are in the wrong folder or the file name is wrong. Run pwd and ls, and make sure main.py is there
uvicorn: command not found
The venv is not active. Activate it and try again
[ ] Commit your work: git add ., git commit -m "Add FastAPI backend", git push origin main.
Part 6: Frontend (React + Vite)
Where to run everything in this part: the Ubuntu terminal, from the writewise project root.
Step 6.1: Install Node.js with nvm
Goal: Install Node.js, which runs JavaScript tools, and npm, its package manager. We use nvm because it makes switching versions easy and avoids the outdated version that apt installs.
# Check if Node is already installed
node -v
npm -v

# If missing or older than v20, install nvm (check github.com/nvm-sh/nvm for the latest version number)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash

# Reload your terminal settings
source ~/.bashrc

# Install the newest Long-Term Support version of Node
nvm install --lts

# Confirm
nvm --version
node -v
npm -v
Before you start: Vite needs a fairly recent Node version (Node 20 or higher at the time of writing). Check the current requirement at vite.dev.
If something goes wrong:
• nvm: command not found: close the terminal, open a new one and try again.
• Old Node installed with apt interferes: remove it with sudo apt remove --purge nodejs npm -y and sudo apt autoremove -y, then reinstall using nvm.
Checkpoint: node -v shows version 20 or higher.
Step 6.2: Create the Vite project
cd ~/writewise
npm create vite@latest frontend -- --template react
cd frontend
npm install
If Vite asks questions, choose React and JavaScript, and say No to anything experimental.
Expected result: A new frontend folder with package.json and a src folder.
Checkpoint: ls inside frontend shows package.json, src and node_modules.
Step 6.3: Add Bootstrap and Axios
npm install bootstrap axios
[ ] Open src/main.jsx and add this line near the top:
import 'bootstrap/dist/css/bootstrap.min.css';
Axios is optional. You can use the built-in fetch instead (shown in Step 6.5).
Step 6.4: Environment variables
[ ] In the frontend folder, create a .env file:
VITE_API_URL=http://127.0.0.1:8000/analyze
[ ] Read it in your code:
const API_URL = import.meta.env.VITE_API_URL;
Important: Variables starting with VITE_ are visible to anyone using your site. Never put secrets or API keys in them. Restart npm run dev whenever you change .env.
Step 6.5: Call the API from React
Goal: Send text to the backend and show the result.
[ ] Replace the contents of src/App.jsx with:
import { useState } from "react";

const API_URL = import.meta.env.VITE_API_URL;

function App() {
  const [text, setText] = useState("");
  const [result, setResult] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState("");

  const handleAnalyze = async () => {
    setLoading(true);
    setError("");
    try {
      const response = await fetch(API_URL, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ text }),
      });
      if (!response.ok) throw new Error("Request failed");
      setResult(await response.json());
    } catch (err) {
      setError("Could not reach the server. Is the backend running?");
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="container mt-5">
      <h1>WriteWise</h1>
      <textarea
        className="form-control mb-3"
        rows="5"
        value={text}
        onChange={(e) => setText(e.target.value)}
      />
      <button className="btn btn-primary" onClick={handleAnalyze} disabled={loading}>
        {loading ? "Analyzing..." : "Analyze"}
      </button>
      {error && <p className="text-danger mt-3">{error}</p>}
      {result && <pre className="mt-3">{JSON.stringify(result, null, 2)}</pre>}
    </div>
  );
}

export default App;
Step 6.6: Run both servers side by side
Goal: The backend and frontend each need their own terminal.
[ ] Terminal 1 (backend):
cd ~/writewise/backend
source venv/bin/activate
uvicorn main:app --reload
[ ] Terminal 2 (frontend): click the + icon in the VS Code terminal panel to open a new one.
cd ~/writewise/frontend
npm run dev
[ ] Open http://localhost:5173, type some text and click Analyze.
Expected result: The page shows a word count and character count.
Checkpoint: The result appears with no red error message.
Step 6.7: Troubleshooting the frontend
Problem
Fix
CORS error in the browser console
Check allow_origins in main.py matches the exact address in your browser (http://localhost:5173), then restart the backend
"Could not reach the server"
The backend is not running, or VITE_API_URL has the wrong port
.env value is undefined
The variable must start with VITE_, the file must be inside frontend, and you must restart npm run dev
Port 5173 is busy
Vite will offer another port. Update allow_origins in the backend to match
Error installing Vite
Your Node is too old. Check node -v and reinstall using nvm (Step 6.1)
[ ] Commit your work: git add ., git commit -m "Add React frontend", git push origin main.
Part 7: How It All Fits Together
The request and response flow
1.  React frontend: the user types text and clicks a button.
2.  HTTP request with JSON: React sends the request to the backend address.
3.  FastAPI backend: receives, validates and processes the data.
4.  Database, external API or AI model: the backend may fetch or compute more information here.
5.  JSON response: FastAPI sends the result back.
6.  React frontend: displays the result to the user.
HTTP methods
Method
Typical use
GET
Read data
POST
Create data or send data to process
PUT
Replace existing data
PATCH
Update part of existing data
DELETE
Remove data
Common status codes
Code
Meaning
200
OK, it worked
201
Created
400
Bad request (something is wrong with what was sent)
401
Not logged in
403
Logged in but not allowed
404
Not found
422
Data failed validation (common in FastAPI)
500
Server error (a bug in the backend)
Part 8: Roadmap (Next-Level Skills)
These are not steps to follow now. They are topics to learn next, once Parts 1 to 7 feel comfortable. Each one will become its own full guide.
[ ] Databases: what a database is, SQL versus NoSQL, creating tables and models, CRUD (Create, Read, Update, Delete) and migrations.
[ ] Security: password hashing (never store plain-text passwords), authentication versus authorization, JWT, input validation, SQL injection, XSS, rate limiting and HTTPS.
[ ] Testing and debugging: pytest and FastAPI TestClient for the backend, Vitest and React Testing Library for the frontend, plus browser developer tools, tracebacks and breakpoints.
[ ] Deployment: development versus production, building React, deploying FastAPI, hosting a database, production environment variables, domains, HTTPS, logging, CI/CD. Tools to know: Docker, Render, Vercel, Railway, GitHub Actions and Nginx.
[ ] AI development: API keys, prompt engineering, structured outputs, tool calling, embeddings, vector databases, RAG, agents and error and cost management. Tools to know: OpenAI API, LangChain, LangGraph, Pydantic AI, Ollama, n8n and MCP.
Appendix
A. Command cheat sheet
Linux
Command
Purpose
pwd
Show current folder
ls / ls -R
List files / list recursively
cd <folder> / cd .. / cd ~
Move around
mkdir, touch
Create folder, create file
cp, mv
Copy, rename or move
rm, rm -r
Delete file, delete folder (permanent)
realpath <file>
Full path of a file
zip -r, unzip
Compress, extract
sudo apt update, sudo apt upgrade -y
Refresh and update Ubuntu
code .
Open folder in VS Code
Git
Command
Purpose
git clone <url>
Download a repository
git status
See what changed
git pull origin main
Get latest changes
git add .
Stage all changes
git commit -m "message"
Save a snapshot
git push origin main
Upload to GitHub
git checkout -b <name>
Create and switch branch
git remote -v
Show linked remote
Python (pip and venv)
Command
Purpose
python3 -m venv venv
Create virtual environment
source venv/bin/activate
Activate it
deactivate
Leave it
pip install <name>
Install a package
pip list
Show installed packages
pip freeze > requirements.txt
Save packages
pip install -r requirements.txt
Install saved packages
uvicorn main:app --reload
Run FastAPI
Node (npm)
Command
Purpose
node -v, npm -v
Check versions
npm install
Install project dependencies
npm install <name>
Add a package
npm run dev
Start Vite dev server
npm run build
Build for production
B. Troubleshooting index (error message to fix)
Error message
Where to look
wsl not recognized
Step 1.2
externally-managed-environment
Step 3.3
ensurepip is not available
Step 3.2
command not found: uvicorn
Step 5.7
ModuleNotFoundError
Step 5.7
Address already in use
Step 5.7
Status 422 in Thunder Client
Step 5.5
CORS error in browser
Steps 5.6 and 6.7
nvm: command not found
Step 6.1
undefined from import.meta.env
Step 6.4 and 6.7
Merge conflict
Step 4.6
C. Extra VS Code extensions
Extension
Purpose
Live Server
Preview HTML pages with auto refresh
Tailwind CSS IntelliSense
Suggestions for Tailwind classes
Auto Rename Tag
Renames the matching HTML tag automatically
Auto Close Tag
Adds closing tags automatically
Path IntelliSense
Autocompletes file paths
GitLens
Shows who changed each line and when
GitHub Pull Requests
Manage pull requests inside VS Code
Docker
Manage containers and images
REST Client
Test APIs from .http files
Error Lens
Shows errors inline in your code
Even Better TOML
Support for TOML config files
SQLite Viewer
Browse SQLite databases
GitHub Copilot
AI-assisted coding
D. Glossary
Term
Meaning
API
A set of addresses (endpoints) that programs use to talk to each other
Endpoint
One specific address in an API, such as /analyze
CORS
Browser rule that controls which websites may call your API
JSON
A simple text format for sending structured data
Commit
A saved snapshot of your project in Git
Branch
A separate line of work in Git
Repository (repo)
A project folder tracked by Git
Environment variable
A setting or secret stored outside your code
Virtual environment
A private set of Python packages for one project
requirements.txt
A list of Python packages a project needs
package.json
A list of JavaScript packages and scripts a project needs
Vite
A fast tool for creating and running React projects
WSL
Windows Subsystem for Linux, which runs Ubuntu inside Windows

# Giko


---
---
---
---


# Running Ubuntu Linux Commands on Windows

## 1. Install Python 3
- Download Python 3 from [python.org](https://www.python.org/downloads/)
- During installation, **check "Add Python to PATH"** to enable terminal commands
- Verify installation: open Command Prompt and run `python --version`

## 2. Set Up VS Code
- Download and install [Visual Studio Code](https://code.visualstudio.com/)
- Install recommended extensions:
  - **Thunder Client** (API testing)
  - **Prettier** (code formatting)
  - **Python** (IntelliSense and code completion)
  - Live preview/ Live server  
  - Tailwind CSS IntelliSense  
  - Auto rename tag 
  - Autocomplete 
  - Auto close tag
  - Path intellisence
  - Es lint
  - Git lense
  - **WSL** (Windows Subsystem for Linux integration)

## 3. Install WSL (Windows Subsystem for Linux)
- Open **PowerShell or Command Prompt as Administrator**
- Run: `wsl --install`
- Restart your computer when prompted
- Ubuntu will be installed by default
- On first launch, create a username and password for Ubuntu

## 4. Launch Ubuntu Terminal
- Search for **"Ubuntu"** in the Windows search bar
- Click to open the Linux terminal
- Alternatively, open Ubuntu directly from VS Code using the integrated terminal

### **To access Linux files manually/ using GUI**  

* **Click on `This pc`** — Then--> `linux`--> `Ubuntu` --> `home` --> `user e.g outa`    

### **Essential Linux Commands**

* **`ls`** — Lists all files and folders in the current directory.
* **`cd <directory_name>`** — Changes the current working directory to the specified folder.
* **`cd ..`** — Moves one level up from the current directory.
* **`code .`** — Opens the current directory in Visual Studio Code (requires VS Code + `code` command installed).

## 5. Create Project Structure
```bash
# Create project directory
mkdir my_project
cd my_project

# Create project files    
touch main.py  

# Delete directory or File    
rm -r <directory-name>

# To get file path      
pwd <file-name>

# To rename file or folder
mv "old name" "new name"  #e.g mv Appp.js App.js

# To zip a folder  
zip -r folder_name.zip folder_name/

# To unzip a folder
unzip -l folder_name.zip

# Install zip 
sudo apt install zip 
```

## 6. Update Ubuntu System
```bash
sudo apt update
sudo apt upgrade -y
```

## 7. Install Python Package Manager
```bash
sudo apt install python3-pip -y
```

## 8. Install Pipenv
```bash
# Primary method
sudo apt install pipenv -y

# Alternative if the above fails
pip3 install --user pipenv

# if you get error, uninstall the older version of pipenv using this command:
sudo apt remove pipenv

# Then re- install new version using this command: 
pip install --user pipenv
# Or 
sudo apt install pipenv


# Add pipenv to PATH (if using alternative method)
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

## 9. Set Up Virtual Environment
```bash
# Create and activate virtual environment
pipenv shell

# Or use venv
python3 -m venv venv
source venv/bin/activate
```

## 10. Create Entry File
```bash
# Create main application file
touch app.py
# or
touch main.py

# Edit using nano or VS Code
nano app.py
```

## 11. Manage Dependencies with requirements.txt
```bash
# Install pipreqs (generates requirements.txt from imports)
pip install pipreqs

# Generate requirements.txt
pipreqs . --force

# Install dependencies from requirements.txt
pip install -r requirements.txt

# Update requirements.txt after adding new packages
pipreqs . --force
# Then reinstall
pip install -r requirements.txt

**Nb:**
# If you run into version compatibility issues while working on your project, simply install the available version first. Then, update the version in your requirements.txt file to the one compatible with your project, and reinstall the dependencies using the updated requirements.txt
```

**Install all depencies your project requires e.g Django, Pandas e.t.c**
```bash
pip install <dependency-name>

# To be able to run (java related tools e.g language-too-python) on ubuntu, you need to also install Java version 8+
sudo apt install default-jre

# if you need specific version of Java, un-install the version installed using the command:
sudo apt remove default-jre
sudo apt autoremove

# then install the needed version by specifying the version using command e.g
sudo apt update
sudo apt install openjdk-21-jre

# Confirm if installation is successful using command:
java -version
```

## 12. Run FastAPI Server
```bash
# Install fastapi
pip install fastapi

# Install uvicorn
pip install uvicorn

# Start development server (auto-reload enabled)
uvicorn app:app --reload
# or if your file is named main.py
uvicorn main:app --reload

# Ensure you cd to the directory where the main.py or app.py is located before starting the uvicorn server
main: refers to the name of your Python file (e.g., main.py).
app: refers to the FastAPI instance you created within that file (e.g., app = FastAPI()).
--reload: (optional) enables automatic reloading of the server when code changes are detected, which is useful during development.


# Access your API at: http://127.0.0.1:8000
# Interactive docs at: http://127.0.0.1:8000/docs. This will confirm the API is up and ready to receive requests from your frontend  
```

## 13. Testing your endpoints    
```bash
# You can test your endpoint are responding correctly before proceeding with frontend work. You can do it on Postman or Thunderclient. 
# To Install Thunder Client, go to vs code extension and search Thunder client  
# It should appear on the side of the vscode after installation, click on it to open it  
# Click Create New Request,  Choose method: POST 
# In the URL field type your local server address e.g http://127.0.0.1:8000/analyze 
# In the Body tab, Select JSON and add what you want to test e.g 
{
  "text": "This is a sample text that may contains grammar issues and also we want to check engagement and tone."
}  
# Then click send button
```

## Additional Tips
- **Access Windows files from Ubuntu**: `/mnt/c/Users/YourUsername/`
- **Access Ubuntu files from Windows**: `\\wsl$\Ubuntu\home\your_ubuntu_username\`
- **Stop the server**: Press `Ctrl + C` in the terminal
- **Deactivate virtual environment**: Run `exit` (for pipenv) or `deactivate` (for venv)
- **Check installed packages**: `pip list`

## Troubleshooting
- If `pipenv` command not found, ensure `~/.local/bin` is in your PATH
- If port 8000 is busy, use: `uvicorn app:app --reload --port 8001`
- For permission errors, ensure you're running commands within your project directory



## 🖥️ 4. FRONTEND (React + Bootstrap)    

### 1. Install Node.js and npm

  * **Check Installation:**
```bash
    node -v
    npm -v
# If not installed, use this command to install i.e
    sudo apt install nodejs npm

# If you get version outdated errors, 
# Step 1. Remove older versions using this command: 
sudo apt remove --purge nodejs npm && sudo apt autoremove

# Step 2: Install nvm using the complete URL with latest version e.g  v0.41.0 being the current latest version:
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.41.0/install.sh | bash

# Step 3: Activate nvm in your terminal
source ~/.bashrc

# Step 4: Install the latest Long-Term Support (LTS) version of Node.js
nvm install --lts

# Step 5: Verify everything worked
nvm --version
node -v
npm -v
```
-----

### 2. Create React App (Recommended: Use Vite)

For faster setup and development, **Vite** is the modern standard, replacing `create-react-app`.

  * **Navigate to Project Root:** e.g  
    ```bash
    cd writewise
    ```
  * **Create Vite Project:**
    ```bash
    npm create vite@latest --template react
    ```
    (This creates the app using the official React template.)
  * **Go into Frontend Folder:**
    ```bash
    cd frontend
    ```
  * **Install Node Modules:**
    ```bash
    npm install
    ```
Vite works with node version 20 and above. If you get error installing vite then,

✅ Remove old Node
✅ Install latest Node

### **STEP 1 — Remove the old Node**

```bash
sudo apt remove nodejs npm -y
```

### Then Remove all old Node files

```bash
sudo apt remove --purge nodejs libnode-dev npm -y
sudo apt autoremove -y
```

> `--purge` ensures config files and headers are removed.
> `autoremove` cleans any leftover dependencies.

### Then Clean the apt cache

```bash
sudo apt clean
sudo rm -rf /var/cache/apt/archives/nodejs_*.deb
```

This prevents the broken `.deb` from being reused.


### **STEP 2 — Install Node 20 (or any new version you want)**

Copy/paste these:

```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
```
if you want another version, just change `_20.x` with the version you want  

### **STEP 3 — Confirm**

```bash
node -v
npm -v
```

You will now see Node 20 installed (or the new version you indicated).


# 🟢 After Node 20 is installed → Vite will work

Try again:

```bash
npm create vite@latest --template react
```

-----

### 3. Install Dependencies and Configuration

Install core libraries, making sure you are inside the `frontend` directory.

  * **Install react and react-dom:**
    ```bash
    npm install  
    npm install react react-dom  
    ```
  * **Install Bootstrap for Styling:**
    ```bash
    npm install bootstrap
    ```
  * **Import Bootstrap CSS:** Add the following line to your main entry file (usually **`src/main.jsx`** or **`src/index.js`**):
    ```javascript
    import 'bootstrap/dist/css/bootstrap.min.css';
    ```
  * **Optional: Install Axios for HTTP requests:**
    ```bash
    npm install axios
    ```
-----

### 5. Connect Frontend to Backend (Environment Variables)

Create an environment variable to securely store your API URL.

  * In the **`frontend`** folder, create a **`.env`** file:
      * **If using Vite:** Variables must be prefixed with `VITE_`.
        ```
        VITE_API_URL=http://127.0.0.1:8000/analyze
        ```
      * **If using create-react-app (CRA):** Variables must be prefixed with `REACT_APP_`.
        ```
        REACT_APP_API_URL=http://127.0.0.1:8000/analyze
        ```
  * **Access in your React App:**
    ```javascript
    // If using Vite
    const API_URL = import.meta.env.VITE_API_URL; 

    // If using CRA
    const API_URL = process.env.REACT_APP_API_URL;
    ```

-----

### 6. Run React Locally

  * **Make sure your backend is running** at `http://127.0.0.1:8000` for local testing.
  * **Start the Development Server:**
    ```bash
    cd frontend
    npm run dev  # If using Vite
    # OR
    npm start    # If using create-react-app
    ```

The app will typically open at `http://localhost:5173` (Vite) or `http://localhost:3000` (CRA).

.  
# Git  

## **Step 1: Install Git (if not already installed)**

1. Go to [https://git-scm.com/downloads](https://git-scm.com/downloads).
2. Download the installer for your operating system (Windows / macOS / Linux).
3. Run the installer and follow the default setup (on Windows, select “Git Bash” during installation).
4. To confirm installation:

   * Open a terminal (Command Prompt, Git Bash, or Terminal on macOS/Linux).
   * Run:

     ```bash
     git --version
     ```
   * You should see a version number (e.g., `git version 2.45.0`).

---

### **Set identity for all repositories (recommended)**

This will help the git to know you better.  
Open the terminal and run these command:  

```bash
git config --global user.name "your_username"
git config --global user.email "your_email_address"
```

Example (replace with your actual GitHub details):  
e.g  
```bash
git config --global user.name "outaagunga"
git config --global user.email "jeconiaagunga@gmail.com"
```

---


## **Step 2: Copy the Repository URL from GitHub**

1. Go to your GitHub account and open the repository you created.
2. Click the green **Code** button.
3. Under **Clone**, you’ll see options:

   * HTTPS (recommended for beginners)
   * SSH (if you’ve set up SSH keys)
   * GitHub CLI

   For now, choose **HTTPS**.
4. Copy the URL (e.g., `https://github.com/your-username/your-repo.git`).

---

## **Step 3: Choose a Local Folder to Store Your Repo**

1. On your PC, decide where you want the repo (e.g., `Documents`, `Projects`, or Desktop).
2. Open a terminal in that location:

   * **Windows**: Open Git Bash, then use `cd` to move to your folder. Example:

     ```bash
     cd ~/Documents/Projects
     ```
   * **macOS/Linux**: Use Terminal and do the same with `cd`.

---

## **Step 4: Clone the Repository**
```bash
# open the project folder in the terminal i.e cd project-folder
# You can also right click the project folder and select open in terminal
# use the command to clone the repository on your pc:
git clone <repository URL> .
# period (.) at the end of the git clone command ensures the command does not create another folder, but does everything inside the current project folder.
# Then
git init 
```

## **Step 6: Configure Git (First Time Only)**
```bash
# If you haven’t set up your identity yet, configure it:
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"

# Check if it’s saved correctly:
git config --list
```

## **Step 7: Verify Remote Connection**
```bash
# Run:
git remote -v

# You should see something like:
origin  https://github.com/your-username/your-repo.git (fetch)
origin  https://github.com/your-username/your-repo.git (push)

# This confirms your local repo is linked to GitHub.
```

## At this point, your repository is cloned and ready. You can now:
```bash
# Add files (stage files)
git add .

# Commit changes
git commit -m "Initial commit"

# Push to GitHub
git push origin main
```

## **Setting up SSH Key for Easy Project Transmission**
```bash
# To avoid typing your GitHub username and password every time, you can use SSH keys for secure authentication. Here’s how to set it up:

# Step 1: Generate SSH Keys. On your local machine, open a terminal and run:
ssh-keygen -t ed25519 -C "your_primary_email_address"

# This creates two files inside the .ssh directory:
  id_ed25519
  id_ed25519.pub

# Step 2: Navigate to the .ssh directory. You can use this command:
cd ~/.ssh

# Once inside the .ssh directory, Open the public key file (you can use VS Code or any text editor) through this command:
code id_ed25519.pub

# Then Copy the full contents of this file.

# Step 3: Add the Public Key to your GitHub account i.e  
1. Go to GitHub → Settings → SSH and GPG Keys.
2. Click New SSH Key.
3. Give it a title (e.g. My Laptop).
4. Paste the contents you had copied from your public key file.
5. Click Add SSH Key.

# Step 4: Clone a Project Using SSH
1. On GitHub, open the repository you want to clone
2. Click the green Code button
3. Select SSH from the dropdown
4. Copy the SSH URL. It looks like e.g  git@github.com:username/repo.git

# Then go back to your terminal, navigate to the directory where you want to clone the repo type:
git clone <SSH_URL_you_had_copied>
```

## Removing Old SSH Keys from GitHub
```bash
# Log in to GitHub account
1. Click your profile picture → Settings
2. Go to SSH and GPG Keys
3. In the left sidebar, click SSH and GPG keys. You’ll see a list of SSH keys linked to your account e.g 
  Laptop-2023    Added on Jan 10, 2023
  Work-PC        Added on May 14, 2024
  New-Laptop-2025 (your latest one)

# Select the one you want to remove 
Next to the key, click the Delete button (trash icon)
Confirm removal
```

## Steps to Pull Changes from GitHub
```bash
# Step 1: Make Sure You’re in the Repo Folder ie 
cd your-repo

# Step 2: Check Your Current Branch
git branch

# You’ll usually see:
* main
or *master depending on your repo

# Step 3: update your local copy with GitHub i.e:
git pull origin main
(or replace main with master if your repo uses that)

# ⚡ NB: Always git pull before starting new work so your local repo is fresh and avoids conflicts.
```

## Generate and save the File structure into a text file.
```bash
# Method 1: Using the tree Command🌳
# 1. Check if `tree` is Installed. Navigate to your project’s root directory, then run:
tree

# If you see a tree view → you’re good to go. If you get `command not found`, install it with:
Debian/Ubuntu: `sudo apt-get install tree`
Fedora/Red Hat: `sudo yum install tree` or `sudo dnf install tree`
macOS (Homebrew): `brew install tree`

# 2. Generate a Structure File. Run:
tree -I 'node_modules|venv|__pycache__|.git' > PROJECT_STRUCTURE.txt

# Explanation:
`tree` → shows nested structure.
`-I` → exclude unwanted folders (edit list as needed, e.g., add `dist` or `target`)
`>` → saves output to a file

# Method 2: Using `ls` (If `tree` Isn’t Available)
# If you can’t install `tree`, use `ls` (less pretty but built-in) i.e run: 
ls -R > PROJECT_STRUCTURE.txt

# Method 3: Windows Built-in `tree` Command
# On Windows, `tree` is built into **Command Prompt** and **PowerShell**
tree /A /F > PROJECT_STRUCTURE.txt

# Options:
`/A` → ASCII characters only
`/F` → list files as well as folders

# How to Read the Structure File
Once you open `PROJECT_STRUCTURE.txt`, focus on:

**Entry Points:** Files like `index.js`, `app.py`, `server.go`, `main.c`
**Configuration:** `package.json`, `requirements.txt`, `pom.xml`, `settings.py`
**Source Code:** Typically inside `src/` or `lib/`.
**Assets/Templates:** `static/`, `public/`, `assets/`, `templates/`
```

---
---
---

## **C. Environment variables and secrets management**
```
# Install dotenv
pip install python-dotenv
```

Create a `.env` file in your backend project:

```
DATABASE_URL=your_database_url
SECRET_KEY=your_secret_key
API_KEY=your_api_key
```

Access these variables in Python:

```
import osfrom dotenv import load_dotenvload_dotenv()SECRET_KEY = os.getenv("SECRET_KEY")
```

Also explain:

- Never upload `.env` files to GitHub.
- Create `.env.example` containing placeholder values.
- Keep backend secrets separate from frontend environment variables.
- Vite variables prefixed with `VITE_` are exposed to frontend code and must never contain secrets.

## **D. Connecting React to FastAPI properly**

**1. Install CORS middleware in FastAPI**

```
from fastapi.middleware.cors import CORSMiddlewareapp.add_middleware(    CORSMiddleware,    allow_origins=["http://localhost:5173"],    allow_credentials=True,    allow_methods=["*"],    allow_headers=["*"],)
```

**2. Create a React function to call your API**

```
const response = await fetch(
  "http://127.0.0.1:8000/analyze",
  {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      text: "Sample text to analyze",
    }),
  }
);

const data = await response.json();

console.log(data);
```

**3. Understand the request-response cycle**

**React Frontend**

User enters information and submits a request

HTTP request / JSON response

**FastAPI Backend**

Receives, processes and validates information

**Database / External API / AI Model**

Processes or retrieves the required information

**FastAPI returns JSON to React**

React displays the results to the user

## **E. Database installation and management**

Your guide doesn't cover databases.

For full-stack applications, you should understand:

- What a database is.
- SQL versus NoSQL databases.
- Database creation and connection.
- Database models and tables.
- CRUD operations (Create, Read, Update, Delete).
- Database migrations.

## **F. API documentation and testing**
- Understanding REST APIs.
- HTTP methods: GET, POST, PUT, PATCH and DELETE.
- HTTP status codes: 200, 201, 400, 401, 403, 404, 422 and 500.
- Request headers.
- Request bodies.
- Query parameters.
- Path parameters.
- Authentication and authorization.
- API error handling.

## **G. Frontend development essentials**
- React components.
- JSX.
- Props.
- State and `useState`.
- `useEffect`.
- React Router for navigation.
- Form handling.
- API calls.
- Conditional rendering.
- Loading indicators and error messages.
- Reusable components.
- Frontend folder structure.

## **H. Application security**
- Password hashing (never store plain-text passwords).
- Authentication and authorization.
- JWT (JSON Web Tokens).
- API key management.
- Input validation.
- SQL injection prevention.
- Cross-Site Scripting (XSS).
- Rate limiting.
- HTTPS.
- Secure handling of user-uploaded files.

For FastAPI, :
- Pydantic for data validation.
- FastAPI security utilities.
- OAuth2 and JWT authentication.

## **I. Testing and debugging**

| Tool                    | What it does                                  |
| ----------------------- | --------------------------------------------- |
| pytest                  | Tests Python code                             |
| FastAPI TestClient      | Tests API endpoints                           |
| Vitest                  | Tests React/frontend code                     |
| React Testing Library   | Tests React components                        |
| Browser Developer Tools | Inspects frontend errors and network requests |
| VS Code Debugger        | Helps identify and troubleshoot code errors   |

Also:
- Read terminal error messages.
- Read Python tracebacks.
- Inspect browser console errors.
- Inspect failed network requests.
- Use breakpoints.
- Read application logs.

## **J. Deployment and hosting**
- Production versus development environments.
- Building a React application.
- Deploying FastAPI.
- Hosting databases.
- Configuring production environment variables.
- Domain names and DNS.
- HTTPS and SSL certificates.
- Application logging and monitoring.
- CI/CD (Continuous Integration and Continuous Deployment).

Some technologies worth knowing:
| Technology     | Purpose                          |
| -------------- | -------------------------------- |
| Docker         | Containerizing applications      |
| Render         | Hosting applications             |
| Vercel         | Hosting frontend applications    |
| Railway        | Application and database hosting |
| GitHub Actions | Automating development workflows |
| Nginx          | Web server and reverse proxy     |

## **L. AI and automation development tools**

| Technology  | Purpose                                             |
| ----------- | --------------------------------------------------- |
| OpenAI API  | Integrating AI models into applications             |
| LangChain   | Building applications around language models        |
| LangGraph   | Building stateful, multi-step AI workflows          |
| Pydantic AI | Building structured AI-powered applications         |
| Ollama      | Running supported AI models locally                 |
| n8n         | Workflow automation                                 |
| MCP         | Connecting AI models to external tools and services |

## Also :
- API keys and model authentication.
- Prompt engineering.
- Structured outputs.
- Function/tool calling.
- Embeddings.
- Vector databases.
- Retrieval-Augmented Generation (RAG).
- AI agents and multi-step workflows.
- Error handling and token/cost management.

# **3. Other useful tools to add to your VS Code setup**

| Extension/tool       | Purpose                                 |
| -------------------- | --------------------------------------- |
| GitHub Pull Requests | Manage GitHub pull requests             |
| Docker               | Manage Docker containers and images     |
| REST Client          | Test APIs directly in VS Code           |
| Error Lens           | Highlight errors and warnings           |
| Even Better TOML     | Work with TOML configuration files      |
| SQLite Viewer        | View SQLite databases                   |
| GitHub Copilot       | AI-assisted coding                      |
| Prettier             | Format frontend code                    |
| ESLint               | Identify JavaScript/React code problems |


The biggest omissions are:

1. Connecting React to FastAPI.
2. Database installation and integration.
3. Git branching and collaboration.
4. Environment variables and security.
5. Application testing and debugging.
6. Docker and deployment.
7. AI application development and automation.

   
