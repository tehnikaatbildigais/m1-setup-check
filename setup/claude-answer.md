# What `make test` does

`make test` runs one command, defined in `Makefile`:

```make
test:
	pytest -q
```

It runs pytest from the repo root. `-q` (quiet) prints less: one dot per passing test and a short summary at the end. The repo has no pytest config file (`pytest.ini`, `pyproject.toml`, `setup.cfg`, `conftest.py`), so pytest uses its default discovery. The only test file it finds is `tests/test_smoke.py`, which has two tests:

1. **`test_openapi_document_can_be_loaded`** loads `docs/openapi.yaml` (the API contract) with PyYAML. It checks that the `openapi` version starts with `3.` and that `paths` isn't empty. This only checks the basic structure. Full validation of the contract is `make lint-contract`, which runs `tools/lint_contract.py`.

2. **`test_participant_files_are_present`** checks that these setup files exist:
   - `.claude/settings.json`
   - `.devcontainer/devcontainer.json`
   - `CLAUDE.md`
   - `Makefile`
   - `tracker/CR-2.md`
   - `tracker/README.md`

   If any are missing, it fails and lists them ("Trūkst faili: …").

## `make test` vs. the related targets

- **`make verify-setup`** checks the environment: Python version, the required packages (fastapi, pydantic v2, httpx, pytest, schemathesis), the `claude` CLI, and that `setup/claude-answer.md` exists. Then it runs the same `tests/test_smoke.py`.
- **`make lint-contract`** is the one to run after changing `docs/openapi.yaml`, as `CLAUDE.md` requires.

Any new `test_*.py` files you add will be picked up by `make test` automatically.
