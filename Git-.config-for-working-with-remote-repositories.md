My `.git/config` looks like this

```
[remote "origin"]
        url = git@github.com:kkaempf/salt.git
        fetch = +refs/heads/*:refs/remotes/origin/*
[remote "upstream"]
        url = git@github.com:saltstack/salt.git
        fetch = +refs/heads/*:refs/remotes/upstream/*
        fetch = +refs/tags/*:refs/tags/*
[remote "opensuse"]
        url = git@github.com:openSUSE/salt.git
        fetch = +refs/heads/*:refs/remotes/opensuse/*
```

This allows me to update the `develop` branch of `openSUSE/salt` from `saltstack/salt` like this

```
git checkout develop
git pull upstream develop
git push opensuse develop
git push --tags opensuse develop
```

The same goes for the `2015.8` branch, just replace `develop` with `2015.8` in the above commands.