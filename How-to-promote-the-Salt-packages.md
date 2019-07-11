Once the Salt toaster results are looking GREEN on Jenkins, the following packages needs to be promoted:

###### From `products:testing` to `products`:
  - salt
  - py26-compat-salt
  - py26-compat-tornado
  - py26-compat-msgpack-python

###### From `products:old:testing` to `products:old`:
  - salt

----

**We should always check if diff (changes to be promoted) is correct and expected.**

Your check-list:

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
osc rdiff systemsmanagement:saltstack:products:old salt systemsmanagement:saltstack:products:old:testing
```

**If the results from the `rdiff` are looking good, then we can promote the packages.**

We automated all the necessary steps into a Jenkins pipeline that can be manually executed.

**In order to that, execute: https://ci.suse.de/job/manager-salt-package-promote-pipeline/**

This pipeline is taking care of:
- Promote necessary packages from `products:testing` to `products`
- Promote necessary packages from `products:old:testing` to `products:old`
- Run services on `products:debian/salt` to refresh the Debian package.
- Run services for the Ubuntu (16.04 & 18.04) client tools at `Uyuni:Master`
- Run services for the Ubuntu (16.04 & 18.04) client tools at `Devel:Galaxy:Manager:4.0`
- Run services for the Ubuntu (16.04 & 18.04) client tools at `Devel:Galaxy:Manager:Head`

**Happy promoting!** :wink: 