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

Best Practices
* Never call `NSTask` on main thread
* Never block the main thread
* Never use retain/release
* Never reference a specific backend in UI code
* All framework APIs are async
* All UI updates occur on main queue
