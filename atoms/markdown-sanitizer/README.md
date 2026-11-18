# Markdown Sanitizer

Removes active content from agent-generated markdown while keeping the
prose: script/style and other active blocks, event-handler attributes,
dangerous link schemes (`javascript:`, `data:` and kin), and raw HTML
tags. Returns the sanitized text plus a count of everything removed, by
class — what was taken out is always named, never silent.

```python
from markdown_sanitizer import evaluate, stamp

result = evaluate({"text": "<script>x</script>hi", "options": {}})
result["sanitized_text"]   # "hi"
result["counts"]           # per-class removal counts
print(stamp(result))       # human-readable rendering
```

The prose pass runs to a fixpoint: a removal can join surrounding text
into a newly-formed active tag, so the pass repeats until the segment
is stable. See the repository README for the full teardown and the
regression test (`test_nested_tag_reformation_removed_by_fixpoint`).

Tests: `python -m pytest atoms/markdown-sanitizer/tests/`
