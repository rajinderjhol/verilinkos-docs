# Getting Started with VeriLinkOS

This guide provides a 5-minute quickstart to deploying VeriLinkOS for your AI agents.

## 1. Prerequisites
*   Python 3.12+
*   Redis (for telemetry/caching)
*   PostgreSQL (for asset/governance data)

## 2. Installation
```bash
pip install verilink-sdk
```

## 3. Quickstart: Protect Your First Agent

```python
from verilink.adapters.decorator import enforce

# Enforce governance on an agent action (SDK configures the endpoint and key)
@enforce(action_type="ai_decision")
def make_ai_decision(query: str) -> dict:
    # Your agent logic here
    return {"result": "ok"}
```

## 4. Next Steps
*   Review [API Reference](api-reference.md) for advanced Guardian configuration.

---
*Note: see [STATUS.md](../STATUS.md) for the authoritative current implementation status.*
