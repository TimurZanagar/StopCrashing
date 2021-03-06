# StopCrashing.fsx

This script looks for possible ways the OS can call **into** your code. These entrypoints are the potential origins for crashes because there is no useful exception handler on the stack at that time.

What are these calls? Well, there are a lot of them:

1. Any time you override members of UI objects
2. Any time you export functions to the OS (as in, the responder chain)
3. Any callbacks you register with the OS
4. I'm sure there are more...

This script uses [Mono.Cecil][MC] to scan all the byte code of your application to find these entrypoints. (Well, I've only implemented #1 and #2. #3 is a harder problem to detect.)

It then detects whether you have any exception handling in that code. If you do not, it yells at you.

The idea is that we don't want to bloat our software with exception handlers everywhere. This certainly makes coding more tedious but also introduces a lot of ambiguity. Instead, we still want to fail fast by leaving the majority of our code free of handlers. We will only catch exceptions where we must do so to prevent crashes.

## Running

Just pass it the path of your solution and it will go find the latest binaries automatically.

It also returns an exit code so that it can be easily integrated into your CI server. (It is currently configured only to detect iOS and OS X calls. Android and Win RT support can be added if anyone is interested.)

For example:

`fsharpi --exec StopCrashing.fsx Path/To/MySolution.sln`

This will find a binary for every project in `MySolution.sln` and report its bad methods.



[MC]: http://www.mono-project.com/docs/tools+libraries/libraries/Mono.Cecil/
[Calca]: http://calca.io
[git]: http://github.com/praeclarum/StopCrashing

