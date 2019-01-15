Once the Salt toaster results are looking GREEN on Jenkins, the following packages needs to be promoted:

###### From `products:testing` to `products`:
  - salt
  - py26-compat-salt

###### From `products:old:testing` to `products:old`:
  - salt

----

We should always check if diff (changes to be promoted) is correct and expected:

```bash
# Example of how show changes that are going to be promoted

$ osc rdiff systemsmanagement:saltstack:products salt systemsmanagement:saltstack:products:testing
$ osc rdiff systemsmanagement:saltstack:products py26-compat-salt systemsmanagement:saltstack:products:testing
$ osc rdiff systemsmanagement:saltstack:products:old salt systemsmanagement:saltstack:products:old:testing
```

Then we can promote the packages:

```bash
# Example of how packages should be promoted

$ osc copypac systemsmanagement:saltstack:products:testing salt systemsmanagement:saltstack:products
$ osc copypac systemsmanagement:saltstack:products:testing py26-compat-salt systemsmanagement:saltstack:products
$ osc copypac systemsmanagement:saltstack:products:old:testing salt systemsmanagement:saltstack:products:old
```

**NOTE:** Other packages or dependencies might be also need to be promoted.