Remapping Fork Patches
======================

This repo is based on Paper 1.16.5. It can be used to remap patches of Paper forks.

This branch is specifically and only for fork remapping. This is not for Paper development or for general use. It
requires special steps and special set up. Follow the instructions in this readme, don't ask for help if you haven't
read this entire readme and followed all of these steps.

Remapping patches requires knowledge of how to fix issues when patches don't or can't apply correctly. Patches not
applying correctly is not an issue with this repo or paperweight. You must fix them and continue the process manually
yourself. This is identical to any other upstream update.

Setup
-----

Paper's unmapped 1.16.5 patches are in [`patches/server-unmapped/0001`](https://github.com/PaperMC/Paper/tree/mappings/mojang/base/patches/server-unmapped/0001).
Place the patches you want to remap in `0002`. Paperweight will only remap patches placed in the last directory - for
example if there are directories `0001`, `0002`, and `0003`, only the patches in `0003` will be remapped.

If you are remapping patches for a fork of a fork, place you other upstream patches in `0002`, and your patches in `0003`.

Pre-remapping
-------------

The patch remap task has no registered inputs, to make working with it repeated easier. That means if you run the task
by itself, it will fail.

Before you start remapping patches, you must first set up the patch environment by running:

```shell
./gradlew applyPatches
```

You will only need to do this once to set up the environment before you start remapping.

Remapping
---------

To start remapping patches, run the `remapPatches` task:

```shell
./gradlew remapPatches
```

This can take a very long time, hours even, as patch remapping is a very complex and CPU & disk intensive task. How long
it takes depends entirely on how many patches there are to be remapped, and how powerful your computer is.

That said, while you may need to walk away during the process, the remapping process does require some amount of
hands-on work to get it to complete. If you leave for a while and come back to a patch failure, you'll need to fix the
issue and restart paperweight to pick up from where it left off, explained below.

### Fixing patch application issues

It is pretty unlikely that patch remapping will succeed first try. Paper's patches have already been fixed to apply
correctly on this branch, but either your patches or your upstream patches are likely to fail in one way or another due
to minor differences in tooling, setup, etc. Again, it is your responsibility to handle patch application issues as they
come up just like any other upstream update.

While the process of fixing issues with patch application is the same, how to resume and complete the process is
different, as it hopefully goes without saying that patch remapping is a more complex process which requires extra work
vs just a simple patch update.

### Patch remap working directory setup

There are a variety of files and directories that paperweight uses during patch remap, but only one directory that you
need to worry about. This is the directory where patches are applied, and code is remapped.

```
<project_root>/.gradle/caches/paperweight/patch-remap-input
```

That directory contains what would be the `Paper-Server` directory during the patch remapping process.

### Apply patch fixes

If a patch fails to apply, go to the `patch-remap-input` directory to fix the issue, like you normally would. This repo
contains the `apatch.sh` script in the `scripts` directory if you want to use it.

Once you have fixed the issues in patch application, and are ready to continue, run `am --continue` in the
`patch-remap-input` directory:

```shell
git am --continue
```

Each patch is applied in its own `am` session, so this won't cause any of the other patches to incidentally apply. After
the `am` command in the above `patch-remap-input` directory is completed, continue the remapping process with:

```shell
./gradlew remapPatches --continue-remap
```

Due to the `--continue-remap` flag paperweight will pick up where it left off. If the patch that failed is in a
pre-remap upstream patch set, it will simply continue applying the other patches. If the patch is in the remap patch
set, paperweight will record the patch, remap it, and re-apply it on the remap branch as it normally would, then
continue applying the rest of the patches from there.

### Completed patch remap

After the process is complete and all patches have been remapped, the new remapped patches (only including the remap
patch set explained above) will be generated into `patches/server-remapped`.

Help, something went wrong
--------------------------

If something goes wrong, it's almost certainly a patch application issue discussed above. We will not support or help
you work through these kinds of issues. Dealing with patch breakages is part of the process of maintaining a fork, so if
you don't already know what to do, don't expect us to teach you how to do it.

If there is an error in paperweight that is causing some issue other than patch application, then we can look into
figuring out what's wrong and fixing it. Please, *please*, read through everything here first and follow the
instructions word for word. Do not try to simplify the process. Do not make up your own steps. If you do, again, we will
absolutely not help you.
