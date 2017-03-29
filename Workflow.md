# Workflow

## Policies

* :warning: **Always** try to keep patches merged to the upstream, if your patch is related to the source. If upstream does not likes the fix, try to find a compromise and try having no patches that aren't upstream. 
* :warning: **Always** open Pull Requests for your fixes upstream and never on openSUSE branch. The openSUSE branch exists merely for the accepted-ready commits that were already discussed.
* Keep patches local as long as they do not need to be posted upstream and are package-related, outside of the source code tarball.
* Pick only the changes from the upstream that are verified and/or are really needed (e.g. required fix won't get in without related changes).
* **Preferably** add patches only after they were already accepted upstream and passed all the checks they run themselves separately. This is not always the case, but keeps you at the safe side.
* **Always** squash commits per a topic. That is, **one patch (file) per one PR** please. Do not make few `.patch` files, related to the same problem. Do not use `fixup` (discards the commit message), but `squash`. This way you will always keep the commit messages inside one big message of the patch, hence everyone can look up what happened and what commits were actually included. IOW, be verbose in commit messages, be sparse in physical files!

## Package route on build.opensuse.org

### Submitting patches

Patches can be accepted by support team only when:
  
* There is a clear reproducer of the problem.  
* Fix for it is sent to upstream as a pull request
* All discussions about it is happening with the upstream.

### Preparing package

You can do it all manually, if you like. However there is a [better way](Using-git-format-pkg-patch) that sucks less.

### Submitting an SR

* Only submit to `systemsmanagement:saltstack:testing/salt`
* :warning: Check `systemsmanagement:saltstack/salt` for changes introduced by `openSUSE:Factory`
  (Yes, the `openSUSE:Factory` maintainers can introduce changes by themselves just to keep Factory working)

Submit request can be valid only if :

* All Rules from "Submitting patches" section above were satisfied
* In case no direct access to the repository, patch was is proposed to https://github.com/openSUSE/salt as a PR, referring to the Upstream PR.

:warning: Please do not discuss pull requests at https://github.com/openSUSE/salt as they already has supposed to be discussed at upstream.

### Submitting to SLE

:bomb: **WARNING: Current process is unstable, is subject to change!**

Before [FATE#318875](https://fate.suse.com/318875) is implemented, procedure of submitting request for a final enterprise packages is as follows:

1. Submit SR as always to [systemsmanagement:saltstack:products](https://build.opensuse.org/package/show/systemsmanagement:saltstack:products/salt) for currently used version (2015.8.7) and [systemsmanagement:saltstack:products:next](https://build.opensuse.org/package/show/systemsmanagement:saltstack:products:next/salt) for upcoming version (2015.8.12).

2. After it got accepted, just re-submit the same package to the `SUSE:SLE-12:Update`.

After [FATE#318875](https://fate.suse.com/318875) is applied, there will one only one single source where to submit requests and the second step above will not be needed anymore.

### Accepting an SR

* **Never** accept an SR right away after it was created. Always give enough time for verification of it.
* If package had a non-Git route changes (e.g. changes to the `salt.spec` file), **always** contact other reviewers to take an additional look and decided whether such change is correct or not. Such changes are _not reflected_ in the Git history!

### Writing [Salt-Toaster](https://github.com/openSUSE/salt-toaster) Tests

[Salt-Toaster](https://github.com/openSUSE/salt-toaster) tests are running integration real-life tests and academic unit tests. If you provided a patch, there needs to be at least one of those test types.

* On _stress-less_ run, your patch **must** be covered with any of these type of tests and they **must** pass on Salt-Toaster or unit test run outside of it.
* On _emergency_ run, security fixes or critical L3 that requires a response within a short time may be tested outside and a permanent test can be implemented shortly later after response has been given.

Anyone who missed to provide a proper test to his patch, owes a beer and [Schäufele](https://en.wikipedia.org/wiki/Sch%C3%A4ufele) to the team member who fixed that for him. :smile: 


## Git routine

### Request push access
* Go to https://github.com/orgs/openSUSE/teams/salt
* Press "Request to join"
* As soon as your request is approved, you can continue with the steps below

:warning: If the URL above is not available for you, please first become a member of openSUSE organisation. If you _cannot_ push to the repository, you _must_ provide a pull request (PR)!

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