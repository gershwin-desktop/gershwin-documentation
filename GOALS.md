## Gershwin Project Goals

This document is outline design decisions and requirements for contributing to help ensure that contributions align with project goals.

### Self contained in /System

* Fonts that Gershwin will use will be installed in /System/Library/Fonts
    * Gershwin will not use Fonts from /usr/share/fonts
    * We do not show ugly fonts that the distribution may have chosen to install
    * We provide a consistent experience no matter which underlying OS is used
    * In /System/Library/Scripts/Gershwin.sh we override fontconfig variables as the mechanism to do this without touching the operating systems configuration files for external tools
* Tools will be developed for things like managing users
    * We do not want to have to modify things like /etc/adduser.conf to manage users
    * We do not want to touch existing user accounts
    * We will make sure new users are able to be created with the same tool no matter what the underlying OS is
* Technologies used in our stack we hope to replace in the future
    * dbus
    * We do not want to add further features that leverage these technologies
    * If anything we would like to create replacements that intercept them for non native applications and work how we need them to for native applications
* Other typical desktop technologies we would like to avoid and provide alternative solutions for
    * Pipewire
        * We would prefer to focus on OSS/ALSA and more agnostic technologies, however we might not reject pull requests to add support for as long as the PRs do not break building on environments or force the use of whereas OSS might be a perfect viable option someone on FreeBSD wants to use.
        * Breaks important applications such as Reaper
        * It’s not exactly a “core audio” type solution we would prefer to implement better for Gershwin
        * Like dbus if anything we would like to create replacements that intercept them for non native applications and work how we need them to for native applications
    * Anything freedesktop related
        * polkit, pkg-config, xdg-user-dirs, etc
        * Many of these tools are low hanging fruit opportunities that we would much rather provide our own native better integrated solutions for to ensure Gershwin pulls in only dependencies needed to build and run GNUstep.  

Why do we do this?  The eventual goal is so that Gershwin requires more or less nothing a but a kernel to run and provide the same experience across any hardware device or underlying operating system it runs on.  We do not want the experience to be drastically different whether it is a FreeBSD system, Linux, Windows, etc.  At the moment Gershwin has a self contained install of under 50MB.  We aim to keep it small and building well on low spec machines.  The next goal is also part of reiterating this goal.

### GNUstep objective C native

* We do not want to ship any tool written in any other language that is not native to GNUstep in /System
    * Aside from simple shell scripts in /System/Library/Scripts in #!/bin/sh format which are currently necessary to start the desktop or provide other important functions
    * We do not want to hardcode shell scripts either to use zsh, bash or either shells that could break posix compatibility or require additional dependencies, etc.

What does this mean to the user experience?  We can build the entire system in less than a few minutes on the lowest spec hardware, and it will consume less than 50MB of storage.  This makes Gershwin ideal for it’s goal to run any hardware device using nearly any OS.  


### No patching GNUstep core libs in Gershwin

* We have tickets open to address where we have made small modifications that will be ported back to GNUstep
    * Such as examples include being able to restore applications by clicking a GNUstep app from the dock
        * This was an example of a quick modification that was made to provide a proof of concept of Gershwin for technology previews
        * Moving forward we will see whether it is feasible to move this functionality in Workspace instead or, make PR to put the behavior behind an option which is the standard way.

We want to be able to provide multiple release versions of libs-base, libs-gui and so on in the future to ensure long term compatibility for applications built as GNUstep releases new versions.  Forking the core libs hinders our ability to do this properly as a project.

### Consistent build systems

* We must avoid pulling in external resources such as fonts, audio files, using tools outside of git
* We currently typically publish overlays to gershwin-system but this may change in the future if we need to have a dedicated repo for fonts etc
* The reasoning for this is FreeBSD packaging for example in works best when all resources are within our organization, and in our repos
* We can ensure a consistent experience between packaging and building from source by not relying on outside tarballs, and cp -R methods that make things difficult for packagers.

The goal here is to make keep gershwin-build simple, and also keep it simple for packagers to package our repos.  We want as many users using Gershwin as possible, and do not want to increase the level of effort for packagers to study gershwin-build to determine how to individually package each repo as needed.

### Cloud & AI free

It should go without saying that we  do not want any technology that would require user sign in, or anything that would violate a users privacy, reveal location, take automated screenshot of activities, provide automated reports of how long a user performs tasks on their devices.

* We do not want to ship a software store that would require sign in or the user to identify whom they are
* We do not want a Weather app that or installer that automatically determines a users location without the user requesting to do so
* We do not want AI baked into any software Gershwin software
    * It is of course okay to use copilot or other tools to review contributions, fix software we just don't want Gershwin software itself include AI features