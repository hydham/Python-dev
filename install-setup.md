```md
# Python Basics – Getting Started

## 1. Introduction  
This series teaches the **foundations of Python programming** — perfect if you’re new to coding or coming from another language.  

You’ll learn:
- How to install and run Python  
- Core data types (numbers, strings, lists, etc.)  
- Conditionals, loops, and iterations  
- Functions and modules  
- Using the Python Standard Library  

By the end, you’ll understand how to write, run, and organize Python programs confidently.

---

## 2. Installing Python

### For macOS
- macOS includes an older version (usually **Python 2.7**).  
- Check by running:
  ```bash
  python --version
  ```
  It will likely show `Python 2.7.x`.  

To install Python 3:
1. Visit [python.org](https://www.python.org).  
2. Go to **Downloads → macOS**.  
3. Download the latest **Python 3 version** and install it.  
4. Verify installation:
   ```bash
   python3 --version
   ```

To make typing easier, you can create an alias so that `python` points to Python 3:
```bash
nano ~/.bash_profile
alias python='python3'
```
Save and restart the terminal.

---

### For Windows
1. Download from [python.org → Downloads → Windows](https://www.python.org).  
2. Run the installer.  
3. ✅ **Important:** Check “Add Python to PATH” before clicking *Install*.  
4. Once complete, open Command Prompt and verify:
   ```bash
   python --version
   ```

Now both macOS and Windows users are ready to start coding.

---

## 3. Running Python

### Interactive Prompt (REPL)
Open your terminal or command prompt and type:
```bash
python
```
You’ll enter the interactive Python shell where you can run commands line by line.

Example:
```python
print("Hello, world!")
x = 10
print(x)
```

Exit using:
```python
exit()
```

---

### Running a Python File
1. Create a new file named `intro.py`.  
2. Add this code:
   ```python
   print("Hello, world!")
   ```
3. Run it from the terminal:
   ```bash
   python intro.py
   ```

Output:
```
Hello, world!
```

---

## 4. Comments in Python
Comments are ignored by Python but useful for explaining your code.  
They start with `#`.

Example:
```python
# This line prints a welcome message
print("Hello, world!")
```

---

## 5. Choosing an Editor

You can write Python code in any plain text editor.

| Editor | Type | Notes |
|--------|------|-------|
| **VS Code** | Text Editor | Great for beginners; built-in terminal |
| **Sublime Text** | Text Editor | Lightweight and fast |
| **PyCharm** | IDE | Feature-rich for large projects |

> 💡 For this course, the instructor uses **Sublime Text**, but any editor works fine.

---

## 6. Summary
- Installed Python 3 on macOS or Windows  
- Learned how to check versions and set an alias  
- Practiced running Python interactively and through a `.py` file  
- Learned how to write comments  
- Got familiar with popular editors  

**Next up → Variables and Data Types (starting with Strings)**
```