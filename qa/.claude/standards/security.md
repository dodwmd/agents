# Security Review Standards

## What to Check in Every PR

Security issues in a diff that must be flagged as **Critical**:

### Hardcoded Secrets
```python
# ❌ Flag immediately
API_KEY = "sk-abc123..."
password = "hunter2"
token = "ghp_..."
```
Any string that looks like a key, token, password, or credential — even in test files.

### Command Injection
```python
# ❌ Flag as Critical
subprocess.run(f"git {user_input}", shell=True)
os.system(f"process {filename}")

# ✅ Safe
subprocess.run(["git", user_input], shell=False)
```

### Path Traversal
```python
# ❌ Flag as Critical
open(f"/uploads/{request.args['file']}")

# ✅ Safe — path is validated against a base directory
```

### SQL / NoSQL Injection
```python
# ❌ Flag as Critical
cursor.execute(f"SELECT * FROM users WHERE id = {user_id}")

# ✅ Safe
cursor.execute("SELECT * FROM users WHERE id = ?", (user_id,))
```

### Error Leakage
```python
# ❌ Flag as Important — exposes internals
except Exception as e:
    return {"error": str(e), "traceback": traceback.format_exc()}

# ✅ Log the detail, return a safe message to the caller
```

### Silent Failure Patterns
```python
# ❌ Flag as Important — hides errors
try:
    do_thing()
except Exception:
    pass  # or just: logger.error("Something went wrong")

# ✅ Fail loudly or re-raise with context
```

## Severity Mapping

| Finding | Severity |
|---|---|
| Hardcoded secret | Critical |
| Command injection | Critical |
| SQL injection | Critical |
| Path traversal | Critical |
| Silent exception catch | Important |
| Error message leaks stack trace | Important |
| Missing input validation at API boundary | Important |
| Dependency with known CVE | Important |
| Logging sensitive data | Important |
