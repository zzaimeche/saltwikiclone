# Testing Salt

## Salt Toaster

We have a tool for running `upstream` integration and unit tests in Docker containers called [Salt Toaster](https://opensuse.github.io/salt-toaster/). Salt Toaster also powers an integration test suite from SUSE. These tests are executed automatically in SUSE's CI. For using Salt Toaster, please consult its [documentation](https://opensuse.github.io/salt-toaster/).

## Running in-tree testsuite locally

It is also possible to run the testsuite locally with `nox`. The [virtual environment](#setting-up-a-virtual-python-environment) section explains how to set up a virtual environment. Make sure to run the optional step 6 which installs `nox`.

`nox` is a tool that creates additional virtual environments on-demand with different configurations. You can list the available configurations, called *sessions*, with `nox --list` (or `-l` for short). One of the listed sessions is `* pytest-3(coverage=False) -> pytest session with zeromq transport and default crypto`. To run that session, you need to pass it to `nox`:

```
nox --session 'pytest-3(coverage=False)' # remember to quote the parenthesis 
```

The first execution takes additional time to prepare the virtual environment that is used for testing this configuration. Following `nox` invocations of the same session will run quicker.

CLI arguments and flags can be passed through to `pytest` by separating them with `--`. This can be used to run all tests in one module or just a single test.
```
nox --session 'pytest-3(coverage=False)' -- tests/unit/modules/test_zypperpkg.py
nox --session 'pytest-3(coverage=False)' -- tests/unit/modules/test_zypperpkg.py::ZypperTestCase::test_list_products
```

New tests should only be added in subdirectories of `tests/pytests/`. There is an ongoing effort upstream that moves old tests into subdirectories of `tests/pytests/`. More information on running the tests and writing new one can be found in the [upstream documentation](https://docs.saltproject.io/en/latest/topics/tutorials/writing_tests.html)
