# openSUSE Salt Development Wiki

This is the developer documentation for Salt Developers in openSUSE. For user documentation, please take a look at the [upstream docs](https://docs.saltproject.io/en/latest/).

## Development Environment

### Git Remotes
As this repository tracks [Salt upstream](https://github.com/saltstack/salt), openSUSE's Salt developers need to work with more than one [Git remote](https://git-scm.com/docs/git-remote). We use [GitHub Pull Requests](https://docs.github.com/en/free-pro-team@latest/github/collaborating-with-issues-and-pull-requests/about-pull-requests) for contributions, either from forks or other branches in this repository. That results in three or four different remotes:

1. https://github.com/saltstack/salt - `upstream`
2. https://github.com/openSUSE/salt - `opensuse`
3. https://github.com/<geeko\>/salt - `origin` (created by forking `upstream`)
4. https://github.com/<geeko\>/salt-1 - `opensuse-fork` (created by forking `opensuse` afterwards)

The names of these remotes can be chosen freely, this documentation will use the names that are listed above.

### Pull Request Policy

There are generally two reasons for opening a Pull Request: adding a new feature or fixing a bug in the existing code base. In both cases, Pull Requests in this repo (`opensuse`) **must** point to one or more Pull Request(s) @`upstream`.

Pull Requests @`upstream` **must** have received positive feedback or been merged before they can be accepted. If `upstream` does not agree with a bug fix, try to find a different way that solves the bug. We don't want to carry changes that are not accepted into `upstream`.

### Branches

The `opensuse` repository contains branches per Salt version that is packaged at OBS. At the time of writing, there are:

- `openSUSE-3000` (default branch)
- `openSUSE-3002.2`
- `openSUSE-2016.11.10`

The default branch is the one of the current version in the latest openSUSE Leap and SUSE Linux Enterprise.

New branches are created by rebasing older patches on top of an `upstream` Salt release (marked by a Git tag). From then on, they move independently with us backporting bug fixes from newer releases. 


### Note on Git Worktrees

One useful Git feature is [`git-worktree`](https://git-scm.com/docs/git-worktree) which allows multiple work trees to co-exist. Different work trees must have different branches checked out and can be used for quickly jumping between Salt versions without having to `git stash` or `git commit` first.

For example, one might have two long living work trees, one that has `upstream`'s master branch checked out and one for `opensuse`'s default branch:
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

Since there are so many projects and it is easy to forget which Salt version is being built in each project, we have a small tool that queries OBS: [saltversions](https://github.com/brejoc/saltversions)

## Testing

We have a tool for running `upstream` integration and unit tests in Docker containers called [Salt Toaster](https://opensuse.github.io/salt-toaster/). Salt Toaster also powers an integration test suite from SUSE.

## Interesting pages

- [py-spy](py-spy): Profiling Python applications
