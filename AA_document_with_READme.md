
## How to Create Complete Source Code Documentation for Your Project

### 1. Ensure the `tree` Command Is Installed (Linux)

On your Linux system, make sure the `tree` utility is installed:

```bash
sudo apt-get install tree
```

### 2. Generate the Project File Structure

Navigate to the root directory of your project and run the following command:

```bash
tree -I 'node_modules|venv|__pycache__|.git' > PROJECT_STRUCTURE.txt
```

### 3. Command Explanation

* **`tree`** → Displays the project’s directory structure in a hierarchical format.
* **`-I`** → Excludes unnecessary folders (you can modify this list to include folders like `dist`, `build`, or `target` if needed).
* **`>`** → Redirects the output to a file instead of printing it to the terminal.

### 4. Review the Generated Structure

Open the generated `PROJECT_STRUCTURE.txt` file in VS Code or any text editor to review your project’s structure.

### 5. Create the Documentation

Use the project structure file as a checklist:

* Go through each file and folder one by one.
* Copy the contents of each source file into your documentation file.
* Add clear and meaningful comments explaining:

  * The purpose of each file
  * Key functions, components, or classes
  * Important logic or configurations

--- 

* **How to use embedme:**
1. Install it via npm: `npm install embedme`.
2. In your README, write: `[//]: # (src/main.py)`
3. Run `npx embedme README.md`.


## Source Files

- [file1.py](path/to/file1.py)
- [file2.py](path/to/file2.py)
