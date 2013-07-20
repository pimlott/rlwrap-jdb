`rlwrap-jdb` is a rear-guard effort to make [jdb][jdb] almost usable.  It
uses to [rlwrap][rlwrap] not to add not only command-line editing and
history, but (using rlwrap's filter mechanism) handy aliases and command
repetition.

[jdb]: http://docs.oracle.com/javase/7/docs/technotes/tools/solaris/jdb.html
[rlwrap]: http://utopia.knoware.nl/~hlub/rlwrap/

Usage
=====

    Usage: rlwrap-jdb PROG [ARG]...

Note you have to include `jdb` in the arguments to `rlwrap-jdb`.  This
allows you to run other wrapper scripts, such as an app startup script or a
`mvn exec:exec` invocation.

Building
========

Nothing to build.  You can just copy `rlwrap-jdb` and `rlwrap-jdb-filter`
into your `bin` directory.

Hacking
=======

The magic lives in the rlwrap filter, `rlwrap-jdb-filter`, written in Perl
and using the `RlwrapFilter` module, which comes with `rlwrap` and is
well-documented.

TODO
====

- Customized completion, especially of breakpoints.

You can report bugs and requests as GitHub issues, or send me email.

Related Work
============

Nobody seems interested in a simple command-line debugger for Java.  Here is
a [Stack Overflow thread][stack] that seeks such a thing, without turning up
any options.

[stack]: http://stackoverflow.com/questions/370072/recomend-a-standalone-java-debugger

Not that surprising--the benefits of an integrated debugger are great.  But
small and simple has its place too.

License
=======

`rlwrap-jdb` is licensed under the GNU General Public License, version 2
(the same license as `rlwrap`).

Author
======

Andrew Pimlott, andrew@pimlott.net
