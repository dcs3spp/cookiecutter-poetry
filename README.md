# cookiecutter-poetry

The cookicutter template of a python package with poetry

## Usage

### Dependencies

[Poetry](https://python-poetry.org/docs/) is used as the Python build tool and dependency manager.

Installation is detailed [here](ihttps://python-poetry.org/docs/#installation).

After installation, configure poetry to create virtualenvs within each project's root folder:

```bash
poetry config virtualenvs.in-project = true
```

### Create poetry project using this cookiecutter

```bash
$ cookiecutter https://github.com/dcs3spp/cookiecutter-poetry.git
description []: my description
email []: x_smith@gmail.com
full_name []: X Smith
license []: MIT
project_name []: my_project
project_slug []: my_project
python_version []: >=3.7,<4.0
```

### Setup project

    make setup

### Run linters, autoformat, tests etc.

    make pretty lint test

### Bump new version

    make bump_major
    make bump_minor
    make bump_patch

