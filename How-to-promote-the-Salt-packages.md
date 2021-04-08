## Salt Package Promotion Pipeline
The promotion pipeline is used to &ldquo;promote&rdquo; the OBS package from the `products:testing` project to the `products` project. It runs in [Jenkins](https://ci.suse.de) and is manually triggered by a member of the Ion Squad. The `products` project is linked to projects in IBS that are used for releasing SUSE Manager. For this reason we have a separate OBS project that receives submit requests.

The Jenkins pipeline is defined in [meaksh/salt-package-promote-obs](https://github.com/meaksh/salt-package-promote-obs) (subject to move to the openSUSE GitHub organization).

### What is getting promoted?

All OBS projects listed below live in the `systemsmanagement:saltstack` namespace.

#### From `products:testing` to `products`:
  - salt
  - py26-compat-salt
  - py26-compat-tornado
  - py26-compat-msgpack-python
-   py27-compat-salt

#### From `products:old:testing` to `products:old`:
  - salt (2016.11.10)

#### From `products:testing:debian` to `products:debian`:
  - salt

#### From `products:testing:debian:python2` to `products:debian:python2`:
  - salt

#### From `products:testing:debian:python3` to `products:debian:python3`:
  - salt

### Promotion Checklist

The `products:testing` [testsuite](https://ci.suse.de/user/manager/my-views/view/Salt/view/default/) must be green and the diff between the source and destination projects should be correct and as expected.

Your check-list for the diffs:

- all new changelog entries are placed on top of the current ones
- all new entries you've added are there
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

**Happy promoting!** :wink: 