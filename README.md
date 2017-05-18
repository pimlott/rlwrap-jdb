rlwrap-jdb is a rearguard effort to make [jdb][jdb] almost usable.  It
uses to [rlwrap][rlwrap] not to add not only command-line editing and
history, but (using rlwrap's nifty filter mechanism) handy aliases, command
repetition, file redirection and completion of breakpoints and variables.

[jdb]: http://docs.oracle.com/javase/7/docs/technotes/tools/solaris/jdb.html
[rlwrap]: http://utopia.knoware.nl/~hlub/rlwrap/


File output redirection works like so:

    > classes > my-output-file


Running commands from a (newline separated) file works like so:

    > runfile /tmp/commands.txt


The two can also be combined like so:

    > runfile /tmp/commands.txt > /tmp/output.txt


Usage
=====

    Usage:
      rlwrap-jdb [OPT]... [RLWRAP-OPT]... PROG [ARG]...
    Run PROG with command-line editing and history as well as handy aliases,
    command repetition, and completion for jdb.  Note PROG doesn't have to be
    jdb; it could be an application startup script, a mvn exec:exec invocation,
    etc. that eventually calls jdb.

    Options:
      --find-breakpoints    Find breakpoints for completion, saving to
                            --breakpoints-file if given.  CLASSPATH must be set.
      --breakpoints-file F  File containing breakpoints, generated with
                            list-java-breakpoints or --find-breakpoints.
      --main-class C        The main class to debug.  This is not essential, and
                            rlwrap-jdb can often figure it out itself.

    RLWRAP-OPTs must be specified ofter OPTs, and are passed to rlwrap.  Default
    rlwrap options:
      --command-name jdb
      --break-chars :
      --histsize 5000
      --wait-before-prompt -40

Within jdb, you can use use all normal commands with history and
command-line editing.  In addition, rlwrap-jdb adds these aliases (also in
the on-line help):

    b <class id>.<method>[(argument_type,...)]
                              -- set a breakpoint in a method
    b <class id>:<line>       -- set a breakpoint at a line
    b                         -- set a breakpoint at the current line
    d <breakpoint>            -- clear a breakpoint in a method or at a line
    B <breakpoint>            -- clear a breakpoint in a method or at a line
    s                         -- step (starts VM if not running)
    r                         -- step up
    n                         -- next (starts VM if not running)
    c                         -- cont (starts VM if not running)
    p <expr>                  -- print
    x <expr>                  -- dump
    bt                        -- where
    T                         -- where
    l [line number|method]    -- list
    h                         -- help
    q                         -- quit

    Hit return to repeat the last next, step, step up, or cont.

The aliases are inspired by gdb and the Perl debugger.

Completion
----------

rlwrap-jdb can complete variable names (method arguments, locals, and
fields) in the `p` (`print`) and `x` (`dump`) commands, and breakpoints in
the `b` (`stop`) commands.  If you hit tab before starting to type the
breakpoint, it will complete to the current class, making it easy to break
within the same class.

For breakpoint completion, either pass `--find-breakpoints` to rlwrap-jdb,
or generate a breakpoints file ahead of time by running
`list-java-breakpoints` and passing the file to rlwrap-jdb with
`--breakpoints-file F`.  Either way, ensure that the `$CLASSPATH`
environment variable includes all the classes for which you want
breakpoints.  Examples:

    CLASSPATH=... rlwrap-jdb --find-breakpoints jdb C

    CLASSPATH=... list-java-breakpoints > breakpoints-file
    rlwrap-jdb --breakpoints-file breakpoints-file run-my-app

You may prefer to generate the breakpoints file ahead of time if there are a
lot of classes to scan.

Breakpoint completion is not ideal, because the completion list may be too
long (eg., if you type `b com<TAB>` you will get all methods under the `com`
package).  I am looking for a way to support partial completion.

I also recommend adding to your `~/.inputrc`:

    $if jdb
        set show-all-if-unmodified On
    $endif

This avoids needing to hit tab twice to get completions.  (You can get rid
of the `$if`/`$endif` if you want this for all applications.)

Building
========

Nothing to build.  You can just copy `rlwrap-jdb`, `rlwrap-jdb-filter`,
and `list-java-breakpoints` into your `bin` directory.

To run, you need rlwrap, Perl, and a few Perl modules.  I think the only
module that does not come bundled with Perl is `Archive::Zip`, which is only
needed for finding breakpoints in JARs.  You can install it as
`libarchive-zip-perl` on Debian-based systems.

Hacking
=======

The magic lives in the rlwrap filter, `rlwrap-jdb-filter`, written in Perl
and using the `RlwrapFilter` module, which comes with rlwrap and is
well-documented.

If you want to syntax check the filter with `perl -c`, copy
`RlwrapFilter.pm` to the current directory so it can be found by the filter.
However, you will run across the problem that `RlwrapFilter` hijacks the
error hooks.  I suggest applying `RlwrapFilter.pm.patch` to the local copy.
This is only for development, and there is no reason to modify the installed
`RlwrapFilter.pm`.

TODO
====

- Partial completion of breakpoints (probably requires deeper integration
  into readline).
- Allow multiple breakpoints files.
- More completions, eg. for clearing breakpoints.
- Replay a session.

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

rlwrap-jdb is licensed under the GNU General Public License, version 2
(the same license as rlwrap).

Author
======

Andrew Pimlott, andrew@pimlott.net
