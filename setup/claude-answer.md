# Explain what make test does

`make test` runs a single command, defined in `Makefile`:

```make
test:
	pytest -q
```

Here's what that does:

- **`pytest`** runs from the repo root. There's no `pytest.ini`, `pyproject.toml`, `setup.cfg` or `conftest.py`, so it uses its default discovery: it searches the repo for `test_*.py` / `*_test.py` files. The only one right now is `tests/test_smoke.py`.
- **`-q`** (quiet) shortens the output to a row of dots and a one-line summary.

`tests/test_smoke.py` has two smoke tests:

1. **`test_openapi_document_can_be_loaded`** loads `docs/openapi.yaml` with `yaml.safe_load` and checks two things:
   - the `openapi` field starts with `"3."`, so it's an OpenAPI 3.x document;
   - the `paths` section exists and isn't empty.
2. **`test_participant_files_are_present`** checks that these files exist:
   - `.claude/settings.json`
   - `.devcontainer/devcontainer.json`
   - `CLAUDE.md`
   - `Makefile`
   - `tracker/CR-2.md`
   - `tracker/README.md`

   If any are missing, it fails with a Latvian message listing them: `Trūkst faili: …` ("Missing files: …").

**What it doesn't check:** these are sanity checks only. `make test` doesn't validate the API contract against the OpenAPI spec or test any API behaviour. For a deeper check of the contract, use `make lint-contract`, which runs `tools/lint_contract.py docs/openapi.yaml`. `make verify-setup` checks your environment: Python packages, the `claude` CLI and `setup/claude-answer.md`, then runs the smoke test file on its own.
