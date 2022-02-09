The `salt-doc` package is one of the subpackages built from `salt.spec` file.

The content of this package is provided in the `html.tar.bz2` tarball that we have in the "salt" OBS/IBS packages together with the rest of the files. For example here: https://build.opensuse.org/package/show/systemsmanagement:saltstack:products/salt

This tarball *is not* generated during build time of the package, and therefore we need to take care of updating the content at least when we do a major version update to Salt.

To regenerate the `html.tar.bz2` simply execute the `update-documentation.sh` script that we have in OBS as follows:

```console
# sh update-documentation.sh salt-maintainers@suse.de --without-sphinx
```

This will update the `html.tar.bz2` based on the current state contains the generated documentation.
