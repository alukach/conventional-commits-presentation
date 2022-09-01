<!-- .slide: data-background-image="imgs/a drawing of a putting python in a package.png" data-background-opacity=".5" -->

Lessons learned from

building & sharing
<br />
a ✨Python module✨ <!-- .element: class="r-fit-text" -->

---

Versioning<!-- .element: class="r-fit-text" -->

--

Where does it go?<!-- .element: class="r-fit-text" -->

--

maybe...

`setup.py`

```py [|7]
#!/usr/bin/env python

from distutils.core import setup

setup(
    name='Distutils',
    version='1.2.3',
    description='Python Distribution Utilities',
    author='Greg Ward',
    author_email='gward@python.net',
    url='https://www.python.org/sigs/distutils-sig/',
    packages=['distutils', 'distutils.command'],
)
```

--

or...

`setup.cfg`

```cfg [|3]
[metadata]
name = Distutils
version = 1.2.3
author = Greg Ward
description = Python Distribution Utilities
url = https://www.python.org/sigs/distutils-sig/
```

--

or...

`pyproject.toml`

```toml [|3]
[tool.poetry]
name = "Distutils"
version = "1.2.3"
authors = ["Greg Ward"]
description = "Python Distribution Utilities"
url = "https://www.python.org/sigs/distutils-sig/"
```

--

AND

`src/__init__.py`

```py
__version__ = "1.2.3"
```

<small>(described in [PEP 8](https://peps.python.org/pep-0008/#module-level-dunder-names))</small>

--

# Goal

Keep

`setup.py` _or_ `setup.cfg` _or_ `pyproject.toml`

in sync with

`__init__.py:__version__`

--

How?<!-- .element: class="r-fit-text" -->

---

How to version?<!-- .element: class="r-fit-text" -->

--

<!-- .slide: data-background-iframe="https://semver.org" data-background-interactive="" -->

Semantic Versioning<!-- .element: class="r-fit-text" -->

--

<!-- .slide: data-background-iframe="https://www.conventionalcommits.org/en/v1.0.0/" data-background-interactive="" -->

Conventional Commits<!-- .element: class="r-fit-text" -->

--

How do Conventional Commits relate to Semantic Versioning?

--

`fix` type commits should be translated to `PATCH` releases.

<div class="fragment" data-fragment-index="1">

`1.2.3`

</div>

<div class="fragment fade-in" data-fragment-index="2">

<span>+</span>

```text
fix: close db connection after request
```

</div>

<div class="fragment" data-fragment-index="3">

<span>=</span>

`1.2.4` <!-- .element: class="fragment fade-in" data-fragment-index="3" -->

</div>

--

`feat` type commits should be translated to `MINOR` releases.

<div class="fragment" data-fragment-index="1">

`1.2.3`

</div>

<div class="fragment fade-in" data-fragment-index="2">

<span>+</span>

```text
feat: add summary endpoint
```

</div>

<div class="fragment" data-fragment-index="3">

<span>=</span>

`1.3.0` <!-- .element: class="fragment fade-in" data-fragment-index="3" -->

</div>

--

Commits with `BREAKING CHANGE` in the commits, regardless of type, should be translated to `MAJOR` releases.

<div class="fragment" data-fragment-index="1">

`1.2.3`

</div>

<div class="fragment fade-in" data-fragment-index="2">

<span>+</span>

```text
feat: remove legacy endpoint

BREAKING CHANGE: no longer supporting thing
```

</div>

<div class="fragment" data-fragment-index="3">

<span>=</span>

`2.0.0` <!-- .element: class="fragment fade-in" data-fragment-index="3" -->

</div>

--

Commits with `!` in the commits, regardless of type, should be translated to `MAJOR` releases.

<div class="fragment" data-fragment-index="1">

`1.2.3`

</div>

<div class="fragment fade-in" data-fragment-index="2">

<span>+</span>

```text
feat!: remove legacy endpoint
```

</div>

<div class="fragment" data-fragment-index="3">

<span>=</span>

`2.0.0` <!-- .element: class="fragment fade-in" data-fragment-index="3" -->

</div>

---

Poetry

🚨🌶🚨<!-- .element: class="fragment fade-in" data-fragment-index="1" -->

(spicey opinions ahead) <!-- .element: class="fragment fade-in" data-fragment-index="1" -->

--

Publishes to PyPi<!-- .element: class="r-fit-text" -->

--

One configuration to rule them all

![](imgs/slaps-roof-of-car.png)

--

Linters/Formatters

`pyproject.toml`

```toml[42-70]

[build-system]
build-backend = "poetry.masonry.api"
requires = ["poetry>=0.12"]

[tool.poetry]
authors = ["Anthony Lukach <anthonylukach@gmail.com>"]
description = "Replace Pydantic's builtin Secret Support with a configuration provider that loads parameters from AWS Systems Manager Parameter Store."
homepage = ""
license = "MIT"
maintainers = ["Anthony Lukach <anthonylukach@gmail.com>"]
name = "pydantic-ssm-settings"
readme = "README.md"
repository = "https://github.com/developmentseed/pydantic-ssm-settings/"
version = "0.2.3"

[tool.poetry.dependencies]
"boto3" = "^1.21.45"
pydantic = "^1.6.2"
python = "^3.7"

[tool.poetry.dev-dependencies]
black = "^22.3.0"
boto3-stubs = {extras = ["ssm"], version = "^1.21.45"}
coverage = {extras = ["toml"], version = "^6.4.1"}
flake9 = "^3.8.3"
isort = "^5.9.3"
moto = {extras = ["all"], version = "^2.2.19"}
mypy = "^0.910"
pytest = "^6.2.5"
pytest-cov = "^2.12.1"

[tool.semantic_release]
# https://python-semantic-release.readthedocs.io/en/latest/configuration.html
branch = "main"
build_command = "pip install poetry && poetry build" # https://github.com/relekang/python-semantic-release/issues/222#issuecomment-709326972
upload_to_pypi = true
upload_to_release = true
version_toml = "pyproject.toml:tool.poetry.version"
version_variable = "pydantic_ssm_settings/__init__.py:__version__"

[tool.flake8]
ignore = ["E203", "W503"]
max-complexity = 14
max-line-length = 90

[tool.pytest]
addopts = "-p no:hypothesispytest"
testpaths = "tests"

[tool.isort]
combine_as_imports = "true"
force_grid_wrap = 0
include_trailing_comma = "true"
known_first_party = "pydantic_ssm_settings"
line_length = 90
multi_line_output = 3

[tool.mypy]
files = "pydantic_ssm_settings"
python_version = 3.9
show_error_codes = "True"

[tool.coverage]
precision = 2
range = [95, 100]

[tool.coverage.comment]
layout = "header, diff, flags, files, footer"
```

--

Packages Python modules

`pyproject.toml`

```toml [1-3]
[build-system]
build-backend = "poetry.masonry.api"
requires = ["poetry>=0.12"]

[tool.poetry]
authors = ["Anthony Lukach <anthonylukach@gmail.com>"]
description = "Replace Pydantic's builtin Secret Support with a configuration provider that loads parameters from AWS Systems Manager Parameter Store."
homepage = ""
license = "MIT"
maintainers = ["Anthony Lukach <anthonylukach@gmail.com>"]
name = "pydantic-ssm-settings"
readme = "README.md"
repository = "https://github.com/developmentseed/pydantic-ssm-settings/"
version = "0.2.3"

[tool.poetry.dependencies]
"boto3" = "^1.21.45"
pydantic = "^1.6.2"
python = "^3.7"

[tool.poetry.dev-dependencies]
black = "^22.3.0"
boto3-stubs = {extras = ["ssm"], version = "^1.21.45"}
coverage = {extras = ["toml"], version = "^6.4.1"}
flake9 = "^3.8.3"
isort = "^5.9.3"
moto = {extras = ["all"], version = "^2.2.19"}
mypy = "^0.910"
pytest = "^6.2.5"
pytest-cov = "^2.12.1"
```

--

Manages Package configuration

`pyproject.toml`

```toml [4-14]
[build-system]
build-backend = "poetry.masonry.api"
requires = ["poetry>=0.12"]

[tool.poetry]
authors = ["Anthony Lukach <anthonylukach@gmail.com>"]
description = "Replace Pydantic's builtin Secret Support with a configuration provider that loads parameters from AWS Systems Manager Parameter Store."
homepage = ""
license = "MIT"
maintainers = ["Anthony Lukach <anthonylukach@gmail.com>"]
name = "pydantic-ssm-settings"
readme = "README.md"
repository = "https://github.com/developmentseed/pydantic-ssm-settings/"
version = "0.2.3"

[tool.poetry.dependencies]
"boto3" = "^1.21.45"
pydantic = "^1.6.2"
python = "^3.7"

[tool.poetry.dev-dependencies]
black = "^22.3.0"
boto3-stubs = {extras = ["ssm"], version = "^1.21.45"}
coverage = {extras = ["toml"], version = "^6.4.1"}
flake9 = "^3.8.3"
isort = "^5.9.3"
moto = {extras = ["all"], version = "^2.2.19"}
mypy = "^0.910"
pytest = "^6.2.5"
pytest-cov = "^2.12.1"
```

--

Manages dependencies

`pyproject.toml`

```toml [16-30]
[build-system]
build-backend = "poetry.masonry.api"
requires = ["poetry>=0.12"]

[tool.poetry]
authors = ["Anthony Lukach <anthonylukach@gmail.com>"]
description = "Replace Pydantic's builtin Secret Support with a configuration provider that loads parameters from AWS Systems Manager Parameter Store."
homepage = ""
license = "MIT"
maintainers = ["Anthony Lukach <anthonylukach@gmail.com>"]
name = "pydantic-ssm-settings"
readme = "README.md"
repository = "https://github.com/developmentseed/pydantic-ssm-settings/"
version = "0.2.3"

[tool.poetry.dependencies]
"boto3" = "^1.21.45"
pydantic = "^1.6.2"
python = "^3.7"

[tool.poetry.dev-dependencies]
black = "^22.3.0"
boto3-stubs = {extras = ["ssm"], version = "^1.21.45"}
coverage = {extras = ["toml"], version = "^6.4.1"}
flake9 = "^3.8.3"
isort = "^5.9.3"
moto = {extras = ["all"], version = "^2.2.19"}
mypy = "^0.910"
pytest = "^6.2.5"
pytest-cov = "^2.12.1"
```

---

Automate!<!-- .element: class="r-fit-text" -->

--

<!-- .slide: data-background="imgs/semantic-prs-bot-screenshot.png" -->

[Semantic PRs](https://github.com/apps/semantic-prs) GitHub App

--

<!-- .slide: data-background-iframe="https://python-semantic-release.readthedocs.io/en/latest/" data-background-interactive="" -->

Bump versions from Conventional Commits via [Python Semantic Release](https://python-semantic-release.readthedocs.io/en/latest/)

--

Run in a Github Action via

`relekang/python-semantic-release`

```yaml [70-75|85-90]
name: CI

on:
  push:

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Install poetry
        run: pipx install poetry

      - uses: actions/setup-python@v4
        with:
          python-version: 3.9
          cache: "poetry"

      - run: poetry install

      - name: run linters
        uses: wearerequired/lint-action@v1
        with:
          black: true
          black_args: pydantic_ssm_settings
          black_command_prefix: poetry run
          flake8: true
          flake8_args: pydantic_ssm_settings
          flake8_command_prefix: poetry run
          mypy: true
          mypy_args: pydantic_ssm_settings
          mypy_command_prefix: poetry run

  test:
    runs-on: ubuntu-latest
    strategy:
      fail-fast: false
      matrix:
        # python-version: [3.6, 3.7, 3.8, 3.9]
        python-version: [3.8]
    env:
      PYTHON: ${{ matrix.python-version }}
      OS: ubuntu

    steps:
      - uses: actions/checkout@v3

      - name: Install poetry
        run: pipx install poetry

      - uses: actions/setup-python@v4
        with:
          python-version: ${{ matrix.python-version }}
          cache: "poetry"

      - run: poetry install

      - run: poetry run pytest --cov=pydantic_ssm_settings
        env:
          COVERAGE_FILE: coverage/.coverage.linux-py${{ matrix.python-version }}-cN-dN
          CONTEXT: linux-py${{ matrix.python-version }}-compiled-no-deps-no

      - name: store coverage files
        uses: actions/upload-artifact@v2
        with:
          name: coverage
          path: coverage

  release:
    name: Semantic Release
    runs-on: ubuntu-latest
    concurrency: push
    if: github.ref_name == 'main'
    needs: [test, lint]

    steps:
      - uses: actions/checkout@v3
        with:
          # You must set fetch-depth to 0 when using actions/checkout@v2, since
          # Python Semantic Release needs access to the full history to
          # determine whether a release should be made.
          fetch-depth: 0

      - name: Python Semantic Release
        uses: relekang/python-semantic-release@v7.28.1
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          repository_username: __token__
          repository_password: ${{ secrets.PYPI_API_TOKEN }}
```

--

<!-- .slide: data-background="imgs/dependabot-screenshot.png" -->

Works with Dependabot

--

<!-- .slide: data-background="imgs/changelog-screenshot.png" -->

Updates your `CHANGELOG.md`

--

<!-- .slide: data-background="imgs/github-releases-screenshot.png" -->

Generates a GitHub Release
