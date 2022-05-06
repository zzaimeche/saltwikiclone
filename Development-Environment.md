## Git Overview

Salt's source code is managed via [Git](https://git-scm.com/). Basic Git knowledge is assumed, but we have examples of somewhat advanced Git workflows in our [Common Workflows][#common-workflows] section.

### Remotes

This repository is not the authoritative Salt repository. As such, openSUSE's Salt developers need to work with multiple [Git Remotes](https://git-scm.com/docs/git-remote):

1. https://github.com/saltstack/salt - `origin`
2. https://github.com/openSUSE/salt - `opensuse`
3. https://github.com/<geeko\>/salt - `geeko` (created by forking `origin`)
4. https://github.com/<geeko\>/salt-1 - `opensuse-fork` (created by forking `opensuse` after setting up remote `geeko`)

You can choose these `names` freely, however, this documentation will use the names that are listed above.

### Local Checkout

A local checkup with the [remote setup](#remotes), once the forks are created through GitHub, can easily be done with a few `git` commands.

```sh
cd ~/src # optional
geeko=agraul
git clone git@github.com:saltstack/salt salt && cd $_
git remote add opensuse git@github.com:openSUSE/salt
git remote add $geeko git@github.com:$geeko/salt
git remote add opensuse-fork git@github.com:$geeko/salt-1
git fetch --all
```

### Active Branches

On `origin`, the only branch we work with is `master`. On `opensuse` however, we maintain a branch for each Salt version we package and support:

- `openSUSE-3000`
- `openSUSE-3000.3` (used as a Python2.7 compatible codebase for Salt SSH)
- `openSUSE-3002.2` (default branch)
- `openSUSE-3004` (next default)
- `openSUSE-2016.11.10`

The default branch is the one of the current version in the latest openSUSE Leap and SUSE Linux Enterprise.

New branches are created by rebasing older patches on top of an `origin` Salt release (marked by a Git tag). From then on, they move independently with us backporting bug fixes from newer releases.

### Branch setup

To reduce the number of branches we have on the top level we do want to make use of folders. Folders for Git branches is a concept which many popular Git GUIs implement and it eases the overview in case you have a lot of branches.

The naming schema we follow can be observed in the following list:

```
master                   --> saltstack/salt master branch
fix/<name>               --> Upstream fixes
openSUSE/release/<name>  --> Downstream openSUSE release
openSUSE/fix/<name>      --> openSUSE specific fixes
openSUSE/backport/<name> --> openSUSE specific backports
openSUSE/MU/<release>    --> SUMA MU versions
<name>                   --> features and misc
```

> Note: At this point in time the openSUSE/salt repository does not have the fork relationship for saltstack/salt. We will readd this after SUSE Manager 4.3 GA was tagged.

### GitHub Pull Request Policy

There are generally two reasons for opening a Pull Request: adding a new feature or fixing a bug in the existing code base. In both cases, Pull Requests in this repo (`opensuse`) **must** point to one or more Pull Request(s) upstream (@`origin`).

Pull Requests @`origin` **must** have received positive feedback or been merged before they can be accepted. If `origin` does not agree with a bug fix, try to find a different way that solves the bug. We don't want to carry changes that are not accepted into `origin`.

### Worktrees

One useful Git feature is [`git-worktree`](https://git-scm.com/docs/git-worktree) which allows multiple work trees to co-exist. Different work trees must have different branches checked out and can be used for quickly jumping between Salt versions without having to `git stash` or `git commit` first.

For example, you might have two long living git trees you work on: one that has `origin`'s master branch checked out and one for `opensuse`'s default branch:
```text
# tree -L 1 ~/src/salt
├── 3002
└── master
```

## Setting up a virtual Python environment

Virtual environments are isolated environments that can be used to keep all Python packages needed for developing Salt in one place. They help avoiding conflicts with globally installed Python packages, e.g. when two Python programs need different versions of the same package.

With the following steps you can create a git checkout of the Salt repository and a virtual environment called `venv` inside of it.

*Step 5 installs Salt's dependencies into the virtual environment. Some of these need additional system libraries. On openSUSE Leap or Tumbleweed, you can install them with `zypper install python3-devel libopenssl-devel libcurl-devel zeromq-devel`*

1. `git clone https://github.com/saltstack/salt salt && cd $_`
2. `python3 -m venv venv` # the first `venv` is the virtual environment module, the second `venv` is the name of the virtual environment.
3. `. venv/bin/activate`
4. `python --version` # Verify the virtual environment is set up and active, `python` should invoke `python3` now
5. `python -m pip install -r requirements/base.txt -r requirements/tests.txt -r requirements/pytest.txt -r requirements/crypto.txt -r requirements/zeromq.txt`
6. (Optional) `python -m pip install nox` # needed for running tests
7. Hack, hack, hack
8. `deactivate`
