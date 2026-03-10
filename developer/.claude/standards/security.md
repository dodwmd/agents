# Security Standards

## Non-Negotiables

- **No hardcoded secrets** — no API keys, passwords, tokens, or private keys in code
- **Use environment variables** — `os.getenv("API_KEY")` not `API_KEY = "sk-..."`
- **`.env` files are always in `.gitignore`** — never commit them
- **Validate all external input** — user input, API responses, file paths

## Common Vulnerabilities to Avoid

### Command Injection
```python
# ❌ Dangerous
subprocess.run(f"git log {user_input}", shell=True)

# ✅ Safe
subprocess.run(["git", "log", user_input], shell=False)
```

### Path Traversal
```python
# ❌ Dangerous
open(f"/data/{filename}")  # filename could be "../../etc/passwd"

# ✅ Safe
from pathlib import Path
base = Path("/data").resolve()
target = (base / filename).resolve()
if not target.is_relative_to(base):
    raise ValueError("Invalid path")
```

### Error Leakage
```python
# ❌ Leaks credentials
except Exception as e:
    print(f"Failed with key: {api_key}, error: {e}")

# ✅ Safe
except Exception:
    raise ValueError("Authentication failed")
```

### SQL Injection (if applicable)
```python
# ❌ Dangerous
cursor.execute(f"SELECT * FROM users WHERE id = {user_id}")

# ✅ Safe
cursor.execute("SELECT * FROM users WHERE id = ?", (user_id,))
```

## Pre-Commit Checklist

Before opening a PR, verify:
- [ ] No API keys, tokens, or passwords in the diff
- [ ] No `shell=True` in subprocess calls (unless justified)
- [ ] All file paths from user input are validated
- [ ] Error messages don't expose internal state or credentials
- [ ] No `eval()` or `exec()` on user-controlled input
- [ ] Dependencies checked for known vulnerabilities (`pip-audit` / `npm audit`)

## Dependency Rules

- Prefer standard library over third-party packages
- Pin exact versions for reproducibility
- No packages with known critical CVEs
- Check licenses — GPL-licensed packages may have commercial implications
