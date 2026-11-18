# aimlds-production-atoms

**Governed agent tools, from production code.**

This repository is the exact production code of three utility atoms that
run live in the AIMLDS atom fleet — extracted, unmodified, and released
under the Apache 2.0 license so agent builders can run them, read them,
and build on them. Not demos, not samples: the same modules, with the
same test suites that gate them in production.

Each atom is a single Python 3 module, standard library only, exposing
one function:

```python
evaluate(payload: dict) -> dict   # the work
stamp(result: dict) -> str          # a human-readable rendering of the result
```

## The atoms

| Atom | What it does | Contract |
| --- | --- | --- |
| [Markdown Sanitizer](atoms/markdown-sanitizer/) | Removes active content (script/style blocks, event handlers, dangerous link schemes, raw HTML) from agent-generated markdown while keeping the prose. | `{"text": str, "options": {...}}` → sanitized text + per-class removal counts |
| [Token Estimator](atoms/token-estimator/) | Estimates token counts for text or chat messages, per model family, before you spend them. | `{"text": str}` or `{"messages": [...]}` (+ optional `"model_family"`) → estimate |
| [JSON Schema Enforcer](atoms/json-schema-enforcer/) | Validates a JSON payload against a schema and reports every violation, every check performed, and any schema keywords it does not support — named, never silently skipped. | `{"payload": <any JSON>, "schema": <object>}` → validity, violations, checks, unsupported keywords |

## Run it

```bash
python -m pytest atoms/        # the full production test suites
```

No dependencies beyond Python 3 and pytest. Each atom also runs
standalone: `python atoms/markdown-sanitizer/src/markdown_sanitizer.py`
reads a JSON payload and prints the result with its stamp.

## The fixpoint fix — a worked teardown

The Markdown Sanitizer's history carries the lesson this repository
exists to teach. A sanitizer that removes dangerous markup in a single
pass has a hole: **a removal can create the thing it removes.** Take the
input

```
<scri<script></script>pt>alert(1)</scri<script></script>pt>
```

A single pass removes the two inner `<script></script>` pairs — and the
fragments they leave behind join into two whole, new, active `<script>`
blocks. The original code then handed those re-formed blocks to the
plain tag-stripper, which removed the tags and *spilled `alert(1)` into
the output as text*. The dangerous content survived precisely because
the sanitizer had done its job once.

Static analysis (CodeQL, run during the fleet's OpenSSF hardening pass,
2026-11-17) flagged the non-idempotence. The fix is in
`atoms/markdown-sanitizer/src/markdown_sanitizer.py`: the prose pass now
repeats **to a fixpoint** — it re-runs until the segment is stable (at
most 8 iterations). Benign input is idempotent, so the second pass
changes nothing and ordinary results are byte-for-byte the single-pass
results; hostile input is processed until nothing active can re-form.

The regression test that pins it
(`test_nested_tag_reformation_removed_by_fixpoint`) asserts the exact
contract: the nested input above sanitizes to the empty string, with
counts `active_blocks_removed == 3` (two inner pairs in pass one, one
re-formed block in pass two) and `html_tags_removed == 0` — proof the
re-formed block was removed *as a block*, not spilled as text.

That is the standard this code is kept to: the edge case is named, the
fix is structural, and the test counts the mechanism, not just the
output.

## Provenance and status

Extracted 2026-11-18 from the production atom-services tree at the
request of the Mercer revenue program, and verified byte-identical to
the deployed modules at extraction. The hosted fleet these atoms serve
in continues to run the same code; fixes land in the fleet first and
this repository is refreshed from it.

Security reports: see [SECURITY.md](SECURITY.md). Contributions:
[CONTRIBUTING.md](CONTRIBUTING.md). Changes: [CHANGELOG.md](CHANGELOG.md).

Copyright 2026 2351485 ALBERTA INC. (AIMLDS). Licensed under the
Apache License, Version 2.0 — see [LICENSE](LICENSE).

## State on record & economics

STATE ON RECORD: RELEASED WITH DISCLAIMER. Gate verdict and Guardian Mesh disposition are owed. This code is not gate-certified.
ECONOMICS & WIRING: To compare the unit economics of these governed tool calls against raw execution, view the pricing baseline: https://aimlds.org/pricing. For a complete audit of your stack's economics, initiate the $99 Sanity Check via the pricing page.
