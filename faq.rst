:tocdepth: 2

.. _faq:

Python Developer FAQ
~~~~~~~~~~~~~~~~~~~~

.. contents::
   :local:


Communications
==============

Where should I report specific problems?
----------------------------------------

Specific problems should be posted to the `issue tracker`_.

.. _issue tracker: https://bugs.python.org


What if I'm not sure it is a bug?
---------------------------------

The general Python help locations listed above are the best place to start
with that kind of question. If they agree it looks like a bug, then the
next step is to either post it to the `issue tracker`_ or else to ask further
on the core development mailing list, `python-dev`_.

.. _python-dev: http://mail.python.org/mailman/listinfo/python-dev


What if I disagree with an issue resolution on the tracker?
-----------------------------------------------------------

First, take some time to consider any comments made in association with the
resolution of the tracker issue. On reflection, they may seem more reasonable
than they first appeared.

If you still feel the resolution is incorrect, then raise the question on
`python-dev`_. If the consensus there supports the disputed resolution, please
take any further objections to `python-ideas`_ (or some other forum). Further
argument on `python-dev`_ after a consensus has been reached amongst the core
developers is unlikely to win any converts.

.. _python-ideas: https://mail.python.org/mailman/listinfo/python-ideas


How do I tell who is and isn't a core developer?
------------------------------------------------

You can check their name against the `full list of developers`_ with commit
rights to the main source control repository.

On the `issue tracker`_, most core developers will have the Python logo
appear next to their name.

.. _full list of developers: https://hg.python.org/committers.txt


Version Control
===============

For everyone
------------

The following FAQs are intended for both core developers and contributors.

Where can I learn about the version control system used, Git?
'''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

Git_'s official web site is at https://git-scm.com/.  A book on Git published
by Apress, `Pro Git`_, is available for free online.

With Git installed, you can run the help tool that comes with
Git to get help::

  git help

The `man page`_ for ``git`` provides a quick refresher on the various commands,
but doesn't provide any guidance on overall workflow. Each sub-command has a
more detailed manpage. You can view these by running a command such as::

    $ man git-pull

or following the links from the main `man page`_.

.. _Git: https://git-scm.com/
.. _Pro Git: https://git-scm.com/book/en/v2
.. _man page: https://www.kernel.org/pub/software/scm/git/docs/


What do I need to use Git?
''''''''''''''''''''''''''

First, you need to `download Git`_.  Most UNIX-based operating systems
have binary packages available.  Most package management systems also
have native Git packages available.

If you have push rights, you need OpenSSH_.  This is needed to verify
your identity when performing commits. As with Git, binary packages
are typically available either online or through the platform's package
management system.

.. _download Git: https://git-scm.com/downloads
.. _OpenSSH: http://www.openssh.org/


What's a working copy? What's a repository?
'''''''''''''''''''''''''''''''''''''''''''

Git is a "distributed" version control system.  This means that each
participant, even casual contributors, download a complete copy (called a
*clone*, since it is obtained by calling ``git clone``) of the central
repository which can be treated as a stand-alone repository for all purposes.
That copy is called in the FAQ the *local repository*, to differentiate
with any *remote repository* you might also interact with.

Changes in your *local repository* don't get automatically shared with the
rest of the world.  Git ensures that you have to do so explicitly
(this allows you to experiment quite freely with multiple branches of
development, all on your private computer).  The main commands for doing
so are ``git pull`` and ``git push``.


Which branches are in my local repository?
''''''''''''''''''''''''''''''''''''''''''

Typing ``git branch`` displays the open branches in your local repository::

    $ git branch
    * master
      2.7
      3.5
      3.4
      3.3
      3.2

.. _git-current-branch:

Which branch is currently checked out in my working copy?
'''''''''''''''''''''''''''''''''''''''''''''''''''''''''

Use::

   $ git branch
   * master

The asterisk (\*) next to the branch name denotes the current branch.

For more details about the current state of the repository, use::

    $ git status
    On branch master
    Your branch is up-to-date with 'origin/master'.
    nothing to commit, working directory clean


.. _git-switch-branches:

How do I switch between branches inside my working copy?
''''''''''''''''''''''''''''''''''''''''''''''''''''''''

Simply use ``git checkout`` to checkout another branch in the current directory::

   $ git branch
   * master
     3.5
   $ git checkout 3.5
   Switched to branch '3.5'
   Your branch is up-to-date with 'origin/3.5'.
   $ git branch
     master
   * 3.5

Note that, due to some previously built executables being used as a part of
the build process, you may sometimes run into issues when attempting to
switch between Python 2.x and Python 3.x branches. In these cases, it is
best to run a ``make distclean`` to ensure that all previously built files
are removed.


I want to keep a separate working copy per development branch, is it possible?
''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

Create several clones of your local repository::

   $ git clone cpython py35
   Cloning into 'py35'...
   remote: Counting objects: 629890, done.
   remote: Total 629890 (delta 0), reused 0 (delta 0), pack-reused 629889
   Receiving objects: 100% (629890/629890), 216.81 MiB | 3.58 MiB/s, done.
   Resolving deltas: 100% (503219/503219), done.
   Checking connectivity... done.
   $ cd py35
   $ git checkout 3.5
   Switched to branch '3.5'
   Your branch is up-to-date with 'origin/3.5'.

.. _git-paths:

How do I link my local repository to a particular remote repository?
''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

Your local repository is linked by default to the remote repository it
was *cloned* from. If you would like to link to a different remote, first add
the remote::

    $ git remote add myremote <remote url>

Then set it as the upstream::

    $ git branch --set-upstream-to myremote/<branch name>


How do I compare my local repository to a remote repository?
''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

To display the list of changes that are in your local repository, but not
in the remote, use::

    $ git log origin/master..

This is the list of changes that will be sent if you call
``git push``.  It does **not** include any :ref:`uncommitted
changes <hg-status>` in your working copy!

Conversely, for the list of changes that are in the remote repository but
not in the local, use::

    $ git log ..origin/master

This is the list of changes that will be retrieved if you call
``git pull``.

Note that these commands will not query the latest state of the remote
repository, but instead the state the repository was in the last time you ran a
command such as ``git fetch`` or ``git pull``. To update the state of remote
branches, run::

    $ git remote update


How do I update my local repository to be in sync with a remote repository?
'''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

Run::

    $ git pull --ff-only <remote> <branch name>

For example::

    $ git pull --ff-only origin master

from the repository you wish to pull the latest changes into.  Most of the
time, that repository is a clone of the repository you want to pull from,
so you can simply type::

   $ git pull --ff-only


How do I apply a patch?
'''''''''''''''''''''''

If you want to try out or review a patch generated using Mercurial, do::

   patch -p1 < somework.patch

This will apply the changes in your working copy without committing them.
If the patch was not created by Mercurial (for example, a patch created by
Subversion and thus lacking any ``a``/``b`` directory prefixes in the patch),
replace ``-p1`` with ``-p0``.

If the patch contains renames, deletions or copies, and you intend committing
it after your review, you might prefer using::

   hg import --no-commit somework.patch

If you want to work on the patch using mq_ (Mercurial Queues), type instead::

   hg qimport somework.patch

This will create a patch in your queue with a name that matches the filename.
You can use the ``-n`` argument to specify a different name.  To have the
patch applied to the working copy, type::

   hg qpush

Finally, to delete the patch, first un-apply it if necessary using ``hg qpop``,
then do::

   hg qdelete somework.patch

.. _extended diff format: http://www.selenic.com/mercurial/hg.1.html#diffs
.. _mq: https://www.mercurial-scm.org/wiki/MqExtension


.. _merge-patch:

How do I solve conflicts when applying a patch fails?
'''''''''''''''''''''''''''''''''''''''''''''''''''''

The standard ``patch`` command, as well as ``hg import``, will produce
unhelpful ``*.rej`` files when it fails applying parts of a patch.
We suggest you try the mpatch_ utility, which can help resolve a number of
common causes of patch rejects.

To make use of ``mpatch`` transparent, you can define a shell alias in one
of your startup files.  For example, if you want it to open the ``kdiff3``
merge program to fix failing patch hunks::

   alias patch='mpatch --merge=kdiff3'

or if you want it to automatically solve conflicts by using heuristics::

   alias patch='mpatch --auto --no-merge'

.. _mpatch: http://oss.oracle.com/~mason/mpatch/


How do I add a file or directory to the repository?
'''''''''''''''''''''''''''''''''''''''''''''''''''

Simply specify the path to the file or directory to add and run::

 hg add PATH

If ``PATH`` is a directory, Mercurial will recursively add any files in that
directory and its descendants.

If you want Mercurial to figure out by itself which files should be added
and/or removed, just run::

 hg addremove

**Be careful** though, as it might add some files that are not desired in
the repository (such as build products, cache files, or other data).

You will then need to run ``hg commit`` (as discussed below) to commit
the file(s) to your local repository.


What's the best way to split a file into several files?
'''''''''''''''''''''''''''''''''''''''''''''''''''''''

To split a file into several files (e.g. a module converted to a package or a
long doc file divided in two separate documents) use ``hg copy``::

    hg copy module.rst module2.rst

and then remove the parts that are not necessary from ``module.rst`` and
``module2.rst``.  This allows Mercurial to know that the content of
``module2.rst`` used to be in ``module.rst``, and will make subsequent merges
easier.  If necessary, you can also use ``hg copy`` several times.

If you simply create ``module2.rst``, add it with ``hg add``, and copy part of
the content from ``module.rst``, Mercurial won't know that the two file are
related.


How do I delete a file or directory in the repository?
''''''''''''''''''''''''''''''''''''''''''''''''''''''

Specify the path to be removed with::

 hg remove PATH

This will remove the file or the directory from your working copy; you will
have to :ref:`commit your changes <hg-commit>` for the removal to be recorded
in your local repository.


.. _hg-status:

What files are modified in my working copy?
'''''''''''''''''''''''''''''''''''''''''''

Running::

 hg status

will list any pending changes in the working copy.  These changes will get
committed to the local repository if you issue an ``hg commit`` without
specifying any path.

Some
key indicators that can appear in the first column of output are:

   =  ===========================
   A  Scheduled to be added
   R  Scheduled to be removed
   M  Modified locally
   ?  Not under version control
   =  ===========================

If you want a line-by-line listing of the differences, use::

 hg diff


How do I revert a file I have modified back to the version in the repository?
'''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

Running::

 hg revert PATH

will revert ``PATH`` to its version in the repository, throwing away any
changes you made locally.  If you run::

 hg revert -a

from the root of your working copy it will recursively restore everything
to match up with the repository.


How do I find out who edited or what revision changed a line last?
''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

You want::

 hg annotate PATH

This will output to stdout every line of the file along with which revision
last modified that line.  When you have the revision number, it is then
easy to :ref:`display it in detail <hg-log-rev>`.


.. _hg-log:

How can I see a list of log messages for a file or specific revision?
'''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

To see the history of changes for a specific file, run::

 hg log -v [PATH]

That will list all messages of revisions which modified the file specified
in ``PATH``.  If ``PATH`` is omitted, all revisions are listed.

If you want to display line-by-line differences for each revision as well,
add the ``-p`` option::

 hg log -vp [PATH]

.. _hg-log-rev:

If you want to view the differences for a specific revision, run::

 hg log -vp -r <revision number>


How can I see the changeset graph in my repository?
'''''''''''''''''''''''''''''''''''''''''''''''''''

In Mercurial repositories, changesets don't form a simple list, but rather
a graph: every changeset has one or two parents (it's called a merge changeset
in the latter case), and can have any number of children.

The graphlog_ extension is very useful for examining the structure of the
changeset graph.  It is bundled with Mercurial.

Graphical tools, such as TortoiseHG, will display the changeset graph
by default.

.. _graphlog: https://www.mercurial-scm.org/wiki/GraphlogExtension


How do I update to a specific release tag?
''''''''''''''''''''''''''''''''''''''''''

Run::

   hg tags

to get a list of tags.  To update your working copy to a specific tag, use::

   hg update <tag>


How do I find which changeset introduced a bug or regression?
'''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

``hg bisect``, as the name indicates, helps you do a bisection of a range of
changesets.

You need two changesets to start the search: one that is "good"
(doesn't have the bug), and one that is "bad" (has the bug).  Usually, you
have just noticed the bug in your working copy, so you can start with::

   hg bisect --bad

Then you must ``update`` to a previous changeset that doesn't have the bug.
You can conveniently choose a faraway changeset (for example a former release),
and check that it is indeed "good".  Then type::

   hg bisect --good

Mercurial will automatically bisect so as to narrow the range of possible
culprits, until a single changeset is isolated.  Each time Mercurial presents
you with a new changeset, re-compile Python and run the offending test, for
example::

   make -j2
   ./python -m test -uall test_sometest

Then, type either ``hg bisect --good`` or ``hg bisect --bad`` depending on
whether the test succeeded or failed.


How come feature XYZ isn't available in Mercurial?
''''''''''''''''''''''''''''''''''''''''''''''''''

Mercurial comes with many bundled extensions which can be explicitly enabled.
You can get a list of them by typing ``hg help extensions``.  Some of these
extensions, such as ``color``, can prettify output; others, such as ``fetch``
or ``graphlog``, add new Mercurial commands.

There are also many `configuration options`_ to tweak various aspects of the
command line and other Mercurial behaviour; typing `man hgrc`_ displays
their documentation inside your terminal.

In the end, please refer to the Mercurial `wiki`_, especially the pages about
`extensions`_ (including third-party ones) and the `tips and tricks`_.


.. _man hgrc: http://www.selenic.com/mercurial/hgrc.5.html
.. _wiki: https://www.mercurial-scm.org/wiki/
.. _extensions: https://www.mercurial-scm.org/wiki/UsingExtensions
.. _tips and tricks: https://www.mercurial-scm.org/wiki/TipsAndTricks
.. _configuration options: http://www.selenic.com/mercurial/hgrc.5.html


.. _core-devs-faqs:

For core developers
-------------------

These FAQs are intended mainly for core developers.


How do I commit a change to a file?
'''''''''''''''''''''''''''''''''''

To commit any changes to a file (which includes adding a new file or deleting
an existing one), you use the command::

 hg commit [PATH]

``PATH`` is optional: if it is omitted, all changes in your working copy
will be committed to the local repository.  When you commit, be sure that all
changes are desired by :ref:`reviewing them first <hg-status>`;
also, when making commits that you intend to push to public repositories,
you should **not** commit together unrelated changes.

To abort a commit that you are in the middle of, leave the message
empty (i.e., close the text editor without adding any text for the
message).  Mercurial will then abort the commit operation so that you can
try again later.

Once a change is committed to your local repository, it is still only visible
by you.  This means you are free to experiment with as many local commits
you feel like.

.. note::
   If you do not like the default text editor Mercurial uses for
   entering commit messages, you may specify a different editor,
   either by changing the ``EDITOR`` environment variable or by setting
   a Mercurial-specific editor in your global ``.hgrc`` with the ``editor``
   option in the ``[ui]`` section.


How do I solve merge conflicts?
'''''''''''''''''''''''''''''''

The easiest way is to install KDiff3 --- Mercurial will open it automatically
in case of conflicts, and you can then use it to solve the conflicts and
save the resulting file(s).  KDiff3 will also take care of marking the
conflicts as resolved.

If you don't use a merge tool, you can use ``hg resolve --list`` to list the
conflicting files, resolve the conflicts manually, and the use
``hg resolve --mark <file path>`` to mark these conflicts as resolved.
You can also use ``hg resolve -am`` to mark all the conflicts as resolved.

.. note::
   Mercurial will use KDiff3 automatically if it's installed and it can find
   it --- you don't need to change any settings.  KDiff3 is also already
   included in the installer of TortoiseHg.  For more information, see
   https://www.mercurial-scm.org/wiki/KDiff3.


How do I make a null merge?
'''''''''''''''''''''''''''

If you committed something (e.g. on 3.6) that shouldn't be ported on newer
branches (e.g. on default), you have to do a *null merge*::

   cd 3.x
   hg merge 3.6
   hg revert -ar default
   hg resolve -am  # needed only if the merge created conflicts
   hg ci -m '#12345: null merge with 3.6.'

Before committing, ``hg status`` should list all the merged files as ``M``,
but ``hg diff`` should produce no output.  This will record the merge without
actually changing the content of the files.


I got "abort: push creates new remote heads!" while pushing, what do I do?
''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

If you see this message while pushing, it means that you committed something
on a clone that was not up to date, thus creating a new head.
This usually happens for two reasons:

1. You forgot to run ``hg pull`` and/or ``hg up`` before committing;
2. Someone else pushed on the main repo just before you, causing a push race;

First of all you should pull the new changesets using ``hg pull``.  Then you can
use ``hg heads`` to see which branches have multiple heads.

If only one branch has multiple heads, you can do::

   cd default
   hg heads .
   hg up csid-of-the-other-head
   hg merge
   hg ci -m 'Merge heads.'

``hg heads .``  will show you the two heads of the current branch: the one you
pulled and the one you created with your commit (you can also specify a branch
with ``hg heads <branch>``).  While not strictly necessary, it is highly
recommended to switch to the other head before merging.  This way you will be
merging only your changeset with the rest, and in case of conflicts it will be
a lot easier.

If more than one branch has multiple heads, you have to repeat these steps for
each branch.  Since this creates new changesets, you will also have to
:ref:`merge them between branches <branch-merge>`.  For example, if both ``3.6``
and ``default`` have multiple heads, you should first merge heads in ``3.6``,
then merge heads in ``default``, and finally merge ``3.6`` with ``default``
using ``hg merge 3.6`` as usual.

In order to avoid this, you should *always remember to pull and update before
committing*.


How do I undo the changes made in a recent commit?
''''''''''''''''''''''''''''''''''''''''''''''''''

First, this should not happen if you take the habit of :ref:`reviewing changes
<hg-status>` before committing them.

In any case, run::

 hg backout <revision number>

This will modify your working copy so that all changes in ``<revision number>``
(including added or deleted files) are undone.  You then need to :ref:`commit
<hg-commit>` these changes so that the backout gets permanently recorded.

.. note::
   These instructions are for Mercurial 1.7 and higher.  ``hg backout`` has
   a slightly different behaviour in versions before 1.7.


General
=======


How do I regenerate configure?
------------------------------

If a change is made to Python which relies on some POSIX system-specific
functionality (such as using a new system call), it is necessary to update the
``configure`` script to test for availability of the functionality.

Python's ``configure`` script is generated from ``configure.ac`` using Autoconf.
Instead of editing ``configure``, edit ``configure.ac`` and then run
``autoreconf`` to regenerate ``configure`` and a number of other files (such as
``pyconfig.h``.

When submitting a patch with changes made to ``configure.ac``, it is preferred
to leave out the generated files as differences between Autoconf versions
frequently results in many spurious changes cluttering the patch. Instead,
remind any potential reviewers on the tracker to run ``autoreconf``.

Note that running ``autoreconf`` is not the same as running ``autoconf``. For
example, ``autoconf`` by itself will not regenerate ``pyconfig.h.in``.
``autoreconf`` runs ``autoconf`` and a number of other tools repeatedly as is
appropriate.

Python's ``configure.ac`` script typically requires a specific version of
Autoconf.  At the moment, this reads: ``version_required(2.65)``

If the system copy of Autoconf does not match this version, you will need to
install your own copy of Autoconf.


How do I port Python to a new platform?
---------------------------------------

The first step is to familiarize yourself with the development toolchain on
the platform in question, notably the C compiler. Make sure you can compile and
run a hello-world program using the target compiler.

Next, learn how to compile and run the Python interpreter on a platform to
which it has already been ported; preferably Unix, but Windows will
do, too. The build process for Python, in particular the ``Makefile`` in the
source distribution, will give you a hint on which files to compile
for Python.  Not all source files are relevant: some are platform
specific, others are only used in emergencies (e.g. ``getopt.c``).

It is not recommended to start porting Python without at least medium-level
understanding of your target platform; i.e. how it is generally used, how to
write platform specific apps, etc. Also, some Python knowledge is required, or
you will be unable to verify that your port is working correctly.

You will need a ``pyconfig.h`` file tailored for your platform.  You can
start with ``pyconfig.h.in``, read the comments, and turn on definitions that
apply to your platform.  Also, you will need a ``config.c`` file, which lists
the built-in modules you support.  Again, starting with
``Modules/config.c.in`` is recommended.

Finally, you will run into some things that are not supported on your
target platform.  Forget about the ``posix`` module in the beginning. You can
simply comment it out of the ``config.c`` file.

Keep working on it until you get a ``>>>`` prompt.  You may have to disable the
importing of ``site.py`` by passing the ``-S`` option. When you have a prompt,
bang on it until it executes very simple Python statements.

At some point you will want to use the ``os`` module; this is the time to start
thinking about what to do with the ``posix`` module.  It is okay to simply
comment out functions in the ``posix`` module that cause problems; the
remaining ones will be quite useful.

Before you are done, it is highly recommended to run the Python regression test
suite, as described in :ref:`runtests`.
