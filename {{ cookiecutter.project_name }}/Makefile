.PHONY: setup  test lint pretty bump_major bump_minor bump_patch

BIN = .venv/bin/
CODE = {{ cookiecutter.project_slug }}

EXECUTABLES = poetry
K := $(foreach exec,$(EXECUTABLES),\
        $(if $(shell which $(exec)),some string,$(error "No $(exec) in PATH")))

setup:
	poetry install

test:
	$(BIN)pytest

lint:
	$(BIN)flake8 $(CODE) tests
	$(BIN)black --check $(CODE) tests
	$(BIN)pytest --dead-fixtures --dup-fixtures

pretty:
	$(BIN)isort $(CODE) tests
	$(BIN)black $(CODE) tests

bump_major:
	$(BIN)bumpversion major

bump_minor:
	$(BIN)bumpversion minor

bump_patch:
	$(BIN)bumpversion patch

