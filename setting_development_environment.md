
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
```
**Install all depencies your project requires e.g Django, Pandas e.t.c**
```bash
pip install <dependency-name>
```
## 12. Run FastAPI Server
```bash
# Install uvicorn
pip install uvicorn

# Start development server (auto-reload enabled)
uvicorn app:app --reload
# or if your file is named main.py
uvicorn main:app --reload

# Access your API at: http://127.0.0.1:8000
# Interactive docs at: http://127.0.0.1:8000/docs
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




# Setting development environment:  
**a)** Download the latest version of `Python` from the official website   
**b)** During installation (when using Windows)-  `Add Python to PATH`  
**c)** If you are using windows, also ensure you install `WSL`  
**d)** Install an IDE (e.g `VS Code`)   
**e)** Install VS Code extensions e.g (`prettier formatters, autocomplete and extensions`)  
**f)** Install a virtual environment manager e.g `venv`  
**g)** Install python extension on the vs code to be able to run python directly from the vs code. Then click ▶️ to run python  

## Set up a Virtual Environment:  
They isolate project dependencies, preventing conflicts between different projects and ensuring your code runs consistently.  
**a)** Create project folder and open the folder in your `terminal`  
**b)** Launch virtual environment using command: `pip install pipenv` (if you get an error use `sudo apt install pipenv`)  
**c)** Then activate the virtual environment: `pipenv shell`  
**d)** if you get error, uninstall the older version of `pipenv` using this command: `sudo apt remove pipenv`  
Then re- install new version using this command:  
* `pip install --user pipenv`  
* Then `sudo apt install pipenv` Then re-activate virtual environment: `pipenv shell`  

## Create requirements.txt file.  
**a)** Install `pipreqs` using this command: `pip install pipreqs`  
Pipreqs is a tool that automatically generates a `requirements.txt` file for your Python project by scanning your project folder for all the libraries your code is importing.  
**b)** Then run the `pipreqs` tool on the current directory using this command: `pipreqs .`   
**c)** Then install all the Python packages listed inside your `requirements.txt` file using this command: `pip install -r requirements.txt`  

**Nb:**    
If you run into version compatibility issues while working on your project, simply install the available version first. Then, update the version in your requirements.txt file to the one compatible with your project, and reinstall the dependencies using the updated requirements.txt  

## Install Dependencies  
(Dependencies are frameworks that you will use for your project e,g `Faker, Pandas, Django, Rest, Flask` e.t.c).   
Use `pip` which is the recommended Python's package installer. Ensure you do this after activating your virtual environment.  
`pip install <dependency-name>`    

* To be able to run language-too-python on ubuntu, you need to also install Java version 8+  
`sudo apt install default-jre`  
* if you need specific version of Java, un-install the version installed using the command:  
`sudo apt remove default-jre`  
`sudo apt autoremove`  
then install the needed version by specifying the version using command e.g  
`sudo apt update`  
`sudo apt install openjdk-21-jre`  
Confirm if installation is successful using command `java -version`  

## Running the project locally   
To run you project locally: We can run it locally using Uvicorn e.g  
`uvicorn main:app --reload`  # if your main app is named `main.py`    
or  
`uvicorn app:app --reload`  # if your main app is named `app.py`    
Ensure you `cd` to the directory where the `main.py` or `app.py` is located before starting the uvicorn server  
•	`main`: refers to the name of your Python file (e.g., `main.py`).  
•	`app`: refers to the FastAPI instance you created within that file (e.g., `app = FastAPI())`.  
•	`--reload`: (optional) enables automatic reloading of the server when code changes are detected, which is useful during development.  

Verify the fastapi Server is running. Once running, you can verify your server by checking the interactive fastapi documentation. i.e  
Open your browser and navigate to: `http://127.0.0.1:8000/docs`. This will confirm the API is up and ready to receive requests from your frontend  
Ensure `fastapi` and `uvicorn` are installed for the backend server to run.  
To install them, run `pip install fastapi uvicorn`  

## Testing your endpoints    
You can test your endpoint are responding correctly before proceeding with frontend work.   
You can do it on `Postman` or `Thunderclient`.  
We are going to do it on `thunderclient`  
.  
**Step 1:** Install Thunder Client Thunder client is a vs code extension. To install it:  
•	Go to vscode- then extensions  
•	Search for thunder client and install it  
•	It should appear on the side of the vscode after installation, click on it to open it  
.  
**Step 2:** Create a New Request  
1.	In Thunder Client, click `New Request`  
2.	Choose method: `POST`  
3.	In the URL field, e.g ours is:  
`http://127.0.0.1:8000/analyze`  
.  
**Step 3:** Add the `JSON body`  
1.	Click the `Body tab`  
2.	Select `JSON`  
3.	Paste your test text inside:  
`{
  "text": "This is a sample text that may contains grammar issues and also we want to check engagement and tone."
}`  
.  
**Step 4:** Hit SEND  
Click the Send button.  
Your API will return something like this `[  ]`  

## 🖥️ 4. FRONTEND (React + Bootstrap)    

### 1. Install Node.js and npm

  * **Check Installation:**
    ```bash
    node -v
    npm -v
    ```
  * If not installed, download and install the **LTS (Long Term Support) version** from the [Node.js official website](https://nodejs.org/).
  * or use terminal commandline e.g
```bash
    sudo apt install nodejs npm
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
When you want to **clone a repository from GitHub**, you **should not use `git init`** before `git clone`.

You only need to use the `git clone` command.

The `git clone` command does everything necessary to create a copy of the existing repository, including:

1.  Creating a **new directory** (unless you specify one that already exists, in which case it will clone into that directory) for the project.
2.  Downloading all the files and history from the remote repository.
3.  **Automatically running the equivalent of `git init`** inside that new directory, setting up the necessary `.git` folder and making it a local Git repository.
4.  Setting up the remote repository as the default **`origin`** and configuring tracking branches.

## 🛠️ The Correct Steps

Since you've already created and navigated into a project directory, here's what to do:

### Option 1: Clone into a *New* Folder (Recommended)

This is the standard and safest approach. `git clone` will create the project folder for you inside your current location.

| Command | Explanation |
| :--- | :--- |
| `cd ..` | Navigate *out* of your current empty project directory. |
| `git clone <repository URL>` | Clones the repository, creating a new folder named after the project (e.g., `my-project`). |
| `cd my-project` | Navigate into the newly created, initialized repository folder. |

### Option 2: Clone into the *Existing, Empty* Folder

If you absolutely want the cloned contents to be directly inside the directory you've already created and navigated into, you can use a period (`.`) at the end of the `git clone` command. **Ensure the directory is completely empty** before doing this.

| Command | Explanation |
| :--- | :--- |
| `git clone <repository URL> .` | Clones the repository directly into the current directory (`.`), and initializes it. **Do not run `git init` before this.** |

## 🚫 Why `git init` is Wrong Before `git clone`

If you run `git init` first, you create an **empty Git repository** in your current directory. When you then try to run `git clone`, Git will complain because it detects an existing repository and doesn't want to overwrite or mix the repositories without explicit instructions.

**In summary: Just use `git clone <repository URL>`** (or `git clone <repository URL> .` if you want it in your current, empty directory). 


#### To clone the repository using https:
Run the command (replace with your repo URL):

```bash
git clone https://github.com/your-username/your-repo.git
```

What happens:

* Git creates a new folder named after your repository.
* It downloads all the files, branches, and history.

---

## **Step 5: Enter the Repository Folder**

```bash
cd your-repo
```

Now you’re inside your cloned project folder.

---

## **Step 6: Configure Git (First Time Only)**

If you haven’t set up your identity yet, configure it:

```bash
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"
```

Check if it’s saved correctly:

```bash
git config --list
```

---

## **Step 7: Verify Remote Connection**

Run:

```bash
git remote -v
```

You should see something like:

```
origin  https://github.com/your-username/your-repo.git (fetch)
origin  https://github.com/your-username/your-repo.git (push)
```

This confirms your local repo is linked to GitHub.

---

✅ At this point, your repository is cloned and ready. You can now:

* Add files → `git add .`
* Commit changes → `git commit -m "Initial commit"`
* Push to GitHub → `git push origin main`

---

Do you want me to also include **steps for making your first commit & push after cloning** (so your local changes sync back to GitHub)?


Great 🚀 Let’s continue from where we left off. Now that you have **cloned your repo locally**, I’ll walk you through making your **first commit and push** so that any changes you make are synced back to GitHub.

---
Got it 👍 Let’s clean up your text, make it more professional, and correct small issues while keeping it simple and step-by-step.

---

# **Setting up SSH Key for Easy Project Transmission**

To avoid typing your GitHub username and password every time, you can use **SSH keys** for secure authentication. Here’s how to set it up:

---

## **Step 1: Generate SSH Keys**

On your local machine, open a terminal and run:

```bash
ssh-keygen -t ed25519 -C "your_primary_email_address"
```

* This creates **two files** inside the `.ssh` directory:

  * `id_ed25519` → your **private key** (keep this safe, never share).
  * `id_ed25519.pub` → your **public key** (this is the one you’ll upload to GitHub).

---

## **Step 2: Open the Public Key**

Navigate to the `.ssh` directory:

```bash
cd ~/.ssh
```

Open the public key file (you can use VS Code or any text editor):

```bash
code id_ed25519.pub
```

Copy the full contents of this file.

---

## **Step 3: Add the Public Key to GitHub**

1. Go to **GitHub → Settings → SSH and GPG Keys**.
2. Click **New SSH Key**.
3. Give it a title (e.g., *My Laptop*).
4. Paste the contents of your public key.
5. Click **Add SSH Key**.

---

## **Step 4: Clone a Project Using SSH**

1. On GitHub, open the repository you want to clone.
2. Click the green **Code** button.
3. Select **SSH** from the dropdown.
4. Copy the SSH URL (it looks like `git@github.com:username/repo.git`).

Back in your terminal, navigate to the directory where you want the repo and run:

```bash
git clone git@github.com:username/repo.git
```

---

✅ That’s it! From now on, you can **push and pull** without entering your username or password each time.

---

👉 Do you also want me to add the step for **testing the connection with GitHub** (`ssh -T git@github.com`) so you can confirm the key works before cloning?


# 🔹 Steps for First Commit & Push

## **Step 1: Check Your Repo Status**

Make sure you’re inside the repo folder:

```bash
cd your-repo
```

Check the repo status:

```bash
git status
```

This will show:

* Untracked files (new files not added yet)
* Modified files (if you edited something)
* Clean (nothing to commit)

---

## **Step 2: Create or Modify a File**

If your repo is empty, let’s create a simple file:

```bash
echo "# My First Project" > README.md
```

If you already have files, you can edit one with your editor (VS Code, Notepad, etc.).

---

## **Step 3: Stage the Changes**

Add files to the staging area:

```bash
git add README.md
```

To add **all files** at once:

```bash
git add .
```

---

## **Step 4: Commit the Changes**

Now commit with a message:

```bash
git commit -m "Add initial README file"
```

This records the snapshot in your local repo.

---

## **Step 5: Push Changes to GitHub**

Push to the default branch (usually `main` or `master`):

```bash
git push origin main
```

If your repo uses `master` instead of `main`, run:

```bash
git push origin master
```

👉 If it’s your first push, Git may ask for your GitHub **username** and **password/token**.

* GitHub now requires a **Personal Access Token** instead of password.
* You can generate one here: [GitHub Tokens](https://github.com/settings/tokens).
* Use that token as your password when prompted.

---

## **Step 6: Verify on GitHub**

1. Go to your repository page on GitHub.
2. Refresh, and you should see your new file/changes. 🎉

---

# 🔹 Quick Summary

1. `git status` → check what changed
2. `git add .` → stage changes
3. `git commit -m "message"` → save snapshot
4. `git push origin main` → send to GitHub

---

⚡ Bonus: If you’re using VS Code, you can do all these inside the **Source Control tab** with buttons instead of commands.

---

Do you want me to also show you **how to pull changes from GitHub** (when you or someone else updates the repo online)? That’s the other half of syncing.

Perfect 👍 Now let’s cover the **other half of syncing**: pulling changes from GitHub to your local machine.

This is important if:

* You made changes directly on GitHub (like editing a file in the browser).
* Someone else contributed to the repository.
* You work on multiple devices and need to stay updated.

---

# 🔹 Steps to Pull Changes from GitHub

## **Step 1: Make Sure You’re in the Repo Folder**

```bash
cd your-repo
```

---

## **Step 2: Check Your Current Branch**

```bash
git branch
```

You’ll usually see:

```
* main
```

(or `master`, depending on your repo).

---

## **Step 3: Fetch and Merge Changes**

To update your local copy with GitHub:

```bash
git pull origin main
```

(or replace `main` with `master` if your repo uses that).

This does **two things**:

1. `git fetch` → Downloads the latest changes from GitHub.
2. `git merge` → Merges them into your local branch.

---

## **Step 4: Handle Merge Conflicts (if any)**

If you and GitHub have different edits to the same file, Git will warn you of a **merge conflict**.

* Open the conflicted file.
* You’ll see markers like this:

  ```text
  <<<<<<< HEAD
  Your local changes
  =======
  GitHub changes
  >>>>>>> origin/main
  ```
* Manually edit the file to keep what you want.
* Save, then stage and commit:

  ```bash
  git add .
  git commit -m "Resolve merge conflict"
  ```

---

## **Step 5: Verify Sync**

Check the repo status:

```bash
git status
```

It should say:

```
Your branch is up to date with 'origin/main'.
```

That means your local repo matches GitHub 🎉

---

# 🔹 Quick Workflow Recap

* **Push** when you want to send local changes →
  `git add .` → `git commit -m "msg"` → `git push origin main`
* **Pull** when you want to update from GitHub →
  `git pull origin main`

---

⚡ Pro Tip: Always `git pull` before starting new work so your local repo is fresh and avoids conflicts.

---

Do you want me to also show you the **full Git workflow diagram** (clone → edit → add → commit → push → pull) so you can see how all these steps connect?

Got it ✅ You want a **complete list of Git commands with their descriptions**, so nothing is missed.
Git has a very large command set (over 150), including both **high-level (porcelain)** and **low-level (plumbing)** commands.

I’ll give you:

1. **Core everyday commands** (most used).
2. **Intermediate commands** (less frequent, but important).
3. **Advanced plumbing commands** (internal, rarely needed but good to know).

This will cover **all Git commands** you can encounter.

---

# 🔹 1. Everyday Git Commands (Most Used)

| Command                     | Description                                                            |
| --------------------------- | ---------------------------------------------------------------------- |
| `git init`                  | Initialize a new Git repository in the current folder.                 |
| `git clone <url>`           | Copy a remote repository (GitHub, GitLab, etc.) to your local machine. |
| `git config`                | Set or get configuration (username, email, editor, aliases).           |
| `git status`                | Show the state of your working directory and staging area.             |
| `git add <file>`            | Stage a file for commit.                                               |
| `git add .`                 | Stage all changes in the current folder.                               |
| `git commit -m "msg"`       | Save changes to the local repository with a message.                   |
| `git log`                   | View commit history.                                                   |
| `git diff`                  | Show changes not yet staged.                                           |
| `git diff --staged`         | Show changes that are staged but not committed.                        |
| `git push origin <branch>`  | Send local commits to remote repository.                               |
| `git pull origin <branch>`  | Fetch and merge changes from remote repository.                        |
| `git fetch`                 | Download changes from remote without merging.                          |
| `git branch`                | List local branches.                                                   |
| `git branch <name>`         | Create a new branch.                                                   |
| `git checkout <branch>`     | Switch to another branch.                                              |
| `git switch <branch>`       | Modern alternative to checkout for switching branches.                 |
| `git merge <branch>`        | Merge another branch into current branch.                              |
| `git reset <file>`          | Unstage a file (remove from staging).                                  |
| `git reset --hard <commit>` | Reset repository to a commit (discard all changes).                    |
| `git rm <file>`             | Remove a file from the repository.                                     |
| `git mv <old> <new>`        | Rename or move a file.                                                 |

---

# 🔹 2. Intermediate Git Commands

| Command                       | Description                                        |
| ----------------------------- | -------------------------------------------------- |
| `git remote`                  | List configured remotes.                           |
| `git remote add origin <url>` | Link local repo to a remote repository.            |
| `git remote -v`               | Show remote URLs.                                  |
| `git show <commit>`           | Show details about a specific commit.              |
| `git stash`                   | Temporarily save changes not ready to commit.      |
| `git stash pop`               | Reapply stashed changes.                           |
| `git stash list`              | Show all stashes.                                  |
| `git rebase <branch>`         | Reapply commits on top of another base branch.     |
| `git cherry-pick <commit>`    | Apply a specific commit to the current branch.     |
| `git tag <name>`              | Create a tag (usually for releases).               |
| `git tag`                     | List all tags.                                     |
| `git revert <commit>`         | Create a new commit that undoes a specific commit. |
| `git blame <file>`            | Show who last modified each line of a file.        |
| `git shortlog`                | Summarize commits by author.                       |
| `git clean -fd`               | Remove untracked files and directories.            |
| `git archive`                 | Create a tar/zip archive of files.                 |
| `git describe`                | Describe the current commit using tags.            |

---

# 🔹 3. Advanced Git Commands (Plumbing – internal, rarely used directly)

These are the **internal commands** Git uses under the hood.

| Command              | Description                                               |
| -------------------- | --------------------------------------------------------- |
| `git cat-file`       | Show contents of repository objects.                      |
| `git hash-object`    | Compute object ID (SHA-1 hash) of a file.                 |
| `git ls-tree`        | List contents of a tree object.                           |
| `git rev-parse`      | Parse revision (commit/branch/tag names).                 |
| `git update-ref`     | Update a reference (branch, tag).                         |
| `git symbolic-ref`   | Read or change symbolic references.                       |
| `git show-ref`       | List references in the repository.                        |
| `git for-each-ref`   | Loop through refs (branches, tags).                       |
| `git reflog`         | Show history of reference changes (even deleted commits). |
| `git gc`             | Clean up unnecessary files and optimize repository.       |
| `git fsck`           | Check integrity of Git objects.                           |
| `git count-objects`  | Count number of objects in the repository.                |
| `git daemon`         | Run a Git server process.                                 |
| `git send-pack`      | Push objects to another repository.                       |
| `git receive-pack`   | Receive objects from another repository.                  |
| `git unpack-objects` | Unpack objects from a pack file.                          |
| `git pack-objects`   | Create a pack file with multiple objects.                 |

---

# 🔹 4. Collaboration Commands

| Command                                   | Description                                             |
| ----------------------------------------- | ------------------------------------------------------- |
| `git fork`                                | (Not built-in, done on GitHub/GitLab UI) – copy a repo. |
| `git pull-request`                        | (Via GitHub CLI, not raw Git) – create PR.              |
| `git submodule add <url>`                 | Add another repo as a submodule.                        |
| `git submodule update --init --recursive` | Initialize and update submodules.                       |
| `git worktree add`                        | Create a new working tree tied to the same repo.        |

---

# 🔹 5. Debugging and Fixing

| Command              | Description                                            |
| -------------------- | ------------------------------------------------------ |
| `git bisect`         | Find the commit that introduced a bug (binary search). |
| `git bisect start`   | Begin a bisect session.                                |
| `git bisect good`    | Mark a commit as good.                                 |
| `git bisect bad`     | Mark a commit as bad.                                  |
| `git bisect reset`   | End bisect.                                            |
| `git grep <pattern>` | Search for text inside tracked files.                  |
| `git verify-commit`  | Check GPG signature of commits.                        |
| `git verify-tag`     | Check GPG signature of tags.                           |
| `git notes`          | Add or show notes attached to commits.                 |

---

# 🔹 6. Full Utility Commands (from `git help -a`)

For completeness, here’s the **full set of Git commands** available:

```
add, am, archive, bisect, branch, bundle, checkout, cherry-pick, citool, 
clean, clone, commit, describe, diff, fetch, format-patch, gc, gitk, 
grep, gui, init, log, maintenance, merge, mv, notes, pull, push, range-diff, 
rebase, reset, restore, revert, rm, shortlog, show, sparse-checkout, stash, 
status, submodule, switch, tag, worktree, 

apply, blame, branch, cat-file, check-attr, check-ignore, check-mailmap, 
check-ref-format, checkout-index, commit-graph, commit-tree, config, 
count-objects, credential, credential-cache, credential-store, daemon, 
diff-files, diff-index, diff-tree, fast-export, fast-import, fsck, 
fetch-pack, filter-branch, for-each-ref, hash-object, http-backend, 
imap-send, index-pack, init-db, ls-files, ls-remote, ls-tree, mailinfo, 
mailsplit, merge-base, merge-file, merge-index, merge-one-file, merge-tree, 
mktag, mktree, multi-pack-index, name-rev, pack-objects, pack-redundant, 
pack-refs, patch-id, prune, prune-packed, read-tree, replace, rev-list, 
rev-parse, run, send-email, shell, sh-i18n, sh-setup, show-index, show-ref, 
symbolic-ref, unpack-file, unpack-objects, update-index, update-ref, 
update-server-info, upload-archive, upload-pack, var, verify-pack, 
verify-commit, verify-tag, write-tree
```

---

✅ This is **every Git command** grouped logically.
In real-world work, you’ll mostly use the **Everyday commands (section 1)** and sometimes the **Intermediate ones (section 2)**.

---

👉 Do you want me to turn this into a **cheat sheet PDF/Markdown** that you can keep offline as a quick reference?

Perfect 👍 Cleaning up old SSH keys is a good security practice. If your old PC is no longer in use, you should **remove its SSH key** from your GitHub account. Here’s how:

---

# 🔹 Removing Old SSH Keys from GitHub

## **Step 1: Log in to GitHub**

* Go to [https://github.com](https://github.com).
* Click your profile picture → **Settings**.

---

## **Step 2: Go to SSH and GPG Keys**

* In the left sidebar, click **SSH and GPG keys**.
* You’ll see a list of SSH keys linked to your account.
  Example:

  ```
  Laptop-2023    Added on Jan 10, 2023
  Work-PC        Added on May 14, 2024
  New-Laptop-2025 (your latest one)
  ```

---

## **Step 3: Identify the Old Key**

* Look at the **Title/Label** and **Date Added**.
* Find the one that belonged to your obsolete PC.

---

## **Step 4: Remove the Old Key**

* Next to the key, click the **Delete** button (trash icon).
* Confirm removal.

---

✅ Done. That key is now invalid — even if someone got hold of your old PC, they can’t push/pull to your GitHub anymore.

---

👉 Pro tip: If you use **multiple PCs** (e.g., home, work, school), you can safely have **multiple SSH keys** added under the same GitHub account.

---

Would you like me to create a **step-by-step visual guide (with mock screenshots)** for this, so it’s easier to follow?

