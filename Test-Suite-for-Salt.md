## Salt Test Suite General Info

[Test suite](https://github.com/isbm/salt-tester) runner is a workflow to perform all kind of covered **white list** tests for the [Salt package](https://build.opensuse.org/package/show/systemsmanagement:saltstack/salt) we maintain. The general idea is to have a verified mix-in of all kind of tests possible for the Salt:

* Integration tests
* Unit Tests
* Custom tests

## The Workflow

* Patch(es are) brought to the Git Repository of [openSUSE Salt fork](https://github.com/openSUSE/salt/). There is a [detailed instruction](https://github.com/openSUSE/salt/wiki) how this should be done.
* Package is built in the [_testing_ area](https://build.opensuse.org/package/show/systemsmanagement:saltstack:testing/salt) that includes these new patch(es).
* The [Salt Test Suite](https://github.com/isbm/salt-tester) is performing on a testing platform (usually Jenkins)
* In case of test failure, the issue needs to be fixed and fixes needs to be published at the same place where they are coming from (i.e. if the fix is needed for the upstream, it has to be published there)
* In case of success, **only at this point** the testing package is declared as ready to replace the same package in the [production area](https://build.opensuse.org/package/show/systemsmanagement:saltstack/salt).

## Working with the Salt Test Suite

### Adding a Test

In general, test are usually just a shell scripts. Technically, they can be as anything else that runs and reports [POSIX exit codes](http://www.tldp.org/LDP/abs/html/exitcodes.html), but _preferrably_ they should be just a shell scripts. The Salt Test suite is simply running these shell scripts one after another and stops immediately if there is an error.

To add a test is to add a shell script that can run (with `chmod +x`) and report POSIX exit code. If shell script exits with zero, then test is successful. Please follow this procedure:

* Add a shell script to the [`$SUITE/tests`](https://github.com/isbm/salt-tester/tree/master/tests) directory and name it as you wish.
* Add its full filename to the [`$SUITE/etc/progression`](https://github.com/isbm/salt-tester/blob/master/etc/progression) file. You can always comment it out, to disable the test while working on it.
* Write or call an integration test. It depends what you are doing and most of the time you likely want to run a particular command on the minion and check its result.

### Adding a Unit Test

First of all, there are four main issues with the Salt Unit Tests:

* Far not everything is covered with them.
* Not everything is needed and not everything will pass through. For example, ZFS, ZPool, Cassandra, MacOS related tests etc.
* Hundreds of them are broken and failing.
* They aren't unit tests from its pure form most of the time, but actually an integration tests _as unit tests_.

In order to full-fill the general policy of the testing (i.e. "should be always green"), there is a [_while list_](https://github.com/isbm/salt-tester/blob/master/etc/unit_tests) of the Unit Tests that are actually passing.

Therefore, in order to add a Unit Test(s), please follow this procedure:

* For a completely new Unit Test, i.e. new `yourgreat_test.py` has been added, you should specify it in the `$SUITE/etc/unit_tests` file as a Pythonic path. E.g. if you place your file as `$SALT/tests/unit/modules/yourgreat_test.py` then you should specify it as `unit.modules.yourgreat_test` and place it to the end of the related group, i.e if it is `unit.modules...` then keep it with "modules" etc. Follow the other examples of other Unit Tests.
* For an existing Unit Test file and is already enabled (see above), just write a yet another Unit Test within that Python source.

### Running Test Suite

Test Suite at the moment is running within a Docker container and every test can be as destructive as it is needed.

### Tools

There is one tool for JSON testing convenience, called [`jsontest`](https://github.com/isbm/salt-tester/blob/master/bin/jsontest). It is far from being perfect, however it checks if in the given JSON are expected values of expected types at expected places.

### Be Aware Of Pitfalls

When writing a script, be aware of the following pitfalls:

* Salt is not always returns non-zero if there is actually an error. Take care of this in your script yourself. _NOTE: Place for improvement!_

* Make sure your shell script is transferring the error code properly to the very end and does not yanks it in between. This is a common inconvenience and is often overlooked.