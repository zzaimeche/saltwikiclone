# Salt Package Support Workflow

Yes, it is not simple. But you can do that if you would just follow the rules. :smile: 

## :arrow_right: Start here

Read the [general policies](#policies) or jump to a specific role:

- [Joining the club](#joining) for the first time?
- [Learn about packaging concepts](#salt-packaging-concepts)
- [Submit a patch](#submitting-patches) for the package
- [Send a Submit Request (SR)](#submitting-an-sr) of prepared package
- [Add a test](#writing-salt-toaster-tests) for your patch
- Roll the entire version
- [Accept SR](#accepting-an-sr) on OBS

---------

## Policies

* :warning: **Always** try to keep patches merged to the upstream, if your patch is related to the source. If upstream does not likes the fix, try to find a compromise and try having no patches that aren't upstream. 
* :warning: **Always** open Pull Requests for your fixes upstream and never on openSUSE branch. The openSUSE branch exists merely for the accepted-ready commits that were already discussed.
* Keep patches local as long as they do not need to be posted upstream and are package-related, outside of the source code tarball.
* Pick only the changes from the upstream that are verified and/or are really needed (e.g. required fix won't get in without related changes).
* **Preferably** add patches only after they were already accepted upstream and passed all the checks they run themselves separately. This is not always the case, but keeps you at the safe side.
* **Always** squash commits per a topic. That is, **one patch (file) per one PR** please. Do not make few `.patch` files, related to the same problem. Do not use `fixup` (discards the commit message), but `squash`. This way you will always keep the commit messages inside one big message of the patch, hence everyone can look up what happened and what commits were actually included. IOW, be verbose in commit messages, be sparse in physical files!

## Package route on build.opensuse.org

# Joining

If you are a member of `salt-maintenance@` team, you should request push access:

* Go to https://github.com/orgs/openSUSE/teams/salt
* Press "Request to join"
* As soon as your request is approved, you can [proceed further steps](#arrow_right-start-here)

:information_source: If the URL above is not available for you, please first become a member of openSUSE organisation. If you _cannot_ push to the repository, you **must** provide a pull request (PR)!

# Submitting patches

An accepted patch is a patch, that is committed in the openSUSE Salt Git repository. Patche can be accepted by Support Team only when:

* There is a clear reproducer of the problem or a good reason to have it (bugfix, security issue etc).
* Fix for it is in the upstream and reviewed _there_ by the upstream developers.
* [Test case is provided](#writing-salt-toaster-tests) (default). Exceptionally, if test case cannot be provided because of a good reason, the support team **must** agree on it.

Note: no need to send the patch upstream to a branch, which is no longer supported by the upstream.

All _reviews and discussions_ about it should happen with the upstream and accepted there, **never** in openSUSE GitHub branch, unless test is missing or upstream no longer supports that branch.


# Salt Packaging Concepts

Salt package meets the requirements of being processed "SUSE traditional way", where patch files are inside the RPM source package. It is impossible do OBS/Git service trick, because the package **must** provide an original `.tar.gz` source tarball and apply patches _on top of it_. Therefore generating all-in-one tarball is _not an option_.

Salt package may contain really a lot patches, that are tracked in the [openSUSE Salt GitHub repository](https://github.com/openSUSE/salt) in the corresponding branch. Patches are generated against a tag, that corresponds to the original released source tarball by the SaltStack.

In order to bring OBS and GitHub so to speak "impedance mismatch", there is a [git-format-pkg-patch](Using-git-format-pkg-patch) tool that you'd _better_ to use, although it is absolutely optional.

General requirements to the package:

- Must provide consecutive changelog
- Must have a minimal diff, where only real changes are reflected.
- All other noise, like bogus patch renames, commit IDs etc are _forbidden_.
- Upstream as much as possible, keep SUSE-only things exceptionally in case upstream has no use of it.

Ready? [**Go!**](Using-git-format-pkg-patch)

# Submitting an SR

## Where
There are three types of Salt package: production, next production and openSUSE. You should submit to all of them, **testing stage**:

1. Production version [`systemsmanagement:saltstack:products:testing`](https://build.opensuse.org/package/show/systemsmanagement:saltstack:products:testing/salt)
2. Next production version: [`systemsmanagement:saltstack:products:next`](https://build.opensuse.org/package/show/systemsmanagement:saltstack:products:next/salt)
3. openSUSE version: [`systemsmanagement:saltstack:testing`](https://build.opensuse.org/project/show/systemsmanagement:saltstack:testing)

**Factory:** Check `systemsmanagement:saltstack/salt` for changes introduced by `openSUSE:Factory`
(Yes, the `openSUSE:Factory` maintainers can introduce changes by themselves just to keep Factory working)

**SLE/ASM:** Corresponding package should be re-submit the same package to the `SUSE:SLE-NN:Update` (`SUSE:SLE-12:Update` at the moment).

## Patch validation

Before you submit your patch, this is your check-list:

1. All Rules from "Submitting patches" section above were satisfied
2. Patch was is proposed to https://github.com/openSUSE/salt as a PR, referring to the Upstream PR and accepted into this Git repository by `salt-maintenance@` team.

:information_source: Pull requests at https://github.com/openSUSE/salt supposed to be discussed **only** with the upstream, unless they are SUSE-only (i.e. `salt.spec` file or similar).

# Accepting an SR

Check list accepting an SR:

1. Verify if patch has been accepted and committed to the https://github.com/openSUSE/salt corresponding branch.
2. Verify if any changes to non-upstream code are correct. They are not tracked by Git!
3. Verify if there was enough time for verification by others.

**IMPORTANT: If any of this fails — do not accept the SR!!**

:warning: If package had a non-Git route changes (e.g. changes to the `salt.spec` file), **always** contact other reviewers to take an additional look and decided whether such change is correct or not. Such changes are _not reflected_ in the Git history!

# Writing [Salt-Toaster](https://github.com/openSUSE/salt-toaster) Tests

[Salt-Toaster](https://github.com/openSUSE/salt-toaster) tests are running integration real-life tests and unit tests. If you provided a patch, there must be at least one of those tests. A patch without a test means someone has to finish your job for you by adding one. Therefore:

* On _stress-less_ run, your patch **must** be covered with any of these type of tests and they **must** pass on Salt-Toaster or unit test run outside of it.
* On _emergency_ run, security fixes or critical L3 that requires a response within a short time may be tested outside and a permanent test can be implemented shortly later after response has been given.

:exclamation: Your check-list, in case no test provided:

- Why no test? ("I have no time", "I am too busy" — doesn't count)
- How do we know your change doesn't break anything?
- How do we know your change will not bring regressions in a future by disappearing over releases?
- You have a good reason for it and you are going to explain it to the whole Support Team.
- You **must** seek a **common agreement** in the team that untested patch is accepted.

## Git routine

### An example adding a new patch to the 2015.8.3 package
* Checkout the `openSUSE-2015.8.3` branch
* Add the fix, using `cherry-pick` of the range from the corresponding upstream branch. In case of conflicts (they usually are normal) fix them in place.
* Commit the fix to the `openSUSE-2015.8.3` branch
* For 2015.8 version run `git format-patch v2015.8.3` against this **tag**. For 2016.11 and ongoing, use [git-format-pkg-patch utility](https://github.com/openSUSE/salt/wiki/Using-git-format-pkg-patch)
* :warning: If you have more than one patch files appeared, squash them into one file, leaving all the commit messages as one big message. See "Changing an existing patch" part below.
* Add the latest `.patch` file to the package
* Build the package
* Test the package
* :warning: Perform the final `git push` **only** if package is built and all the tests passes!

### Changing an existing patch
* Checkout the `openSUSE-2015.8.3` branch
* Run `git rebase -i v2015.8.3` — this will open your `$EDITOR`
* In case to correct something within the commit, change `pick` to `edit` of the patch you want to change, then save and exit your editor
* In case to merge many commits into one, use [`squash` technique](http://gitready.com/advanced/2009/02/10/squashing-commits-with-rebase.html).
* Change the file(s)
* `git add`, `git commit`, `git rebase --continue`
* Run `git format-patch v2015.8.3`
* Move all `.patch` files to the package
* Build the package
* Test the package
* `git push -f` if tests passes. Pushing with `--force` is perfectly OK.

### Upgrade to a new upstream release

Example: New 2015.8.x release

Assumptions:
* Old openSUSE branch: `openSUSE-2015.8.3`, based on `v2015.8.3`
* New release `v2015.8.4`
* New openSUSE branch: `openSUSE-2015.8.4` based on `v2015.8.4`

* `git fetch upstream` to get your repo up-to-date compared to upstream
* `git fetch opensuse` to get your repo up-to-date compared to openSUSE

* `git checkout openSUSE-2015.8.3`
  to go to the `openSUSE-2015.8.3` branch

* `git pull opensuse openSUSE-2015.8.3`
  to get up-to-date with the openSUSE patches

* `git checkout 2015.8`
  to go to the `2015.8` branch

* `git pull upstream 2015.8`
  to get the latest branch state from upstream

* `git push opensuse 2015.8`
  if it's a new upstream 'major release branch' not existing in opensuse yet

* `git checkout v2015.8.4`
  to place git HEAD to the upstream release tag
  if this fails, do another `git fetch`, this will import tags from the 2015.8 branch

* `git checkout -b openSUSE-2015.8.4`
  to create the new openSUSE release branch

* `git cherry-pick v2015.8.3..openSUSE-2015.8.3`
  to move all patches from the old openSUSE release branch to the new one.
  This will automagically detect patches that were merge upstream.
  You might run into conflicts if a openSUSE patch touches the same code area as an upstream commit.
  Go fix these conflicts, then `git add ...`, `git cherry-pick --continue`.

  **Don't** `git cherry-pick --allow-empty`, this will add empty commits. Rather do `git reset` followed by `git cherry-pick --continue`.

* `git format-patch v2015.8.4`
  to generate the new patches files in order to review them.
  Sometimes, especially when changes are done upstream in the same area of code, it might happen that Git automagically resolves a conflict and produces an invalid patch, which contains only some parts of the original patch, and maybe it should no be applied.
  It's interesting to review all new patches in order to detect some inconsistency and if they are still applying.

  If necessary, we can **drop** invalid patches with: `git rebase -i v2015.8.4` and marking them with **drop**

* `git push opensuse openSUSE-2015.8.4`
  to push the new branch to opensuse