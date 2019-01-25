Once the Salt toaster results are looking GREEN on Jenkins, the following packages needs to be promoted:

###### From `products:testing` to `products`:
  - salt
  - py26-compat-salt

###### From `products:old:testing` to `products:old`:
  - salt

----

We should always check if diff (changes to be promoted) is correct and expected.

Your check-list:

- all new changelog entries are placed on top of the current ones
- all new entries you've added are there
- all new patches you've added are there
- all patches you've removed are also removed
- nothing else is happening that you didn't do (gets removed or added)


```bash
# Example of how to show changes that are going to be promoted

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

After package has been promoted, disabled download on the promoted target will be removed. This should be manually reverted back. Typical example:

```diff
===================================================================
--- _service (revision 154)
+++ _service (revision 214)
@@ -15,7 +15,7 @@
     <param name="path">saltstack/salt/tar.gz/v2018.3.0</param>
     <param name="filename">v2018.3.0.tar.gz</param>
   </service>
-  <service name="download_url" mode="disabled">
+  <service name="download_url">
        <param name="protocol">https</param>
        <param name="host">github.com</param>
        <param name="path">openSUSE/salt/archive/openSUSE-2018.3.0.tar.gz</param>
Index: salt.changes
```
