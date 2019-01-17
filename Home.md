# openSUSE Salt Packaging

This repository tracks [upstream salt](https://github.com/saltstack/salt) and the patches for the [openSUSE salt](https://build.opensuse.org/package/show/systemsmanagement:saltstack/salt) as well as the [SLE salt](https://build.opensuse.org/package/show/systemsmanagement:saltstack:products/salt) package.

See [here](https://github.com/openSUSE/salt/wiki/Git-.config-for-working-with-remote-repositories) for working with remote git repositories.

# Projects

openSUSE (Factory/Tumbleweed) always get the _latest & greatest_ Salt version.

SUSE Linux Enterprise (SLE) 12 gets the _tested & stable_ Salt version.

Both have three build projects

- one for the tested version (aka 'production') - this is ready to grab and use. Bug reports and welcome
  - [openSUSE](https://build.opensuse.org/package/show/systemsmanagement:saltstack/salt)
  - [SLE](https://build.opensuse.org/package/show/systemsmanagement:saltstack:products/salt)

- one for the latest patch (aka 'testing') - this is currently under test and will move to production when all tests pass
  - [openSUSE](https://build.opensuse.org/package/show/systemsmanagement:saltstack:testing/salt)
  - [SLE](https://build.opensuse.org/package/show/systemsmanagement:saltstack:products:testing/salt)

- one for the next version (aka 'next') - this is a testbed for a new version. It might move to production or get replaced by another version
  - [openSUSE](https://build.opensuse.org/package/show/systemsmanagement:saltstack:next/salt)
  - [SLE](https://build.opensuse.org/package/show/systemsmanagement:saltstack:products:next/salt)

## Branches

* `develop` - upstream development
* `2016.11` - upstream branch for 2016.11.X bugfixes
* `openSUSE-2016.3.4` - openSUSE branch for the [openSUSE salt package](https://build.opensuse.org/package/show/systemsmanagement:saltstack/salt)
* `2015.8` - upstream branch for 2015.8.X bugfixes
* `openSUSE-2015.8.12` - openSUSE branch for the [SLE salt package](https://build.opensuse.org/package/show/systemsmanagement:saltstack:products/salt)

The `openSUSE-2015.8.12` branch is based on the `v2015.8.12` upstream tag. Commits on the `openSUSE-2015.8.12` branch apply cleanly on the `salt-2015.8.12.tar.gz` release tarball.

# Git Workflow

See the [Workflow](Workflow) page

# Tooling
 * Profiling: [py-spy](py-spy)
