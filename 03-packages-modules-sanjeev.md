# 🧩 Part 2 – Deep Dive into Modules & Packages

Alright — this time I’m diving deeper into how **Python modules and packages** really work.  
I already learned how to import my own files, but now I want to understand what’s actually happening behind the hood — especially when projects get bigger.

---

## 1. What Exactly Is a Module?

Any single `.py` file is already a **module**.  
If I create `helper.py`, that file itself is a module — even if it’s just one line of code.

When I import it from `main.py` like this:

    import helper

Python looks for `helper.py` in the same directory as `main.py`.  
Once found, it executes that file and creates a **namespace** for it.  
That’s why I must call things like `helper.add()` instead of just `add()`.

So importing doesn’t copy the code — it *loads* the module and gives me access to everything through its namespace.

---

## 2. Importing in Different Ways

I can import specific parts instead of the whole module:

    from helper import add, subtract

This lets me call them directly as `add()` or `subtract()`.

There’s also a wildcard import:

    from helper import *

That brings in *everything* except names starting with `_`.  
But it’s bad practice since it can cause name clashes — I’ll avoid this in real code.

---

## 3. Handling Name Conflicts with Aliases

Sometimes, two modules might define the same function name.  
To avoid overwriting, I can use **aliases**:

    from mod1 import test as mod1_test
    from mod2 import test as mod2_test

Or rename a whole module:

    import helper as hp

Now I can call `hp.add()` instead of `helper.add()`.  
This keeps names short and avoids confusion.

---

## 4. Conditional Imports

Python lets me import modules only when I need them.  
For example:

    x = 10
    if x > 5:
        import helper
        print(helper.add(x, 2))

I can also import inside a function so it only loads when the function runs.  
This helps reduce startup time in bigger programs.

---

## 5. Checking What’s in Scope with `dir()`

Calling `dir()` shows me all the names currently available.  
Before importing, I’ll only see built-ins.  
After `import helper`, I’ll see `helper` added.  
If I use `from helper import add`, then I’ll see `add` directly instead of `helper`.

It’s a simple but powerful way to see what’s currently in my namespace.

---

## 6. Modules That Can Run Themselves

Every `.py` file can be run directly.  
If I put a `print()` inside `helper.py` and then import it, that print still executes because Python runs the file once when importing.

To prevent that, I use:

    if __name__ == "__main__":
        print("Running helper directly")

This code block only runs when I execute:

    python helper.py

and not when I import it elsewhere.  
It’s basically the standard “entry point” pattern for Python scripts.

---

## 7. Understanding Packages

As my code grows, I don’t want all `.py` files in one folder.  
That’s where **packages** come in — they’re just **folders that contain related Python modules**.

Example structure:

    database/
        mod1.py
        mod2.py

Now I can import like this:

    import database.mod1
    database.mod1.connect()

Packages are just folders — Python treats them as namespaces.  

In older Python versions, I needed an empty `__init__.py` file inside every package folder.  
Now it’s optional, but still a good idea to include one for clarity.

---

## 8. What `__init__.py` Does

When Python imports a package, it checks for an `__init__.py` file.  
If found, it runs that file **once** — like a “startup script” for the package.

    # database/__init__.py
    print("Database package loaded")

This file can also define what should be imported when someone uses `from database import *`.

---

## 9. Controlling Imports with `__all__`

When someone writes:

    from database import *

Python needs to know which modules to include.  
I can control that inside `database/__init__.py`:

    __all__ = ["mod1", "mod2"]

Now only those modules will be imported.  
It’s optional but helpful in large projects.  
The same rule works in a single file too — I can limit which functions get exported.

---

## 10. Sub-Packages and Relative Imports

Folders can contain other folders — these are **sub-packages**.

    utils/
        sub_package1/
            mod1.py
            mod2.py
        sub_package2/
            mod3.py

To import from one:

    import utils.sub_package1.mod1
    utils.sub_package1.mod1.func1()

Inside one module, I can import another in the same package using a **relative import**:

    from .mod2 import func2

Here, `.` means “current folder”.  
To go up one level, use two dots:

    from ..sub_package2.mod3 import func3

Once I thought of the dots like folder paths, everything made sense — absolute imports start from the top, relative imports start from where I am.

---

## 11. Running Modules with `python -m`

I used to get confused seeing `python -m`.  
Now I get it — it tells Python to **run a module as part of its package**, not as a loose file.

Example:

    python -m database.mod1

This runs `mod1.py` but keeps its package imports working.  
Inside, `__name__` becomes `"__main__"`, so the `if __name__ == "__main__":` block executes normally.

So the simple rule is:

- `python file.py` → run as a standalone script  
- `python -m package.module` → run as part of its package (so imports don’t break)

---

## 12. Running a Package Directly

A package can be run directly too — it just needs a `__main__.py` file.  
That’s the package’s “entry point.”

Example structure:

    database/
        __init__.py
        __main__.py
        mod1.py
        mod2.py

Example content of `__main__.py`:

    from database.mod1 import connect
    from database.mod2 import disconnect

    if __name__ == "__main__":
        print("Running database package")
        connect()
        disconnect()

Then I can run it from the project root:

    python -m database

and Python will execute `database/__main__.py`.  

Sub-packages can work the same way — if `utils/sub_package1` has a `__main__.py`, I can run:

    python -m utils.sub_package1

to execute it.

If I only want a specific module:

    python -m utils.sub_package1.mod1

That runs `mod1.py` while still respecting package imports.

---

## 💡 Takeaway

After this deep dive, I finally have a mental map of how modules and packages behave.

- A **module** is any `.py` file.  
- A **package** is just a folder grouping related modules.  
- `__name__ == "__main__"` prevents unwanted code from running on import.  
- `__init__.py` runs once when the package loads.  
- `__all__` controls what wildcard imports bring in.  
- Dots in imports (`.`, `..`) act like folder paths.  
- `python -m` runs modules or packages in the correct, package-aware way.

Now the entire module system finally feels clear and logical.