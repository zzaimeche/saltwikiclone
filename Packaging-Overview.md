# Packaging Overview

Our Salt packages are done in the "SUSE traditional way", where patch files are inside the RPM source package. It is not possible to generate a tarball from this repository, because the package **must** provide an original `.tar.gz` source tarball and apply patches *on top of it*.

Since the `diff` between the upstream tarball and in our openSUSE/salt repository can be quite large, we maintain a [separate repository](https://github.com/openSUSE/salt-packaging) with generated `.patch` files. These are generated manually with the help of [git-format-pkg-patch](https://github.com/openSUSE/git-packaging-tools/blob/master/git-format-pkg-patch)

An overview of the Salt Packaging workflow can be seen in the diagram below.
[[images/salt-packaging-overview.png]]

## Build Service Projects

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

"Promoting" packages from `systemsmanagement:saltstack:products:testing` to `systemsmanagement:saltstack:products` is done with the use of a Jenkins pipeline. The workflow is documented in [[How to promote Salt packages|How-to-promote-the-Salt-packages]].

## From Pull Request to Package

Once a Pull Request is merged, there are still a few steps needed to get the change into Salt packages. As the image above shows, our packages are based on the [upstream](https://github.com/saltstack/salt) release tarballs with added `.patch` files, that change the source code in the package build process. These patches are maintained kept in [openSUSE/salt-packaging](https://github.com/openSUSE/salt-packaging).

### Simple Patch

This example shows how you can integrate a merged Pull Request targeting `openSUSE-3002.2` into the Salt package in `systemsmanagement:saltstack:products`. Remember that all changes go through the `:testing` subproject!

1. Create a Patch using [openSUSE/git-packaging-tools](https://github.com/openSUSE/git-packaging-tools)
  ```sh
  cd ~/src/salt
  mkdir patches && cd $_
  git-format-pkg-patch --format v3002.2-suse # important: directory must be empty!
  ```
2. Copy the Patch into your local `salt-packaging` repository
  ```sh
  cd ~/src/salt-packaging/salt
  git switch 3002.2
  git pull --ff
  git-format-pkg-patch --update ~/src/salt/patches
  ```
3. Adapt `salt.spec`:
  - At the bottom of the list of `Patch<N>`, add a comment with the link to the upstream PR:`# PATCH-FIX_UPSTREAM: https://github.com/saltstack/salt/pull/123`, where `N` is the number of the last patch that's already listed
  - Add `Patch<N+1>: name-of-patch.patch`
  - (Pre-Salt 3004) Add `%patch<N+1> -p1` right below the line starting with `%patch<N>`
4. Commit the changes. **Important**: The commit message is will be turned into a changelog entry. Regular commit message etiquette does not apply here.
5. Push the new commit to `openSUSE/salt-packaging`.
6. Branch `systemsmanagement:saltstack:products:testing/salt` with `osc`:
  ```sh
  cd ~/obs
  osc bco systemsmanagement:saltstack:products salt # bco = branch + checkout
  cd home:$geeko:branches:systemsmanagement:saltstack:products:testing/salt
  ```
7. Use our [obs source service](https://openbuildservice.org/help/manuals/obs-user-guide/cha.obs.source_service.html) to pull the latest patches from GitHub:
  ```sh
  osc service runall
  osc status # check that salt.spec and salt.changes are marked as modified
  ```
8. Track the new patch and verify the changes:
  ```sh
  osc add name-of-patch.patch
  osc diff
  ```
9. If everything looks good, the change can be commited into OBS:
  ```sh
  osc commit
  # wait while OBS builds the package after the latest modification, then check build results
  osc results
  ```
10. If the build is successful, you can open a Submit Request (SR). By default, the SR will target the project you have branched from:
  ```sh
  osc sr
  ```
11. You are done! Someone else will review and (hopefully accept) your SR!

Submit Requests differ from GitHub's Pull Request in one big way: You can't change them once opened. If you want to change an SR, you need to open a new one. Use `osc sr --supersede <old request id>` to indicate that the new SR replaces the old one.
