# Coding Conventions

License
* For new code: FreeBSD type 2-clause BSD header for new code where possible

Mandatory
* ARC only
* No retain/release
* No NSThread
* No NSOperationQueue

Concurrency Model
* Grand Central Dispatch only
* Clear separation:
  ' Background queues → backend work
  * Main queue → UI updates
