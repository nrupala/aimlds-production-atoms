# Token Estimator

Estimates token counts before you spend them: for a plain text string
or a list of chat messages, with per-model-family factors
(`gpt | claude | llama | generic`).

```python
from token_estimator import evaluate, stamp

result = evaluate({"text": "some text", "model_family": "generic"})
result = evaluate({"messages": [{"role": "user", "content": "hi"}]})
print(stamp(result))
```

Give `text` or `messages`, not both. The estimate is a planning
figure, not a tokenizer — it exists so an agent can budget a call
before making it.

Tests: `python -m pytest atoms/token-estimator/tests/`
