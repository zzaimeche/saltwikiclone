## How to suck less at packaging

When packaging Salt package, you generate your patches with `git format-patch vYYYY.MM.DD` tag. But this has a number of limitations, like Git generates patches with a prefix order number, which shifts overtime to something else, being _the same_ patch. This leads to the unpleasant consequences, violating [patch lifecycle](https://en.opensuse.org/openSUSE:Packaging_Patches_guidelines#Patch_life_cycle) and [naming guidelines](https://en.opensuse.org/openSUSE:Packaging_Patches_guidelines#Patch_naming), generating lots of renames. The rename is not working as a rename per se but as "add-remove". That is, if you rename patch from `001-foo.patch` to `003-foo.patch` which is _the same_ patch but with the different order number, the OBS will have to a) remove `001-foo.patch` and then b) add `003-foo.patch` for it. You then will need to adjust `.spec` file accordingly and generate your `.changelog` right. After your submit request (SR) to the OBS, your reviewer will see "added patch" and "removed patch" in the a) change log and b) OBS commit diff. While this information _technically_ absolutely valid, _practically_ it makes zilch zero sense to anyone.

So in order to package better, you can use **Git Packaging Tools** ([git src](https://github.com/openSUSE/git-packaging-tools), [man page](https://github.com/openSUSE/git-packaging-tools/blob/master/doc/git-format-pkg-patch.md), [package](https://build.opensuse.org/package/show/home:bmaryniuk/git-packaging-tools)).

### Procedure

1. Create your patches (example) in a new, empty sub-directory of your tracked package in the Git, say `/home/your/patches-from-git`:

   `git-format-pkg-patch -f v2015.8.12`

   This will create a set of patches, but without ordering numbers. You will find in the same directory a file, called `patches.orders.txt` which contains their ordering and applying within the `.spec` file. You can change its name with an option `-o`, if you like. The content of this file is fully working and is ready to be included into the `.spec` file, although still missing your [patch description](https://en.opensuse.org/openSUSE:Packaging_Patches_guidelines#Type_1:_minimal_single-line_comment_in_spec_file), according to the guidelines, e.g.:

   ```
   Patch1: fix-for-opensuse-specific-things.patch
   Patch2: fix-for-sle-specific-things.patch
   Patch3: fix-for-upstream-sources.patch
   ```


2. Checkout your package somewhere:

   `obs bco [your:project:somewhere](https://github.com/openSUSE/salt/wiki/Workflow#where) packagename`

3. Update patches by one go:

   `git-format-pkg-patch -u /home/your/patches-from-git/ -c`

   This will replace existing patches with a new content, if needed. As well will add/remove patches that changed.

4. Find pre-formatted changes for your `.changelog` in the file `patches.changes.txt` what was removed, what was added and what was changed, if anything.

5. Remix your patch ordering file so it will contain a proper patch descriptions:

   ```
   git-format-pkg-patch -s yourpackage.spec \
                         -o /home/your/patches-from-git/patches.orders.txt \
                         > patches.described.txt
   ```

   Now the file `patches.described.txt` will contain the section that looks [according to the guidelines](https://en.opensuse.org/openSUSE:Packaging_Patches_guidelines#Type_1:_minimal_single-line_comment_in_spec_file), e.g.:

   ```
   # PATCH-FIX-OPENSUSE fix-for-opensuse-specific-things.patch bnc#123456
   Patch1: fix-for-opensuse-specific-things.patch
   # PATCH-FIX-SLE fix-for-sle-specific-things.patch bnc#123456
   Patch2: fix-for-sle-specific-things.patch
   # PATCH-FIX-UPSTREAM fix-for-upstream-sources.patch bnc#123456
   Patch3: fix-for-upstream-sources.patch
   ```

6. Open the [best editor](http://duenenhof-wilhelm.de/church/size.html) and replace the information in the `.spec` file from `patches.described.txt`, `patches.orders.txt` if you need anything.

7. Update your `.changes` with the content of `patches.changes.txt`:

   `osc vc`

8. Finalise everything:

   `osc addremove; osc commit`

