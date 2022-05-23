### Git

- `git rebase -i $COMMIT_SHA` let's you modify the Git log interactively.
  `--autostash` takes care of saving & restoring your current working directory
  state before respectively after rebasing. See `man(1) git-rebase` for more details.
- `git cherry-pick -x $COMMIT_SHA` creates a new commit in the currently active
  branch with the contents of the commit specified by `$COMMIT_SHA`. `-x` puts
  a reference to the original commit into the commit message of the new commit.
  See `man(1) git-cherry-pick` for more details.
- `git bisect` helps pinpoint a commit that introduces breaking changes. See
  `man(1) git-bisect` for more details.

### Salt Bundle

#### Diffs, Listing Packages, Aligning Sub-projects

This [bash script](https://gist.github.com/agraul/067922320b9d429aec744b48990120ff) is useful to list all packages in the bundle sub-project, look at the `osc rdiff` for each of them in the respective subproject of the two defined parent projects and for `osc copypac` or `prjconf` alignments.
