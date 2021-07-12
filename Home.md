# openSUSE Salt Development Wiki

This is the developer documentation for Salt Developers in openSUSE. For user documentation, please take a look at the [upstream docs](https://docs.saltproject.io/en/latest/).

## Development Environment

### Git Remotes
As this repository tracks [Salt upstream](https://github.com/saltstack/salt), openSUSE's Salt developers need to work with more than one [Git remote](https://git-scm.com/docs/git-remote). We use [GitHub Pull Requests](https://docs.github.com/en/free-pro-team@latest/github/collaborating-with-issues-and-pull-requests/about-pull-requests) for contributions, either from forks or other branches in this repository. That results in three or four different remotes:

1. https://github.com/saltstack/salt - `upstream`
2. https://github.com/openSUSE/salt - `opensuse`
3. https://github.com/<geeko\>/salt - `origin` (created by forking `upstream`)
4. https://github.com/<geeko\>/salt-1 - `opensuse-fork` (created by forking `opensuse` afterwards)

You can choose the names of these remotes freely, however, this documentation will use the names that are listed above.

### Pull Request Policy

There are generally two reasons for opening a Pull Request: adding a new feature or fixing a bug in the existing code base. In both cases, Pull Requests in this repo (`opensuse`) **must** point to one or more Pull Request(s) @`upstream`.

Pull Requests @`upstream` **must** have received positive feedback or been merged before they can be accepted. If `upstream` does not agree with a bug fix, try to find a different way that solves the bug. We don't want to carry changes that are not accepted into `upstream`.

### Branches

The `opensuse` repository contains branches per Salt version that is packaged at OBS. At the time of writing, there are:

- `openSUSE-3000`
- `openSUSE-3000.3`
- `openSUSE-3002.2` (default branch)
- `openSUSE-2016.11.10`

The default branch is the one of the current version in the latest openSUSE Leap and SUSE Linux Enterprise.

New branches are created by rebasing older patches on top of an `upstream` Salt release (marked by a Git tag). From then on, they move independently with us backporting bug fixes from newer releases. 

### Setting up a virtual Python environment

Virtual environments are isolated environments that can be used to keep all Python packages needed for developing Salt in one place. They help avoiding conflicts with globally installed Python packages, e.g. when two Python programs need different versions of the same package.

With the following steps you can create a git checkout of the Salt repository and a virtual environment called `venv` inside of it.

*Step 5 installs Salt's dependencies into the virtual environment. Some of these need additional system libraries. On openSUSE Leap or Tumbleweed, you can install them with `zypper install python3-devel libopenssl-devel libcurl-devel zeromq-devel`*

1. `git clone --origin upstream https://github.com/saltstack/salt && cd salt`
2. `python3 -m venv venv` # the first `venv` is the virtual environment module, the second `venv` is the name of the virtual environment.
3. `. venv/bin/activate`
4. `python --version` # Verify the virtual environment is set up and active, `python` should invoke `python3` now
5. `python -m pip install -r requirements/base.txt -r requirements/tests.txt -r requirements/pytest.txt -r requirements/crypto.txt -r requirements/zeromq.txt`
6. (Optional) `python -m pip install nox` # needed for running tests
7. Hack, hack, hack
8. `deactivate`


### Note on Git Worktrees

One useful Git feature is [`git-worktree`](https://git-scm.com/docs/git-worktree) which allows multiple work trees to co-exist. Different work trees must have different branches checked out and can be used for quickly jumping between Salt versions without having to `git stash` or `git commit` first.

For example, you might have two long living git trees you work on: one that has `upstream`'s master branch checked out and one for `opensuse`'s default branch:
```text
# tree -L 1 ~/src/salt
├── openSUSE-3000
└── master
```

## openSUSE Salt Packaging

Our Salt packages are done in the "SUSE traditional way", where patch files are inside the RPM source package. It is not possible to generate a tarball from this repository, because the package **must** provide an original `.tar.gz` source tarball and apply patches *on top of it*.

Since the `diff` between the `upstream` tarball and in our `opensuse` repository can be quite large, we maintain a [separate repository](https://github.com/openSUSE/salt-packaging) with generated `.patch` files. These are generated manually with the help of [git-format-pkg-patch](Using-git-format-pkg-patch). 

An overview of the Salt Packaging workflow can be seen in the diagram below.
[[images/salt-packaging-overview.png]]


### Build Service Projects

There are two main code streams that we need to handle:
1. *Latest & greatest* Salt for openSUSE Tumbleweed
2. *Tested & stable* Salt for openSUSE Leap and SUSE Linux Enterprise

Each of them have an ever-growing number of OBS projects:

1. "production": This version is shipped in the distributions -- bug reports welcome!
  - For Tumbleweed: [systemsmanagement:saltstack](https://build.opensuse.org/package/show/systemsmanagement:saltstack/salt)
  - For Leap: [systemsmanagement:saltstack:products](https://build.opensuse.org/package/show/systemsmanagement:saltstack:products/salt)
2. "testing": This version is being tested and will move to "production" once all tests pass
  - For Tumbleweed: [systemsmanagement:saltstack:testing](https://build.opensuse.org/package/show/systemsmanagement:saltstack:testing/salt)
  - For Leap: [systemsmanagement:saltstack:products:testing](https://build.opensuse.org/package/show/systemsmanagement:saltstack:products:testing/salt)
3. "next": This version is a testbed. It might move to "production" (via "testing") or be replaced.
  - For Tumbleweed: [systemsmanagement:saltstack:next](https://build.opensuse.org/package/show/systemsmanagement:saltstack:next/salt)
  - For Leap: [systemsmanagement:saltstack:products:next](https://build.opensuse.org/package/show/systemsmanagement:saltstack:products:next/salt)
4. "next testing": This version is for testing before it moves to "next".
  - For Tumbleweed: [systemsmanagement:saltstack:next:testing](https://build.opensuse.org/package/show/systemsmanagement:saltstack:next:testing/salt)
  - For Leap: [systemsmanagement:saltstack:products:next:testing](https://build.opensuse.org/package/show/systemsmanagement:saltstack:products:next:testing/salt)

Since there are so many projects and it is easy to forget which Salt version is being built in each project, we have a small tool that queries OBS: [saltversions](https://github.com/brejoc/saltversions).

"Promoting" packages from `systemsmanagement:saltstack:products:testing` to `systemsmanagement:saltstack:products` is done with the use of a Jenkins pipeline. The workflow is documented [here](https://github.com/openSUSE/salt/wiki/How-to-promote-the-Salt-packages).

## Testing

### Salt Toaster

We have a tool for running `upstream` integration and unit tests in Docker containers called [Salt Toaster](https://opensuse.github.io/salt-toaster/). Salt Toaster also powers an integration test suite from SUSE. These tests are executed automatically in SUSE's CI. For using Salt Toaster, please consult its [documentation](https://opensuse.github.io/salt-toaster/).

### Running in-tree testsuite locally

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


## Interesting pages

- [py-spy](py-spy): Profiling Python applications
- [How to promote the Salt packages](https://github.com/openSUSE/salt/wiki/How-to-promote-the-Salt-packages): Package promotion pipeline
