# Packaging Overview

Our Salt packages are done in the "SUSE traditional way", where patch files are inside the RPM source package. It is not possible to generate a tarball from this repository, because the package **must** provide an original `.tar.gz` source tarball and apply patches *on top of it*.

Since the `diff` between the `upstream` tarball and in our `opensuse` repository can be quite large, we maintain a [separate repository](https://github.com/openSUSE/salt-packaging) with generated `.patch` files. These are generated manually with the help of [[git-format-pkg-patch|Using-git-format-pkg-patch]]. 

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

"Promoting" packages from `systemsmanagement:saltstack:products:testing` to
`systemsmanagement:saltstack:products` is done with the use of a Jenkins
pipeline. The workflow is documented in [[How to promote Salt packages|How-to-promote-the-Salt-packages]].
