# Salt Promotion Pipeline Stages

The Jenkins pipeline is defined in [openSUSE/salt-package-promote-obs](https://github.com/openSUSE/salt-package-promote-obs). This page walks through the stages of the pipeline. Look [here](https://github.com/openSUSE/salt/wiki/How-to-promote-the-Salt-packages) when you want to know how to use it.

**Note**: At the time of writing (May 2021), there is the need to maintain, and therefore promote, two Salt versions. The reason for that is that Salt 3002.2 is the main version, but Salt 3000 is still maintained for distributions that do not support 3002.2. This is the case for Distributions that use Python 2.7 or Python 3.4.

## Parameters

- `salt_version`: The Salt version of the Salt package at `products:testing` that will be promoted to `products`
- `mu_version`: The [SUSE Manager](https://www.suse.com/products/suse-manager/) maintenance update (MU) version that the next Salt update is released along

## Stage 0: Initial Checks

This stage ensures that MU branches are set up in openSUSE/salt and openSUSE/salt-packages. These branches need to reflect the patch level of `products:testing`. We (might) use them for emergency maintenance updates (EMUs), that must only contain a fix on top of what is already released. Having branches that reflect what's in a MU makes that easy. At this time, the content of the MU branches is not verified by the pipeline. This should be done in the future.

There is one check in this stage that is not about the MU branches. That one checks that `salt_XYZ.orig.tar.gz` tarball used for Debian packaging has the correct version (i.e. `XYZ` is equal to the `salt_version` parameter listed above.)

## Stage 1: Create temporaray OBS environment

This stage is very simple, it creates a directory in `/tmp` that is used from here on.

## Stage 2: Promote Salt testing RPM packages

This stage runs an `osc copypac` from `systemsmanagement:saltstack:products:testing` to `systemsmanagement:saltstack:products` for these packages:
- `salt`
- `py26-compat-salt` - for managing Salt 2016.11.10 minions that run Python2.6 with `salt-ssh`
- `py26-compat-tornado` - dependency for `py26-compat-salt`
- `py26-compat-msgpack-python` - dependency for `py26-compat-salt`
- `py27-compat-salt` - for managing Salt 3000(.3) minions that run Python2.7 with `salt-ssh`


Additionally, the stage runs `osc copypac` from `systemsmanagement:saltstack:products:old:testing` to `systemsmanagement:saltstack:products:old` for `salt`. This is the 2016.11.10 codebase that only receives security fixes. It is used on SUSE Linux Enterprise 11 SUSE Manager Client systems.

## Stage 3: Promote Salt 3000 testing RPM packages

This is a temporary stage. It only exists because we are currently maintaining Salt 3000 along with the main Salt version 3002.2. For this reasons, it contains hardcoded project names. 

All this stage does is an `osc copypac` from `systemsmanagement:saltstack:products:3000:testing` to `systemsmanagement:saltstack:products:3000"` for `salt`.

## Stage 4: Promote Salt testing DEB packages

This stage is similar to stage 2 in spirit, but different in the implementation. The Debian packaging workflow setup is different from the one used for rpm packages. Instead of working with patches on top of the source tarball for a Salt release (marked with a Git tag), the Debian packages download the already-patched sources from GitHub. This is done with an OBS Service. The service is triggered automatically in `systemsmanagement:saltstack:products:testing:debian` before the packages are (re-)built. But that is not a good idea for `systemsmanagement:saltstack:products:debian`, we don't want the package sources to change automatically.

This stage takes care of disabling the OBS Service, triggering it once to get a new source tarball, adding the new tarball and checking it in. It then uses `osc copypac` to copy everything in `systemsmanagement:saltstack:products:testing:debian/salt` to `systemsmanagement:saltstack:products:debian`.

As a final step, the stage does some cleanup. It re-enables the OBS Service in `systemsmanagement:saltstack:products:debian` and removes the tarball.

## Stage 5: Promote Salt 3000 testing DEB packages

This is a temporary stage. It only exists because we are currently maintaining Salt 3000 along with the main Salt version 3002.2. For this reasons, it contains hardcoded project names.

This stage works just as stage 4, expect that it operates on the debian subproject under `systemsmanagement:saltstack:products:3000:debian`.

## Stage 6: Cleanup

The final stage. This stage ensures that the package links for Debian packages (to the correspoding rpm OBS packages) are correct and removes the OBS environment created in stage 1.
