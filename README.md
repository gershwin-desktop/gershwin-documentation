# gershwin-documentation
Gershwin is a desktop environment based on GNUstep.  It is designed to provide an early Mac OS X-like user experience with an aim to also innovate.  The domains documentation in this repo is a good first example of this.

Gershwin aims to be different from your typical desktop environment project.  Here are some key examples of technology differences that Gershwin will use versus typical free and open source desktop environments:

* GCC -> CLANG
* GTK -> GNUstep/Swift
* DBUS -> GNUstep Distributed Objects
* Flatpaks -> Application Bundles

This repository contains the official documentation for Gershwin, covering both conceptual design and practical configuration. The initial documentation here will be mostly developer, contributor, tester facing.  However these are also intended to provide historical insight for interested end users as to how the project is being developed.

## Table of Contents

- **[Gershwin GNUstep Domains](DOMAINS.md)** – Explanation of Gershwin’s NeXTSTEP-inspired domain model and how the system is organized.  
- **[Gershwin File System Layout](FILESYSTEMLAYOUT.md)** – Overview of the proposed filesystem structure and environment variables mapping to Gershwin’s domains.
- **[Gershwin ZFS Dataset Configuration](ZFS.md)** – Steps to configure ZFS datasets (`/System`, `/Local`, `/Network`) for Gershwin installations to preserve data across boot environments.  
- **[Gershwin GNUstep Users](USERS.md)** – Instructions for configuring user accounts in the Gershwin environment (setup for local vs. network users).  
- **[Gershwin GNUstep Network Domain](NETWORK.md)** – Guide to sharing applications, libraries, and user accounts over a network using NIS/NFS.  

## License

This documentation is licensed under the **BSD 2-Clause License** (see the [LICENSE](LICENSE) file for details).
