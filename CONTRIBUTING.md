# Contributing

These atoms are extracted from a production fleet. Fixes land in the
fleet first and are refreshed into this repository, so behaviour here
never diverges from the code running in production.

If you find a bug or an edge case:

1. Open an issue with the atom name, the exact input payload, the
   output you got, and the output you expected.
2. A failing test that reproduces it is the fastest route to a fix —
   each atom's suite lives in `atoms/<atom>/tests/`.

Run the suites before proposing a change:

```bash
python -m pytest atoms/
```

Every change must keep the suites green and keep the modules
standard-library only.
