# Hiding Secrets Using Environment Variables

When I first started coding, I used to hardcode passwords and API keys right into my Python scripts, like:

    DB_USER = "admin"
    DB_PASS = "SuperSecret123"

It worked on my laptop… until I pushed the code to GitHub or shared it with teammates. Those secrets were now exposed to anyone with repo access. The fix is simple and standard: **store secrets in environment variables**, not in code.

---

## Why Environment Variables Are Better

Environment variables let me:

- Keep code public while keeping secrets private.
- Give each developer (and each environment: dev/stage/prod) their own credentials.
- Avoid committing passwords, tokens, and database URLs to version control.

This is essential for real-world apps (Flask/Django, CLI tools, data pipelines) where DB URLs, API keys, and app secrets must be confidential.

---

## Step 1: Set Environment Variables (Mac/Linux)

I set variables in my shell profile so they’re available to my terminal sessions.

    # 1) Go to home folder
    cd ~

    # 2) Open your shell profile (bash example; zsh users: ~/.zshrc)
    nano ~/.bash_profile

    # 3) Add secrets (no spaces around =)
    export DB_USER="admin"
    export DB_PASS="SuperSecret123"

    # 4) Save + reload profile
    source ~/.bash_profile

Tip: Quotes are optional except when values contain spaces or special characters.

---

## Step 2: Read Them in Python

Use the standard library `os`:

    import os

    user = os.getenv("DB_USER")
    password = os.getenv("DB_PASS")

    print("Database user:", user)

No secrets in code, yet the app runs with the right credentials.

---

## Step 3: Teams, Repos, CI/CD, and Containers

- Teammates define their own variables locally; we all run the **same code**, with different private values.
- CI/CD systems (GitHub Actions, GitLab CI, Jenkins) inject secrets securely via their UI/secret store.
- Containers accept env vars at runtime:

    docker run \
      -e DB_USER=admin \
      -e DB_PASS=secret \
      myapp:latest

Inside the container, the same Python code (`os.getenv`) reads the variables—no hardcoding required.

---

## Step 4: Project-Specific `.env` Files

When a project has many variables, I keep them together in a `.env` file in the repo root (but **never** commit it):

    DB_USER=admin
    DB_PASS=SuperSecret123
    DEBUG=True

Two common ways to load `.env`:

1) **python-dotenv** (explicit load)

    pip install python-dotenv

    from dotenv import load_dotenv
    import os

    load_dotenv()  # Loads .env into process env
    print(os.getenv("DB_USER"))

2) **pipenv** (auto-loads `.env` on `pipenv shell` / `pipenv run`)
- Just create `.env` in the project root; pipenv takes care of loading.

Add `.env` to `.gitignore` so it never ends up in version control:

    echo ".env" >> .gitignore

---

## Step 5: Real-World Example — API Keys

Say I’m building a weather CLI that calls OpenWeather. Instead of hardcoding:

    API_KEY = "a6e8f9bd1234567890abcdef"

I set it once:

    export API_KEY="a6e8f9bd1234567890abcdef"

And read it at runtime:

    import os
    import requests

    api_key = os.getenv("API_KEY")
    url = f"https://api.openweathermap.org/data/2.5/weather?q=Paris&appid={api_key}"
    resp = requests.get(url, timeout=10)
    resp.raise_for_status()
    print(resp.json())

If I share the code, my key remains private.

---

## Step 6: If I Accidentally Commit a Secret

Deleting the line isn’t enough—Git history still contains the secret. The correct response is:

1) **Revoke** the leaked key in the provider’s dashboard.  
2) **Generate** a new key.  
3) **Rotate** it wherever it’s used (local, CI, prod).  
4) Add safeguards to prevent repeats:
   - `.env` in `.gitignore`
   - Pre-commit checks / scanners
   - Separate config from code

---

## Quick Recap

- **Never** hardcode secrets in code.  
- Use **environment variables** (global shell profile or project-level `.env`).  
- Load in Python via `os.getenv(...)`.  
- Keep `.env` **out of Git** (use `.gitignore`).  
- CI/CD and containers pass secrets safely at runtime.  
- If a secret leaks, **revoke and rotate**.

This pattern keeps my code public, my secrets private, and my deployments consistent across machines.