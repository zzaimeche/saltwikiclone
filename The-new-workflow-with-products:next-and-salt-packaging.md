1. Create patch in the usual way (from openSUSE/salt repo version branch)
2. Copy and add patch to salt-packaging
3. Edit spec file in salt-packaging (TODO: could be a service)
4. Commit the changes to branch `oxygen-rc1`. Commit message should look like this:

```
   Add fqdns to grains (bsc#1063419)
   Added:                     # TODO: could be a service                                           
     * add-fqdns-grains.patch # --                                                                 
```

5. osc service disabledrun                                                                                 
6. Fix formatting of salt.changes # TODO: fix the changelog update service to eliminate this step  
7. osc add/commit changes                                                                          
8. Optionally build via: osc build foo                                                             


TODO:
- osc service run does not remove files (e.g. patches)   