```md
# Importing Modules & Python Standard Library

Alright — today I’m learning how to **import modules** in Python.  
This means learning how to bring in **code from other Python files** (including ones I write myself) and also **from the standard library** — which is Python’s huge built-in toolkit.

---

## 1. Importing My Own Module

I created a file called `my_module.py`.  
Inside it, I added:

```python
print("Imported my_module!")  # Just so I know when this file runs
test = "Test String"

def find_index(to_search, target):
    """Finds the index of a value in a sequence"""
    for i, value in enumerate(to_search):
        if value == target:
            return i
    return -1
```

So basically:
- The print statement will help me see when this module is actually executed.  
- The variable `test` just holds a string.  
- The function `find_index()` searches for a value inside a list and returns its index. If it can’t find it, it returns `-1`.

Now I want to use this function from **another file**, `intro.py`.

In `intro.py`, I have:
```python
courses = ['History', 'Math', 'Physics', 'CompSci']
```

To import my module, I just write this at the top:
```python
import my_module
```

Since `my_module.py` is in the **same folder**, Python finds it automatically.

When I run `intro.py`, I notice something interesting:
```
Imported my_module!
```
That’s the print statement from `my_module.py`.  
It proves that **when a module is imported, all of its code runs once** (that’s how Python creates the functions and variables in memory).

Now I can use the function:
```python
index = my_module.find_index(courses, 'Math')
print(index)
```
Output:
```
1
```
Perfect — the index of “Math” is 1.

---

## 2. Using an Alias (Shorter Names)

Typing `my_module.find_index` every time could get annoying.  
So Python lets me **rename** the module when importing it:
```python
import my_module as mm
```
Now I can do:
```python
index = mm.find_index(courses, 'Math')
```

This alias feature is commonly used with big libraries:
```python
import numpy as np
import pandas as pd
```

---

## 3. Importing Specific Things from a Module

Instead of importing the entire module, I can import just what I need:
```python
from my_module import find_index
```

Now I can call:
```python
find_index(courses, 'Math')
```
directly — no `my_module.` prefix needed.

But there’s a catch:  
This only imports **that specific function** — not everything else from the file.  
So `test` (the variable) won’t exist unless I also import it:
```python
from my_module import find_index, test
```

Then I can use both:
```python
print(find_index(courses, 'History'))
print(test)
```

If I want to rename one of these imports:
```python
from my_module import find_index as fi
```
Now I can use `fi()` instead of typing the full name.  
Though the teacher warns not to overdo this — it’s only worth it when the name still makes sense.

---

## 4. Importing Everything (and Why It’s a Bad Idea)

Python technically allows this:
```python
from my_module import *
```
That imports **everything** in the file — functions, variables, etc.

But the problem is, I lose track of where things came from.  
If something breaks, I can’t easily tell if `find_index` was mine or from some other library.  
So this method is considered **bad practice**.

I’ll stick to importing only what I need explicitly.

---

## 5. How Python Finds Modules (Behind the Scenes)

When I write:
```python
import my_module
```
Python somehow knows **where to look** for that file — even though I didn’t give it a file path.

It searches through a list called:
```python
sys.path
```
So if I do:
```python
import sys
print(sys.path)
```
I’ll see a list of directories.

The order usually looks like this:
1. The current directory (where I’m running my script)
2. Any directories listed in the **PYTHONPATH environment variable**
3. The **Standard Library directories**
4. The **site-packages** directory (where third-party packages live)

That’s how Python finds everything.

---

## 6. When the Module Isn’t in the Same Directory

If I move `my_module.py` somewhere else (say into a “my_modules” folder on my Desktop),  
then Python will no longer find it automatically.

When I try to run:
```python
import my_module
```
I’ll get:
```
ModuleNotFoundError: No module named 'my_module'
```

### Option 1 – Append the Path Manually

I can add that directory to `sys.path` at runtime:
```python
import sys
sys.path.append('/Users/dy/Desktop/my_modules')

import my_module
```
Now it works again — but this approach is clunky and not maintainable.

---

### Option 2 – Add It to the PYTHONPATH Environment Variable

This is a more permanent solution.

#### On macOS:
1. Open Terminal.
2. Edit your `.bash_profile`:
   ```bash
   nano ~/.bash_profile
   ```
3. Add this line (with your own folder path):
   ```bash
   export PYTHONPATH="/Users/dy/Desktop/my_modules"
   ```
4. Save and restart your terminal.
5. Now try:
   ```python
   import my_module
   ```
   It works!  
   If I print `sys.path`, I’ll see my folder in the list now.

#### On Windows:
1. Go to **Start → Computer → Properties → Advanced system settings**.
2. Click **Environment Variables**.
3. Add a **new variable**:
   - Name: `PYTHONPATH`
   - Value: `C:\Users\YourName\Desktop\my_modules`
4. Save and restart your Command Prompt.
5. Type:
   ```python
   import my_module
   ```
   Works the same way.

> 🧠 Extra note:  
> Some editors (like PyCharm or VS Code) manage environment variables separately inside the project.  
> So I might need to configure PYTHONPATH inside the editor’s settings too.

---

## 7. Python Standard Library

After checking the current directory and PYTHONPATH,  
Python searches the **standard library directories**.

The **standard library** is Python’s massive built-in toolkit — ready to use without installing anything.  
It includes modules for math, random numbers, dates, files, and even fun stuff.

These modules are **super reliable** and maintained by Python’s core developers.

---

### Example 1: The `random` Module
```python
import random

courses = ['History', 'Math', 'Physics', 'CompSci']
random_course = random.choice(courses)
print(random_course)
```
Each time I run it, I’ll get a random item from the list.

---

### Example 2: The `math` Module
```python
import math

rads = math.radians(90)
print(rads)  # converts degrees to radians

print(math.sin(rads))  # prints 1.0 (the sine of 90 degrees)
```

---

### Example 3: The `datetime` and `calendar` Modules
```python
import datetime
import calendar

today = datetime.date.today()
print(today)  # shows today’s date

print(calendar.isleap(2017))  # False
print(calendar.isleap(2020))  # True
```

---

### Example 4: The `os` Module
The `os` module lets me interact with my computer’s file system and environment.

```python
import os

print(os.getcwd())  # current working directory
```
This shows where my Python script is running from.  
`os` can also create, delete, and rename files — super powerful.

---

## 8. Looking Inside the Standard Library

All modules are just **Python files** themselves.  
I can check where they live by printing:
```python
import os
print(os.__file__)
```
That gives me the path to the file on my system.

If I open that folder, I’ll see all the standard library modules — written in plain Python!

For example, there’s a module called `antigravity`.  
It’s kind of a joke in the Python community.

If I run:
```python
import antigravity
```
it opens a web comic in my browser!  
When I peek into that file, it’s just importing the `webbrowser` module and opening a URL.  
So even the “fun” parts of Python are written like normal modules.

---

## 9. Summary (My Personal Takeaways)

- A **module** is just a `.py` file that contains reusable code.  
- I can import my own modules if they’re in the same directory or visible in `sys.path`.  
- The `import` statement runs the module once and makes its contents available.  
- I can use:
  - `import module_name`  
  - `import module_name as alias`  
  - `from module_name import function_name`  
  - or (not recommended) `from module_name import *`
- `sys.path` is the list of places Python searches for modules.  
- I can permanently add locations to this list using the **PYTHONPATH** environment variable.  
- The **standard library** gives me tons of pre-built modules (`math`, `random`, `datetime`, `os`, `calendar`, etc.).
- Every module is just a normal Python file — I can open them to learn from real, optimized code.

---

That wraps up everything from this lecture.  
Next step for me: start exploring some of these standard library modules one by one and try to use them in small scripts — like writing a mini app that uses `datetime` and `os` together.
```