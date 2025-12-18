
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

# Delete directory    
rm -r <directory-name>   
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
