# Gershwin build system

The build system for Gershwin was designed to package a self containted System for multiple operating systems both locally and in github.

## Bootstrapping

It starts with bootstrap scripts that use functions.sh to detect with kernel or distro release is running.  To faciliate this requirements/arch.txt is used for example or requirements/freebsd.txt where requirements/ghostbsd.txt is just a symlink since they have different kernel names but the package requirements remain the same.  It might make sense to change requrements to requirements-build and requirements-runtime.  This would produce lighter ISO installations without the requirement of developer packages.

## Checkout

The checkout system is designed to checkout a list of reposistories defined in the script currently.  It would be nice to support https and git.  Since all of the repos are cloned to a repos/ folder that is ignored by .gitignore.  This can aid in development by allowing commits to be made and tested on a local system before pushing without affected gershwin-build repo itself.  Adding more repos is just a matter of adding to the file they all clone the same way using the same command.

## Building

The building is mostly done in install-system-domain.sh.  This was created in case we ever want to install some things in local domain from a 2nd script.  Everything for the layout itself starts with tools-make.

```
cd "$REPOS_DIR/tools-make"
./configure \
  --with-config-file=/System/Library/Preferences/GNUstep.conf \
  --with-layout=gershwin \
  --with-library-combo=ng-gnu-gnu
$MAKE_CMD || exit 1
$MAKE_CMD install
$MAKE_CMD clean
$MAKE_CMD distclean
```

The layout is defined by `--with-layout=gershwin` which resides under tools-make/FileSystems/gershwin.  The Tools-make GNUmakefile and configure process this and translate into `GNUstep.conf`.  The install location is controlled by `--with-config-file=/System/Library/Preferences/GNUstep.conf`.  

The library combo flag tells gmake to use CLANG + libobjc2 rather than GCC objective C.  

https://mediawiki.gnustep.org/index.php/Building_GNUstep_with_Clang

The MAKE_CMD is variablized for different reasons because GNU make is required and arch has it has make, where FreeBSD has it is gmake.  Also it determines how many CPUs are presnet in a system to build as fast as possible with all available cores whether running on Linux or FreeBSD.

For libobjc2 because it is not packaged on Linux most of the time we build ourselves and this flags ensure it installs properly within the System domain.  The install type ensures that /System/Library will be used vs /System/lib, etc.  The Release means build without debugging.  Compiler is set to CLANG.  The final option if set to off with libsblocksruntime would try to use libdispatch for blocks runtime.  This requires a compiler-rt that is newer than what is present in FreeBSD packaging.  For now we use libobjc2 blocks runtime since this may be too experimental anyway and results are unknown.

```
cmake .. \
  -DGNUSTEP_INSTALL_TYPE=SYSTEM \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_C_COMPILER=clang \
  -DCMAKE_CXX_COMPILER=clang++ \
  -DEMBEDDED_BLOCKS_RUNTIME=ON
  ```

The rest of the examples are pretty generic configure, gmake, gmake install until Gershwin global defaults until finally we get to things that do not need configure.

```
cd "$REPOS_DIR/libs-base"
./configure
$MAKE_CMD -j"$CPUS" || exit 1
$MAKE_CMD install
$MAKE_CMD clean

cd "$REPOS_DIR/libs-gui"
./configure
$MAKE_CMD -j"$CPUS" || exit 1
$MAKE_CMD install
$MAKE_CMD clean

cd "$REPOS_DIR/libs-back"
export fonts=no
./configure
$MAKE_CMD -j"$CPUS" || exit 1
$MAKE_CMD install
$MAKE_CMD clean

cd "$REPOS_DIR/gershwin-globaldefaults"
mkdir -p /System/Library/Preferences/GlobalDefaults
cp -R System/Library/Preferences/GlobalDefaults/* /System/Library/Preferences/GlobalDefaults/

cd "$REPOS_DIR/gershwin-workspace"
./configure
$MAKE_CMD -j"$CPUS" || exit 1
$MAKE_CMD install
$MAKE_CMD clean

cd "$REPOS_DIR/gershwin-systempreferences"
$MAKE_CMD -j"$CPUS" || exit 1
$MAKE_CMD install
$MAKE_CMD clean

cd "$REPOS_DIR/gershwin-rik-theme"
$MAKE_CMD -j"$CPUS" || exit 1
$MAKE_CMD install
$MAKE_CMD clean
```

After new repos are added to checkout.sh the commands have to be added here for now to run the steps to build additional repos.  Any additional repos added should contain isolation to /System only.  Things that install outside of /System should be handled in ISO package scripts or OS packages instead preferably.  This is so system.txz can be easily packaged, extracted as one artifact by the build system.  A local installatioon script and local.txz would be acceptable as well.

## Adding new distros

This mostly should be a matter of adding to requirements/*.txt as well as bootstrap.sh and function.sh.  We should abstract at some point into a more singular method but this is what is in place for now at the time of writing.  
