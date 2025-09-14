# Gershwin GNUstep Network Domain

## Overview

The GNUstep Network Domain provides a mechanism for sharing applications, libraries, and resources across GNUstep machines on a local network using gdomap (GNUstep Distributed Objects Mapper) for service discovery and Distributed Objects for configuration exchange.

## Core Concept

The server's `/Local` domain becomes the network's shared `/Network` domain. One machine acts as the server, sharing its `/Local` directory tree to all client machines via NFS, discovered through gdomap.

## Architecture

### Service Discovery via gdomap

The GNUstep Distributed Objects Mapper (gdomap) is the foundation of network domain discovery:

1. **Server Registration** - Server registers "NetworkDomain" service with local gdomap
2. **Client Discovery** - Client queries gdomap for "NetworkDomain" service  
3. **Configuration Exchange** - Client connects via Distributed Objects to get mount details
4. **Resource Access** - Client mounts server's `/Local` at `/Network`

### Domain Hierarchy

- `/System/` - GNUstep core (local to each machine)
- `/Local/` - Machine-specific resources (on server, this is shared)
- `/Network/` - Network-shared resources (clients only; mounted from server:/Local)

## Implementation

### Server Components

NetworkDomain Service provides the Distributed Objects interface:
- domainInfo - Returns NFS mount information
- serverVersion - Protocol version check
- availableResources - List shared applications/libraries

The service registers with gdomap using NSConnection and NSSocketPortNameServer.

### Client Components

Discovery via gdomap - Client uses NSConnection to find NetworkDomain service with nil host (searches all hosts). Retrieves mount information including exportPath and hostName.

## gdomap Integration Details

### Starting gdomap

gdomap should run on all GNUstep machines:
`gdomap -p` where -p enables network probing

### Service Registration (Server)

Start the network server which registers with gdomap:
`gershwin-network-server &`

Verify registration:
`gdomap -L NetworkDomain`

### Service Discovery (Client)

Query gdomap for NetworkDomain:
`gdomap -L NetworkDomain`

## Tools

### gershwin-promote-server

Configures a machine as a network domain server:
1. Configure NFS export for /Local
2. Ensure gdomap is running
3. Start NetworkDomain service
4. Register with gdomap

### gershwin-join-network

Connects a client to the network domain:
1. Ensure gdomap is running
2. Discover NetworkDomain service
3. Get mount information
4. Mount the network domain
5. Update /etc/fstab for persistence

## Failure Handling

### gdomap Timeouts
- Default timeout: 5 seconds for service discovery
- Configurable via GNUSTEP_GDOMAP_TIMEOUT environment variable

### Service Unavailable
Use exception handling when connecting to NetworkDomain service. Fall back to local-only operation if network domain unavailable.

### Network Partition
- gdomap maintains service cache
- Existing NFS mounts continue working
- New connections fail until network heals
- gdomap automatically rediscovers when network returns

## File System Layout

| Location | On Server | On Client |
|----------|-----------|-----------|
| GNUstep System | `/System/` | `/System/` |
| Network Resources | `/Local/` (source) | `/Network/` (mounted) |
| Local Resources | N/A (they are network) | `/Local/` |

### Installing Network Applications

On the server:
`make install GNUSTEP_INSTALLATION_DOMAIN=LOCAL`

Installs to /Local/Applications/MyApp.app, immediately available at /Network/Applications/MyApp.app on all clients.

## Benefits of gdomap Integration

1. **Pure GNUstep** - No external dependencies (no Avahi, no mDNS)
2. **Automatic Discovery** - gdomap handles all service discovery
3. **DO Integration** - Natural use of Distributed Objects
4. **Extensible** - Easy to add more network services
5. **GNUstep Native** - Works with all GNUstep tools and frameworks

## Testing

### Verify gdomap Operation
- List all registered services: `gdomap -M`
- Test specific service: `gdomap -L NetworkDomain`
- Check gdomap status: `gdomap -S`

### Test Network Domain
On server: Run gnustep-make-server and verify with gdomap -L NetworkDomain
On client: Run gnustep-join-network and verify /Network/Applications shows server's applications

## Summary

The GNUstep Network Domain uses gdomap as its service discovery backbone, leveraging GNUstep's native Distributed Objects system for configuration exchange. This provides a simple, zero-configuration network domain that requires no external dependencies beyond GNUstep itself.