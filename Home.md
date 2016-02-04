# openSUSE salt packaging

This repository tracks [upstream salt](https://github.com/saltstack/salt) and the patches for the [openSUSE salt package](https://build.opensuse.org/package/show/systemsmanagement:saltstack/salt).

See [here](https://github.com/openSUSE/salt/wiki/Git-.config-for-working-with-remote-repositories) for working with remote git repositories.
## Branches

* `develop` - upstream development
* `2015.8` - upstream branch for 2015.8.X bugfixes
* `openSUSE-2015.8.3` - openSUSE branch for the [openSUSE salt package](https://build.opensuse.org/package/show/systemsmanagement:saltstack/salt)

The `openSUSE-2015.8.3` branch is based on the `v2015.8.3` upstream tag. Commits on the `openSUSE-2015.8.3` branch apply cleanly on the `salt-2015.8.3.tar.gz` release tarball.

## Workflow

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

* `git pull upstream 2015.8`
   get the latest branch state from upstream

* `git checkout v2015.8.4`
  place git HEAD to the upstream release tag

* `git checkout -b openSUSE-2015.8.4`
  create the new openSUSE release branch

* `git cherry-pick v2015.8.3..openSUSE-2015.8.3`
  move all patches from the old openSUSE release branch to the new one
  this will automagically detect patches that were merge upstream
  you might run into conflicts if a openSUSE patch touches the same code area as an upstream commit
  go fix this conflict, then `git add ...`, `git cherry-pick --continue` resp. `git cherry-pick --allow-empty`

* `git push opensuse openSUSE-2015.8.4`
  pushes the new branch to opensuse