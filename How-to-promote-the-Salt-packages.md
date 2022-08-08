## Introduction

We have automated the process to "promote" our testing changes for Salt into our "stable" projects. We are using Jenkins pipelines for doing these tasks:

## Salt RPM Package Promotion Pipeline
This promotion pipeline is used to &ldquo;promote&rdquo; the OBS package from the `products:testing` project to the `products` project. It runs in [Jenkins](https://ci.suse.de) and is manually triggered by a member of the Ion Squad. The `products` project is linked to projects in IBS that are used for releasing SUSE Manager. For this reason we have a separate OBS project that receives submit requests.

This Jenkins pipeline is defined in [openSUSE/salt-package-promote-obs](https://github.com/openSUSE/salt-package-promote-obs). If you want to learn more about the pipeline in detail, take a look at the [Salt Promotion Interals](https://github.com/openSUSE/salt/wiki/Salt-Promotion-Pipeline-Internals) page.

### Promotion Checklist

The `products:testing` [testsuite](https://ci.suse.de/user/manager/my-views/view/Salt/view/default/) must be green and the diff between the source and destination projects should be correct and as expected.

Your check-list for the diffs:

- all new changelog entries are placed on top of the current ones
- all new changelog entries you've added are there
- all new patches you've added are there
- all patches you've removed are also removed
- nothing else is happening that you didn't do (gets removed or added)


```bash
# Example of how to show changes that are going to be promoted

osc rdiff systemsmanagement:saltstack:products salt systemsmanagement:saltstack:products:testing
osc rdiff systemsmanagement:saltstack:products py26-compat-salt systemsmanagement:saltstack:products:testing
osc rdiff systemsmanagement:saltstack:products py26-compat-tornado systemsmanagement:saltstack:products:testing
osc rdiff systemsmanagement:saltstack:products py26-compat-msgpack-python systemsmanagement:saltstack:products:testing
osc rdiff systemsmanagement:saltstack:products py27-compat-salt systemsmanagement:saltstack:products:testing
osc rdiff systemsmanagement:saltstack:products:old salt systemsmanagement:saltstack:products:old:testing
```

**If the results from the `osc rdiff` are looking good, then we can promote the packages.**

### How to run the Promotion Pipeline

Running the pipeline needs access to [SUSE&rsquo;s Jenkins](https://ci.suse.de). Log in with the SUSE Manager team credentials and navigate to
[Dashboard > manager-salt-package-promote-pipeline](https://ci.suse.de/job/manager-salt-package-promote-pipeline/build?delay=0sec).

#### Parameters

-   `salt_version`: The Salt version that is currently used in `products` and `products:testing`.
-   `mu_version`: The Maintenance Update that the newly promoted version will be released in.


This pipeline is taking care of:
- Promote necessary packages from `products:testing` to `products`
- Promote necessary packages from `products:old:testing` to `products:old`
- Run services on `products:debian/salt` to refresh the Debian package.
- Run services for the Ubuntu (16.04 & 18.04) client tools at `Uyuni:Master`
- Run services for the Ubuntu (16.04 & 18.04) client tools at `Devel:Galaxy:Manager:4.0`
- Run services for the Ubuntu (16.04 & 18.04) client tools at `Devel:Galaxy:Manager:Head`

## Salt Bundle (venv-salt-minion) Promotion Pipeline
This other promotion pipeline is used to &ldquo;promote&rdquo; the Salt Bundle OBS package (venv-salt-minion), as well as all the necessary Salt Bundle dependencies (saltbundlepy-* packages) from the `systemsmanagement:saltstack:bundle:testing` project to the `systemsmanagement:saltstack:bundle` project.

It also takes care of promoting the packages from the different client tools subprojects as well as the `debbuild` subproject:

- systemsmanagement:saltstack:bundle:testing:AlmaLinux8
- systemsmanagement:saltstack:bundle:testing:CentOS7
- systemsmanagement:saltstack:bundle:testing:CentOS8
- systemsmanagement:saltstack:bundle:testing:Debian10
- systemsmanagement:saltstack:bundle:testing:Debian11
- systemsmanagement:saltstack:bundle:testing:Debian9
- systemsmanagement:saltstack:bundle:testing:Fedora33
- systemsmanagement:saltstack:bundle:testing:Fedora34
- systemsmanagement:saltstack:bundle:testing:Fedora35
- systemsmanagement:saltstack:bundle:testing:Raspbian10
- systemsmanagement:saltstack:bundle:testing:Raspbian9
- systemsmanagement:saltstack:bundle:testing:SLE12
- systemsmanagement:saltstack:bundle:testing:SLE15
- systemsmanagement:saltstack:bundle:testing:Ubuntu1804
- systemsmanagement:saltstack:bundle:testing:Ubuntu2004
- systemsmanagement:saltstack:bundle:testing:debbuild

It runs in [Jenkins](https://ci.suse.de) and is manually triggered by a member of the Ion Squad. 

This Jenkins pipeline is defined in [openSUSE/salt-package-promote-obs](https://github.com/openSUSE/salt-package-promote-obs).

**NOTE: Please keep in mind this pipeline does not run automatically when running the other pipeline for the Salt RPM package, we need to run this explicitely.**

## Releasing Salt in SLE
Once the packages are promoted to `products`, our SUSE Manager Release Engineer iS taking care of creating the necessary Maintenance Requests (MRs) against SUSE Maintenance as part of the tasks for the next Maintenance Update (MU) for some of the release codestream, but not for all of them.

The Salt Maintainers **needs to take care of creating the MRs in IBS** for the following codestreams **only at the time** they get pinged by our Release Engineer at the preparing the MU submissions:

- SUSE:SLE-15-SP1:Update
- SUSE:SLE-15-SP2:Update
- SUSE:SLE-15-SP3:Update
- SUSE:SLE-15-SP4:Update

In order to do that we need to do the following for each codestream:

1) Create a branch of the target codestream in IBS.
2) Change "_service" file and adjust `revision` to the corresponding MU branch. Like `MU-4.3.1`. (to avoid getting patches that might be already pushed to testing)
3) Run disabled services: `iosc service disabled run`.
4) Add the new patches and review the generated changelog entry.
5) Revert changes for "_service" file: `iosc revert _service`.
6) Commit the changes and then create MR with `iosc mr`.

---
**Happy promoting!** :wink: 