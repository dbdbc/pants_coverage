# Minimal example to show a possible bugs with pants+coverage

Install as follows:

    $ python3 --version
    Python 3.8.5
    $ python3 -m venv test_env
    $ source test_env/bin/activate
    $ pip install -U pip
    $ pip install -U wheel poetry
    $ poetry install

Run tests through pants:

    $ ./pants test --test-use-coverage ::
    ...
    ✓ tests/test_placeholder.py:tests0 succeeded in 0.28s.

    Wrote raw coverage report to `dist/coverage/python`

    Name                          Stmts   Miss  Cover
    -------------------------------------------------
    source/mypkg/placeholder.py       2      0   100%
    -------------------------------------------------
    TOTAL                             2      0   100%

Check coverage directly:

    $ COVERAGE_FILE=dist/coverage/python/.coverage coverage report
    Name                          Stmts   Miss  Cover
    -------------------------------------------------
    source/mypkg/placeholder.py       2      2     0%
    -------------------------------------------------
    TOTAL                             2      2     0%

## Issue 1:

The file `source/mypkg/uncovered.py` is not shown in the coverage report, as no test depends on it. This leads to
the coverage being erroneously calculated as 100%, when in reality should be 50% (2 statements in `placeholder.py`
covered, 2 statements in `uncovered.py` not covered).

## Issue 2:

The coverage database `dist/coverage/python/.coverage` is not read/parsed correctly by `coverage` outside of `pants`,
always showing a coverage of 0%.
