# Changelog

## 1.0.0 — 2026-11-18

Initial public release. The three production utility atoms — Markdown
Sanitizer, Token Estimator, JSON Schema Enforcer — extracted
byte-identical from the AIMLDS atom-services production tree, with
their full production test suites, under the Apache 2.0 license.

Includes the Markdown Sanitizer fixpoint fix and its regression test
(see the README teardown): the prose pass repeats until stable, so a
removal can no longer re-form an active block that survives the pass.
