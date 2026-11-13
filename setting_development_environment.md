
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

   
