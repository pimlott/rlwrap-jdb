`rlwrap-jdb` is a rear-guard effort to make [jdb][jdb] almost usable.  It
uses to [rlwrap][rlwrap] not to add not only command-line editing and
history, but (using rlwrap's filter mechanism) handy aliases, command
repetition, and breakpoint completion.

[jdb]: http://docs.oracle.com/javase/7/docs/technotes/tools/solaris/jdb.html
[rlwrap]: http://utopia.knoware.nl/~hlub/rlwrap/

Usage
=====

    Usage:
      rlwrap-jdb [OPT]... [RLWRAP-OPT]... PROG [ARG]...
    Run PROG with command-line editing and history as well as handy aliases,
    command repetition, and breakpoint completion for jdb.  Note PROG doesn't
    have to be jdb; it could be an application startup script, a mvn exec:exe
    invocation, etc. that eventually calls jdb.

    Options:
      --breakpoints-file F  File containing breakpoints for completion;
                            generate with list-java-breakpoints.

    RLWRAP-OPTs must be specified ofter OPTs, and are passed to rlwrap.  Some
    rlwrap options are given defaults:
      --command-name jdb
      --break-chars :
      --histsize 5000

Within jdb, you can use use all normal commands with history and
command-line editing.  In addition, `rlwrap-jdb` adds these aliases:

    h               help (displays in a pager)
    b <breakpoint>  stop in or stop at, depending on <breakpoint> syntax
    b               clear (list breakpoints)
    B               clear (list or delete breakpoints)
    r               run
    n               next
    s               step
    r               step up
    c               cont
    p               print
    d               dump
    bt              where
    l               list
    q               quit

Generally, the aliases are inspired by `gdb`, with some influence from
Perl's debugger.

If you just hit return, the last `next`, `step`, `step up` or `cont` command
is repeated.

Breakpoint completion
---------------------

`rlwrap-jdb` can complete breakpoints.  You first need to run the included
`list-java-breakpoints` with your `$CLASSPATH` environment variable set,
saving the output to a file:

    CLASSPATH=... list-java-breakpoints > breakpoints-file

Then, pass `--breakpoints-file breakpoints-file` to `rlwrap-jdb`.

The support is not ideal, because the completion list may be too long (eg.,
if you type `b com<TAB>` you will get all methods under the `com` package).
I am looking for a way to support partial completion.

I also recommend adding to your `~/.inputrc`:

    $if jdb
        set show-all-if-unmodified On
    $endif

This avoids needing to hit tab twice to get completions.

Building
========

Nothing to build.  You can just copy `rlwrap-jdb`, `rlwrap-jdb-filter`,
and `list-java-breakpoints` into your `bin` directory.

To run, you need Perl, and a few modules.  I think the only module that does
not come bundled with Perl is `Archive::Zip`, which is only needed by
`list-java-breakpoints`.  You can install it as `libarchive-zip-perl` on
Debian-based systems.

Hacking
=======

The magic lives in the rlwrap filter, `rlwrap-jdb-filter`, written in Perl
and using the `RlwrapFilter` module, which comes with `rlwrap` and is
well-documented.

If you want to syntax check the filter with `perl -c`, copy
`RlwrapFilter.pm` to the current directory so it can be found by the filter.
However, you will run across the problem that `RlwrapFilter` hijacks the
error hooks.  I suggest applying `RlwrapFilter.pm.patch` to the local copy.
This is only for development, and there is no reason to modify the installed
``RlwrapFilter.pm`.

TODO
====

- Partial completion of breakpoints (probably requires deeper integration
  into readline).
- More customized completions.

Please report bugs and requests as GitHub issues, or send me email.

Related Work
============

Nobody seems interested in a simple but usable command-line debugger for
Java.  Here is a [Stack Overflow thread][stack] that seeks such a thing,
without turning up any options.

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
