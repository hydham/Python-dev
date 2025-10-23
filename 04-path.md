# Understanding Python Interpreter, PATH, and Aliases (Mac)

When I started switching between different Python versions, I used to get weird errors — like `python` showing version 2.7 even though I’d already installed Python 3. Or I’d `pip install` a package and the `import` would still fail. For a while I blamed Python. Then I realized the real issue was **which Python my system was using** — the interpreter that actually runs when I type `python`.

---

## Figuring out which Python I’m really using

So first, I just check versions:

    python --version
    python3 --version

On my Mac, `python` pointed to the old, preinstalled 2.7. `python3` showed the real Python 3.x I installed.

I kept wondering: *how does my Mac decide which “python” it runs when I type the command?*  
Answer: the **PATH environment variable**.

---

## How PATH decides what gets run

The shell searches a list of folders (left to right) whenever I type a command. To see the list:

    echo $PATH

It prints something like:

    /usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin

Each folder is separated by a colon (`:`). When I type `python`, macOS checks those folders in order for a file named `python`. The first match wins. That’s why **order matters** and why multiple Pythons can coexist but only one runs.

---

## Finding where a command actually lives

To see *which file* is being run:

    which python3

This prints the path to the binary (e.g., `/usr/local/bin/python3`).  
But if I’ve set an alias, `which` won’t tell me about the alias. For that, I use:

    type python3
    type python

`type` works for both binaries and aliases. If `python` is an alias, I’ll see something like:

    python is aliased to `python3`

That’s why I prefer `type` when I’m debugging.

---

## When aliases helped… and when they hurt

I got tired of typing `python3`, so I created aliases:

    alias python="python3"
    alias pip="pip3"

Nice and convenient — until I activated a virtual environment and my global alias **overrode** the environment’s `python`. Now I keep this rule:

> Use aliases for convenience, but if something seems off (especially in virtual environments), temporarily remove them:
>
>     unalias python
>     unalias pip

After I finish debugging, I can re-add them (or just open a new shell).

---

## Making changes permanent

Edits in a terminal session vanish when I close the window. To keep PATH updates and aliases, I put them in my shell profile (bash example):

    nano ~/.bash_profile

At the bottom I add:

    # Prefer this Python 3 bin directory
    PATH="/Library/Frameworks/Python.framework/Versions/3.7/bin:${PATH}"
    export PATH

    # Convenience aliases
    alias python="python3"
    alias pip="pip3"

Then I restart the terminal (or `source ~/.bash_profile`) and the changes stick.

---

## How I confirm the exact interpreter running my code

Even if the version looks right, I like to **prove** which interpreter is running:

    python -c "import sys; print(sys.executable)"

or inside Python:

    import sys
    print(sys.executable)

That prints the full path to the currently running Python binary. It’s the most reliable truth-source, especially with multiple Pythons or environments around.

---

## When pip installs but imports still fail

I’ve installed a package with pip and then Python said `ModuleNotFoundError`. Usually this means pip installed into a **different** Python than the one I’m running.

I check where pip put it:

    pip show requests

Look at **Location**. That path should line up with the `site-packages` under the directory shown by `sys.executable`. If they don’t match, I used the wrong pip. Often the fix is simply:

    pip3 install requests

(Or, better: use the pip that belongs to the interpreter you’re running, e.g. the environment’s `pip`.)

---

## Editors using the “wrong” Python

My terminal had Python 3.7, but Sublime Text kept running Python 2.7 — f-strings failed. That’s because editors often pick their own interpreter and **don’t** use the terminal’s PATH.

In Sublime, I pointed a build system to the exact Python I wanted:

    {
      "cmd": ["/Library/Frameworks/Python.framework/Versions/3.7/bin/python3", "-u", "$file"]
    }

After setting that, the code ran with 3.7. The lesson: **tell your editor which interpreter to use**.

---

## Virtual environments (why they “just work”)

Virtual environments are little isolated Python worlds. Each has its **own** `python` and `pip`. When I activate one, the environment’s `bin/` folder is placed at the **front** of PATH. That’s why `which python` suddenly points inside the environment:

    /Users/me/anaconda/envs/flaskblog/bin/python

To verify:

    which python
    python -c "import sys; print(sys.executable)"

Deactivating the environment restores my old PATH. No manual PATH editing needed — activation scripts do it for me.

---

## My quick debug checklist

When something feels “off” — wrong version, imports failing, editor acting weird — I check three things:

    which python
    echo $PATH
    python -c "import sys; print(sys.executable)"

If pip is suspect:

    which pip
    pip show <package>

And if I used aliases:

    type python
    type pip
    unalias python  # (temporarily, to test)
    unalias pip

Nine times out of ten, these reveal exactly why my machine used a different Python than I expected.

---