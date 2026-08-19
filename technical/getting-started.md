# Getting Started with VeriLinkOS

This guide provides a 5-minute quickstart to deploying VeriLinkOS for your AI agents.

## 1. Prerequisites
*   Python 3.12+
*   Redis (for telemetry/caching)
*   PostgreSQL (for asset/governance data)

## 2. Installation
```bash
pip install verilinkos
```

## 3. Quickstart: Protect Your First Agent

```python
from verilinkos.guardian import Guardian

# Initialize Guardian
guardian = Guardian(
    policy_engine_url="http://localhost:8181",
    kms_key_id="aws-kms-key-id"
)

# Enforce governance
@guardian.enforce(action_type="agent_decision")
def make_ai_decision(query: str) -> dict:
    # Your agent logic here
    return {"result": "ok"}
```

## 4. Next Steps
*   See [Deployment Guide](deployment-guide.md) for enterprise production setup.
*   Review [API Reference](api-reference.md) for advanced Guardian configuration.
