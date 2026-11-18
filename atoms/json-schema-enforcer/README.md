# JSON Schema Enforcer

Validates a JSON payload against a schema and reports the whole
picture: whether the payload is valid, every violation found, how
many checks were performed, and any schema keywords the enforcer does
not support — named in `unsupported_keywords`, never silently skipped.

```python
from json_schema_enforcer import evaluate, stamp

result = evaluate({"payload": {"a": 1}, "schema": {"type": "object"}})
result["valid"]                # True / False
result["violations"]           # every violation, with its path
result["unsupported_keywords"] # [] when the schema is fully covered
print(stamp(result))
```

Tests: `python -m pytest atoms/json-schema-enforcer/tests/`
