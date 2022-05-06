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

Using `pytest-cov` directly with the `--cov=` option shows the correct result:

    $ poetry install  # Must install mypkg as editable
    ...
    $ pytest --cov=source/mypkg
    =========================== test session starts ============================
    platform linux -- Python 3.8.5, pytest-7.1.2, pluggy-0.13.1
    rootdir: /home/dba2hi/code/pants_minimal
    plugins: cov-3.0.0
    collected 1 item

    tests/test_placeholder.py .                                          [100%]

    ----------- coverage: platform linux, python 3.8.5-final-0 -----------
    Name                          Stmts   Miss  Cover
    -------------------------------------------------
    source/mypkg/placeholder.py       2      0   100%
    source/mypkg/uncovered.py         2      2     0%
    -------------------------------------------------
    TOTAL                             4      2    50%


    ============================ 1 passed in 0.03s =============================


## Issue 2:

The coverage database `dist/coverage/python/.coverage` is not read/parsed correctly by `coverage` outside of `pants`,
always showing a coverage of 0%.
