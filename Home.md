# openSUSE Salt Packaging

This repository tracks [upstream salt](https://github.com/saltstack/salt) and the patches for the [openSUSE salt package](https://build.opensuse.org/package/show/systemsmanagement:saltstack/salt).

See [here](https://github.com/openSUSE/salt/wiki/Git-.config-for-working-with-remote-repositories) for working with remote git repositories.
## Branches

* `develop` - upstream development
* `2015.8` - upstream branch for 2015.8.X bugfixes
* `openSUSE-2015.8.3` - openSUSE branch for the [openSUSE salt package](https://build.opensuse.org/package/show/systemsmanagement:saltstack/salt)

The `openSUSE-2015.8.3` branch is based on the `v2015.8.3` upstream tag. Commits on the `openSUSE-2015.8.3` branch apply cleanly on the `salt-2015.8.3.tar.gz` release tarball.

## Workflow

### Policies

* :warning: Always try to keep patches merged to the upstream, if your patch is related to the source. If upstream does not likes the fix, try to find a compromise and try having no patches that aren't upstream. 
* Patches do not need to be upstream, if they are related merely to the package (e.g. SUSE systemd scripts etc) and they do not need to be posted upstream.
* Pick only the changes from the upstream that are verified and/or are really needed (e.g. required fix won't get in without related changes).

### Request push access
* Go to https://github.com/orgs/openSUSE/teams/salt
* Press "Request to join"
* As soon as your request is approved, you can continue with the steps below

### Adding a new patch to the 2015.8.3 package
* Checkout the `openSUSE-2015.8.3` branch
* Add the fix
* Commit the fix
* Run `git format-patch v2015.8.3`
* Add the latest `.patch` file to the package
* Build the package
* Test the package
* `git push` if tests pass

### Changing an existing patch
* Checkout the `openSUSE-2015.8.3` branch
* `git rebase -i v2015.8.3` - this will open your `$EDITOR`
* Change `pick` to `edit` of the patch you want to change - save and exit your editor
* Change the file(s)
* `git add`, `git commit`, `git rebase --continue`
* Run `git format-patch v2015.8.3`
* Move all `.patch` files to the package
* Build the package
* Test the package
* `git push -f` if tests pass

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

* `git checkout v2015.8.4`
  to place git HEAD to the upstream release tag

* `git checkout -b openSUSE-2015.8.4`
  to create the new openSUSE release branch

* `git cherry-pick v2015.8.3..openSUSE-2015.8.3`
  to move all patches from the old openSUSE release branch to the new one.
  This will automagically detect patches that were merge upstream.
  You might run into conflicts if a openSUSE patch touches the same code area as an upstream commit.
  Go fix these conflicts, then `git add ...`, `git cherry-pick --continue` resp. `git cherry-pick --allow-empty`

* `git push opensuse openSUSE-2015.8.4`
  to push the new branch to opensuse