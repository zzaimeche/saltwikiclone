## How to suck less at packaging

When packaging Salt package, you generate your patches with `git format-patch vYYYY.MM.DD` tag. But this has a number of limitations, like Git generates patches with a prefix order number, which shifts overtime to something else, being _the same_ patch. This leads to the unpleasant consequences, violating [patch lifecycle](https://en.opensuse.org/openSUSE:Packaging_Patches_guidelines#Patch_life_cycle) and [naming guidelines](https://en.opensuse.org/openSUSE:Packaging_Patches_guidelines#Patch_naming), generating lots of renames. The rename is not working as a rename per se but as "add-remove". That is, if you rename patch from `001-foo.patch` to `003-foo.patch` which is _the same_ patch but with the different order number, the OBS will have to a) remove `001-foo.patch` and then b) add `003-foo.patch` for it. You then will need to adjust `.spec` file accordingly and generate your `.changelog` right. After your submit request (SR) to the OBS, your reviewer will see "added patch" and "removed patch" in the a) change log and b) OBS commit diff. While this information _technically_ absolutely valid, _practically_ it makes zilch zero sense to anyone.

So in order to package better, you can use **Git Packaging Tools** ([git](https://github.com/openSUSE/git-packaging-tools), [map page](https://github.com/openSUSE/git-packaging-tools/blob/master/doc/git-format-pkg-patch.md), [package](https://build.opensuse.org/package/show/home:bmaryniuk/git-packaging-tools)).

### Procedure

1. Checkout your package:

   `obs bco your:project:somewhere packagename`
