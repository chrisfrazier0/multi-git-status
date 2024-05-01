% MGITSTATUS(1)
% Ferry Boender
% Mar 2022

# NAME

mgitstatus - Show uncommitted, untracked and unpushed changes for multiple Git repos.

# SYNOPSIS

 **mgitstatus** [**\--version**] [**-w**] [**-e**] [**-f**] [**\--throttle** SEC] [**\-c**] [**-d/\--depth**=2] [**\--flatten**] [**\--no-X**] [**DIR** [**DIR**]...]

# DESCRIPTION

**mgitstatus** shows uncommitted, untracked and unpushed changes in multiple
Git repositories.  By default, **mgitstatus** scans two directories deep. This
can be changed with the `-d` (`--depth`) option.  If **DEPTH** is 0, the scan
is infinitely deep.

mgitstatus shows:

- **Uncommitted changes** if there are unstaged or uncommitted changes on the
  checked out branch.

- **Untracked files** if there are untracked files which are not ignored.

- **Needs push (BRANCH)** if the branch is tracking a (remote) branch which is
  behind.

- **Needs upstream (BRANCH)** if a branch does not have a local or remote
  upstream branch configured. Changes in the branch may otherwise never be
  pushed or merged.

- **Needs pull (BRANCH)** if the branch is tracking a (remote) branch which is
  ahead. This requires that the local git repo already knows about the remote
  changes (i.e. you've done a fetch), or that you specify the -f option.
  mgitstatus does NOT contact the remote by default.

- **X stashes** if there are stashes.

Since there are a lot of different states a git repository can be in,
mgitstatus makes no guarantees that all states are taken into account.

# OPTIONS

**\--version**
:   Show version

**-w**
:   Warn about dirs that are not Git repositories

**-e**
:   Exclude repos that are 'ok'

**-f**
:   Do a 'git fetch' on each repo (slow for many repos)

**\--throttle SEC**
:   Wait SEC seconds between each 'git fetch' (-f option)

**-c**
:   Force color output (preserve colors when using pipes)

**-d, \--depth=2**
:   Scan this many directories deep. Default is 2. If **0**, the scan is infinitely deep

**\--no-depth**
:   Do not recurse into directories (incompatible with -d)

**\--flatten**
:   Flatten output by only showing one status per line. If a repo has multiple statuses, multiple lines are shown for that repo. This aids in grepability.

You can limit output with the following options:

**\--no-push**
:   Do not show branches that need a push.

**\--no-pull**
:   Do not show branches that need a pull.

**\--no-upstream**
:   Do not show branches that need an upstream.

**\--no-uncommitted**
:   Do not show branches that have unstaged or uncommitted changes.

**\--no-untracked**
:   Do not show branches that have untracked files.

**\--no-stashes**
:   Do now show stashes

**\--no-ok**
:   Do now show repos that are 'ok' (same as -e)


# EXAMPLES

The following command scans two directories deep for Git projects and shows
their status:

    $ mgitstatus
    ./fboender/sla: ok
    ./fboender/multi-git-status: Needs push (master) Untracked files
    ./other/peewee: ok

To scan deeper (three dirs instead of two) in the current dir:

    $ mgitstatus -d 3

The following command scans three levels deep in `/opt/deploy/` and hides
repos that are 'ok'. It does not show stashes:

    $ mgitstatus -e --no-stashes -d 3 /opt/deploy

To ignore a repo, set the `mgitstatus.ignore` git configuration option for
that repo to `true`. E.g.:

    $ cd stupidrepo
    $ git config --local mgitstatus.ignore true

Sort output by repo name while retaining colors:

    $ mgitstatus -c | sort
    ./ansible: ok
    ./ansible-cmdb: Needs push (master) Uncommitted changes Untracked files
    ./davis: ok
    ./espy: Uncommitted changes Untracked files
    ./garner: Untracked files
    ./garner-chains: ok
    ./mdpreview: ok

Sort output by repo status while retaining colors:

    $ mgitstatus -c --flatten -e | sort -k2
    ./fboender/ansible-cmdb: Uncommitted changes
    ./fboender/espy: Uncommitted changes
    ./fboender/multi-git-status: Uncommitted changes
    ./fboender/ansible-cmdb: Needs push (master)
    ./fboender/ansible-cmdb: Untracked files
    ./fboender/espy: Untracked files
    ./fboender/garner: Untracked files
    ./fboender/multi-git-status: Untracked files

Force color output and flatten the output so we can grep for things:

    $ mgitstatus --flatten -c | grep Uncommitted
    ./fboender/multi-git-status: Uncommitted changes
    ./fboender/ansible-cmdb: Uncommitted changes
    ./fboender/espy: Uncommitted changes


# COPYRIGHT

Copyright 2016-2022, Ferry Boender (et al).

Licensed under the MIT license. For more information, see the LICENSE.txt file.
