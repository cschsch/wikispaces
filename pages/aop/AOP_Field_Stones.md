---
title: AOP_Field_Stones
---
## Taken from The Server Side
<http://www.theserverside.com/news/thread.tss?thread_id=39026>

We use it for asynchronous event handling, undo management (on the client), replication, transactions, security, caching, parameter validation, the entire object model (in a way that would be more or less impossible to do without AOP), versioning of state, automatic persistence, locking, last modified timestamps, business rules, client/server synchronization, and lots lots more.

Some specific projects/products for the ideas you mentioned implemented using AOP:
Audit trail: see: http://nearinfinity.com/products.html
Monitoring: http://www.glassbox.com/glassbox/Downloads.do (and http://www.ibm.com/developerworks/java/library/j-aopwork10)
<plug>
For authentication and authorization implemented using AspectJ, check out chapters from my book (free download): http://manning.com/books/laddad/chapters. Also take a look at table of content for a range of problems solved with AOP.
</plug>

Indeed, nonintrusiveness is an important consideration in using AOP (in legacy code or otherwise). Relationship between AOP and metadata is pretty complex; it took me two full-lenght articles to discuss it:
Part 1 - Concepts and constructs of metadata-fortified AOP 
http://www-128.ibm.com/developerworks/java/library/j-aopwork3/
Part 2 - Multidimensional interfaces with metadata 
http://www-128.ibm.com/developerworks/java/library/j-aopwork3/


