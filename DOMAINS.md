# Gershwin GNUstep Domains: A Modern Take on NeXTSTEP's Elegant Architecture

## Introduction: The Lost Art of System Organization

In the early days of NeXTSTEP and OpenSTEP, computer systems were organized around a brilliant concept called **domains**. This wasn't just about where files lived on disk—it was a comprehensive mental model for understanding how different parts of the system related to each other, who controlled what, and how users, administrators, and the system itself could coexist harmoniously.

Apple's Rhapsody began to carry this torch forward, but Mac OS X ultimately abandoned much of this elegant organization in favor of a simpler (but less powerful) approach. The result? We lost one of computing's most thoughtful approaches to system architecture.

This document explains how Gershwin can bring back and enhance this domain model, creating a system that's both more organized and more powerful than what we see in modern operating systems.

## The Domain Mental Model: Four Worlds, One System

Think of your computer as having four distinct "worlds" or **domains**, each with its own purpose and authority:

### 1. **System Domain** (`/System/`)
- **Who controls it:** The operating system itself
- **What it contains:** Core system components that make the computer work
- **When it changes:** Only during OS updates or system maintenance
- **Mental model:** "The foundation that everything else builds upon"

### 2. **Network Domain** (`/Network/`)
- **Who controls it:** Network administrators
- **What it contains:** Applications and resources shared across multiple computers
- **When it changes:** When administrators want to deploy software organization-wide
- **Mental model:** "The shared company resources everyone can access"

### 3. **Local Domain** (`/Local/`)
- **Who controls it:** Local administrators (the person who owns the computer)
- **What it contains:** Applications and resources for all users of this specific machine
- **When it changes:** When you install software for everyone on your computer
- **Mental model:** "The stuff that belongs to this computer itself"

### 4. **User Domain** (`~/` - your home directory)
- **Who controls it:** Individual users
- **What it contains:** Personal applications, preferences, and data
- **When it changes:** Whenever you customize your personal environment
- **Mental model:** "Your personal space that only you control"

## Why This Matters: The Power of Clear Boundaries

Modern systems often throw everything into a few giant directories (`/usr/bin`, `/Applications`, etc.) This creates confusion:
- Where should I install this application?
- Why can't I remove this software?
- How do I share settings across users?
- What happens if I mess up my system configuration?

The domain model answers these questions before you even ask them. Each domain has clear rules about who can modify it and what belongs there.

## The Directory Structure: Where Everything Lives

Based on your proposed layout, here's how the four domains organize themselves:

### System Domain: The Untouchable Foundation
```
/System/
├── Applications/           # Core system applications
│   └── Admin/             # System administration tools
└── Library/               # Everything else the system needs
    ├── Tools/             # Command-line utilities
    │   └── Admin/         # System admin command-line tools
    ├── Libraries/         # System frameworks and libraries
    ├── Headers/           # Development headers
    ├── Documentation/     # System documentation
    │   ├── man/          # Manual pages
    │   └── info/         # Info documents
    ├── WebApplications/   # System web services
    └── Makefiles/         # Build system configuration
```

### Network Domain: Shared Across the Organization
```
/Network/
├── Applications/          # Apps available to all network users
│   └── Admin/            # Network administration applications
└── Library/              # Shared network resources
    ├── Tools/            # Shared command-line tools
    │   └── Admin/        # Network admin tools
    ├── Libraries/        # Shared frameworks
    ├── Headers/          # Shared development resources
    ├── Documentation/    # Shared documentation
    │   ├── man/
    │   └── info/
    └── WebApplications/  # Network web services
```

### Local Domain: This Computer's Resources
```
/Local/
├── Applications/          # Apps for all users of this machine
│   └── Admin/            # Local administration applications
└── Library/              # Local machine resources
    ├── Tools/            # Local command-line tools
    │   └── Admin/        # Local admin tools
    ├── Libraries/        # Local frameworks
    ├── Headers/          # Local development resources
    ├── Documentation/    # Local documentation
    │   ├── man/
    │   └── info/
    └── WebApplications/  # Local web services
```

### User Domain: Your Personal Space
```
/Users/jmaloney/          # (or wherever users live)
├── Applications/         # Your personal applications
│   └── Admin/           # Your personal admin tools
└── Library/             # Your personal resources
    ├── Tools/           # Your personal command-line tools
    │   └── Admin/       # Your personal admin tools
    ├── Libraries/       # Your personal frameworks
    ├── Headers/         # Your personal development resources
    ├── Documentation/   # Your personal documentation
    │   ├── man/
    │   └── info/
    ├── WebApplications/ # Your personal web services
    └── Preferences/     # Your settings and preferences
        ├── GNUstep.conf # GNUstep configuration
        └── (other preference files)
```

## The "Library" Philosophy: Why Not Everything Goes in Applications

You'll notice that only actual applications go in the `Applications` directories. Everything else—tools, libraries, documentation, frameworks—lives under `Library`. This serves several important purposes:

### 1. **Conceptual Clarity**
- `Applications` = things users click on or launch directly
- `Library` = supporting resources that applications depend on

### 2. **Search and Discovery**
When you're looking for an application to run, you don't want to wade through hundreds of supporting files. Applications directories stay clean and focused.

### 3. **Dependency Management**
Applications often depend on tools, libraries, and frameworks. By keeping these in `Library`, the system can manage dependencies more intelligently.

### 4. **Administrative Control**
Different types of resources often need different access controls and update policies. Separating them makes this easier to manage.

## How Domains Work in Practice

### Installing Software

**User installs a personal application:**
```bash
# Application goes to personal Applications
/Users/jmaloney/Applications/MyPersonalApp.app

# Supporting files go to personal Library
/Users/jmaloney/Library/Tools/myapp-cli
/Users/jmaloney/Library/Libraries/libmyapp.so
```

**Administrator installs software for all local users:**
```bash
# Application goes to Local Applications
/Local/Applications/TeamProductivitySuite.app

# Supporting files go to Local Library
/Local/Library/Tools/productivity-cli
/Local/Library/Libraries/libproductivity.so
```

**Network administrator deploys organization-wide software:**
```bash
# Application goes to Network Applications
/Network/Applications/CompanySecurityTool.app

# Supporting files go to Network Library
/Network/Library/Tools/security-scanner
/Network/Library/Libraries/libsecurity.so
```

### Preference Lookup Order

When an application needs to find a preference or configuration, GNUstep searches domains in order:

1. **User Domain first** - Your personal settings override everything
2. **Local Domain second** - Machine-specific settings
3. **Network Domain third** - Organization policies
4. **System Domain last** - Built-in defaults

This means you can always override organization settings with personal preferences, but organization settings can override system defaults.

### Example: Font Configuration

```
# System provides basic fonts
/System/Library/Fonts/Helvetica.ttf

# Organization adds corporate fonts
/Network/Library/Fonts/CompanyBrand.ttf

# Local machine adds special fonts
/Local/Library/Fonts/DesignSuite.ttf

# User adds personal fonts
/Users/jmaloney/Library/Fonts/MyFavoriteFont.ttf
```

Applications see all fonts, but personal fonts can override corporate ones if they have the same name.

## The Psychological Benefits

This organization provides several psychological advantages:

### 1. **Confidence in Changes**
You know that modifying files in your User domain can't break the system or affect other users.

### 2. **Clear Ownership**
Every file has an obvious owner and purpose. No more wondering "what is this file and can I delete it?"

### 3. **Predictable Behavior**
Once you understand the domain model, you can predict where any type of file should live.

### 4. **Safe Experimentation**
You can try new software in your User domain without affecting anyone else.

## Comparison with Modern Systems

### macOS (Post-NeXTSTEP)
- **What it does:** Throws most applications into `/Applications`
- **Problems:** Unclear ownership, difficult to manage user vs. system software
- **Lost benefit:** The elegant separation of concerns that NeXTSTEP provided

### Linux (Traditional)
- **What it does:** Scatters files across `/usr`, `/usr/local`, `/opt`, etc.
- **Problems:** Inconsistent organization, unclear boundaries
- **Lost benefit:** User-centric organization and clear mental models

### Windows
- **What it does:** `Program Files`, `Program Files (x86)`, `AppData`, etc.
- **Problems:** Architecture-based rather than ownership-based organization
- **Lost benefit:** The concept of domains entirely

## Implementation Notes for Your Configuration

The proposed Gershwin configuration elegantly maps these concepts:

```bash
# System Domain
GNUSTEP_SYSTEM_APPS=/System/Applications
GNUSTEP_SYSTEM_LIBRARY=/System/Library
# ... (all System variables point under /System/)

# Network Domain  
GNUSTEP_NETWORK_APPS=/Network/Applications
GNUSTEP_NETWORK_LIBRARY=/Network/Library
# ... (all Network variables point under /Network/)

# Local Domain
GNUSTEP_LOCAL_APPS=/Local/Applications  
GNUSTEP_LOCAL_LIBRARY=/Local/Library
# ... (all Local variables point under /Local/)

# User Domain (relative paths within user home)
GNUSTEP_USER_DIR_APPS=Applications
GNUSTEP_USER_DIR_LIBRARY=Library
# ... (all User variables are relative to user home)
```

This configuration restores the NeXTSTEP mental model while adapting it for modern needs.

## Conclusion: Bringing Back Elegance

The domain model isn't just about file organization—it's about creating a system that makes sense to humans. When every file has a clear purpose and owner, when the hierarchy reflects actual relationships and responsibilities, when users can confidently make changes without fear of breaking things, the computer becomes a tool that amplifies human intelligence rather than fighting it.

By implementing this enhanced domain structure in GNUstep, we're not just organizing files—we're restoring one of computer science's most elegant solutions to the problem of system organization. It's a return to the days when operating systems were designed around clear mental models rather than historical accident and accumulated cruft.

This is what Rhapsody promised. This is what Mac OS X abandoned. This is what Gershwin based on GNUstep can deliver.