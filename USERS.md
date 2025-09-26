# Gershwin GNUstep Users

## Overview

 ZFS datasets for Local and Network must be created in advance for this to work properly as described in ZFS.md.  NIS/NFS must be setup in advance as documented in NETWORK.md for Network user accounts.  Eventually this will be automated with StepInfo siutil or something similar.

The following is an example of what a properly configured `/etc/adduser.conf` might look like that reflects the Gershwin GNUstep layout.

/etc/adduser.conf
```
defaultHomePerm=0700
defaultLgroup=
defaultclass=
defaultgroups=
passwdtype=yes
homeprefix=/Local/Users
defaultshell=/usr/local/bin/zsh
udotdir=/usr/share/skel
msgfile=/etc/adduser.msg
disableflag=
uidstart=1001
```

Note if `/etc/adduser.conf` exists and you do not want to modify `adduser -N` may be used.

Create a Local User (Applies to Local Machine Only)
```
# adduser
```

Create a Network User (Must be done from Server)
```
# adduser
```
Using jmaloney for example when promoted for `Home directory [/Local/Users/jmaloney]:` Change `/Local/` to `/Network/` instead:
```
/Network/Users/jmaloney
```
