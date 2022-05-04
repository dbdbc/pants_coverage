# Minimal example to show a possible bug with pants+coverage

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
    ✓ tests/test_placeholder.py:tests0 succeeded in 0.53s.

    Wrote raw coverage report to `dist/coverage/python`

    Name                          Stmts   Miss  Cover
    -------------------------------------------------
    source/mypkg/placeholder.py       2      0   100%
    tests/test_placeholder.py         3      0   100%
    -------------------------------------------------
    TOTAL                             5      0   100%

Check coverage directly:

    $ COVERAGE_FILE=dist/coverage/python/.coverage coverage report
    Name                          Stmts   Miss  Cover
    -------------------------------------------------
    source/mypkg/placeholder.py       2      2     0%
    tests/test_placeholder.py         3      3     0%
    -------------------------------------------------
    TOTAL                             5      5     0%
