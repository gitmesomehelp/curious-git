.. check with make doctest

################################
The curious coder's guide to git
################################

*****************
git - love - hate
*****************

I've used git now for a long time.  I think it is a masterpiece of design, I
use it all day every day and I just can't imagine what it would be like not to
use it. So, no question, I *love* git.

As y'all may know, `Linus Torvalds wrote git from scratch
<http://git-scm.com/book/en/Getting-Started-A-Short-History-of-Git>`_.  He
loves it too.  `Here is Linus talking about git
<http://www.youtube.com/watch?v=MShbP3OpASA#t=2288>`_ in a question and answer
session:

    Actually I'm proud of git. I want to say this. The fact that I had to
    write git was accidental, but Linux, the design came from a great mind,
    and that great mind was not mine. I mean you have to give credit for the
    design of Linux to Kernighan and Ritchie and Thompson. I mean there's a
    reason I like Unix and I wanted to redo it. I do want to say that git is a
    design that is mine and unique, and I'm proud of the fact that I can damn
    well also do good design from scratch.

But some people hate git.  Really *hate* it. They find it confusing and
error prone and it makes them angry.  Why are there such different views?

I think the reason some people hate git, is because they don't yet understand
it.  I reason I can say this without being patronizing is because I went
through something similar myself.

When I first started using git, I found it uncomfortable.  I could see it was
very powerful, but I sometimes got lost and stuck and had to Google for a set
of magic commands to get me out of trouble.  I once accidentally made a huge
mess of our project's main repository by running a command I didn't
understand. Git often made me feel stupid.  It felt like a prototype racecar
with a badly designed dashboard that was just about to blow a tire or an
engine and take me off the road.

Then, one day, I read the `git parable`_.  The git parable is a little story
about a developer trying to work out how to make a version control system.  It
gradually builds up from copying whole directories of files to something very
much like git.  I didn't understand it all right away, but as soon as I read
that page, the light-bulb went on - I got git.  I suddenly started to feel
comfortable.  I knew for sure I could work out why git worked the way it did.
I could see that it must be possible to do complicated and powerful
things, and I could work out how to do them.

Reading the git parable took me about 45 minutes, but those 45 minutes changed
me from an unhappy git user to someone who uses git often every day, but,
happily, knowing that I have the right tool for the job.

So, my experience tells me that to use git |--| yes *use* git |--| you need to
spend the short amount of time it takes to *understand* git.  You don't
believe me, or you think that I'm a strange kind of person not like you who
probably likes writing their own operating systems. Not so - the insight I'm
describing comes up over and over.  From `understanding git conceptually
<http://www.sbf5.com/~cduan/technical/git>`_:

    When I first started using Git, I read plenty of tutorials, as well as the
    user manual. Though I picked up the basic usage patterns and commands, I
    never felt like I grasped what was going on “under the hood,” so to speak.
    Frequently this resulted in cryptic error messages, caused by my random
    guessing at the right command to use at a given time. These difficulties
    worsened as I began to need more advanced (and less well documented)
    features.

Here's a quote from the `pro git book <http://git-scm.com/book>`_ by Scott
Chacon.  The git book is a standard reference that is hosted on the main git
website.

    Chapter 9: Git Internals

    You may have skipped to this chapter from a previous chapter, or you may
    have gotten here after reading the rest of the book — in either case, this
    is where you’ll go over the inner workings and implementation of Git. I
    found that learning this information was fundamentally important to
    understanding how useful and powerful Git is, but others have argued to me
    that it can be confusing and unnecessarily complex for beginners. Thus,
    I’ve made this discussion the last chapter in the book so you could read
    it early or later in your learning process. I leave it up to you to
    decide.

.. comment yoh -- below you demand an hour while above only 45
           minutes.  Do not raise stakes! ;-)

So |--| have no truck with people who try and tell you that you can just use
git and that you don't need the `deep shit`_. You *do* need the deep shit, but
the deep shit isn't that deep, and it will take you an hour of your time to
get all of it.  Then I'm betting that you'll see that the alchemist has
succeeded at last, and the shit has finally turned into gold.

.. _deep shit: http://rogerdudler.github.io/git-guide

So |--| please |--| invest an hour of your life to understand this stuff.
Concentrate, go slowly, make sure you get it. In return for an hour of your
life, you will get many happy years for which git will appear in its true
form, both beautiful and useful.

The one thing about git you really need to understand
=====================================================

git is not really a "Version Control System". It is better described
as a "Content Management System", that turns out to be really good for
version control.

I'll say that again.  Git is a content management system.  To quote from the
`root page of the git manual <http://git-scm.com/docs/git.html>`_: "git - the
stupid content tracker".

The reason that this is important is that git thinks in a very simple way
about files and directories.  You will ask git to keep snapshots of files in a
directory, and it does just this; it stores snapshots of the files, so you can
go back to them later.

Here is a story where we develop a very simple system for storing file
snapshots.  We soon find it starts to look just like git.

****************
A familiar story
****************

To understand why git does what it does, we first need to think about what a
content manager should do, and why we would want one.

As in the `git parable`_ - we'll try and design our own, and then see what
git has to say.

(To go through this a little more slowly, and with more jokes, you might also
try my  :doc:`foundation` page).

While we are designing our own content management system, we will do a lot of
stuff longhand, to show how things work.  When we get to git, we will find it
does these tasks for us.

The story so far...
===================

You are writing a breakthrough paper showing that you can entirely
explain the brain using random numbers. You've got the draft paper, and
the analysis script and a figure for the paper. These are all in a
directory modestly named ``nobel_prize``.

In this directory, you have the paper draft ``nobel_prize_paper.txt``, the
analysis script ``very_clever_analysis.py``, and a figure for the paper
``stunning_figure.png``.

You can get this ground-breaking paper by downloading and unzipping
:download:`nobel_prize_files.zip`.

.. workrun::
    :hide:

    # clean up old files from previous doc run
    rm -rf nobel_prize repos .gitconfig
    cp ../nobel_prize_files.zip .

Here I'm using the command line to do the unzip:

.. desktoprun:: bash

    unzip -o nobel_prize_files.zip

Here's what we get in our ``nobel_prize`` directory:

.. desktoprun::

    # List directory contents
    ls nobel_prize

The dog ate my results
======================

You've been working on this paper for a while.

About 2 weeks ago, you were very excited with the results. You ran the script,
made the figure, and went to your advisor, Josephine. She was excited too. The
figure looks good! You get ready to publish in Science.

Today you finished cleaning up for the Science paper, and reran the analysis,
and it doesn't look that good anymore. You go to see Josephine. She says "It
used to look better than that". That's what you think too. But:

* **Did it really look better before?**
* If it did, **why does it look different now?**

Deja vu all over again
======================

Given you are so clever and you have discovered how the brain works, it is
really easy for you to leap in your time machine, and go back two weeks to
start again.

What are you going to do differently this time?

Make regular snapshots
======================

You decide to make your own content management system called
*fancy_snapshots*.

It's very simple.

Every time you finish doing some work on your paper, you make a snapshot
of the analysis directory.

The snapshot is just a copy of all the files in the directory, kept
somewhere safe.

You make a directory to store the snapshots called ``.fancy_snapshots``:

.. desktoprun:: bash

    # Change to nobel_prize directory
    cd nobel_prize

.. prizerun::

    # Make the ".fancy_snapshots" directory
    mkdir .fancy_snapshots

Then you make a directory for the first backup:

.. prizerun::

    mkdir .fancy_snapshots/1
    mkdir .fancy_snapshots/1/files

And you copy the files there:

.. prizerun::

    # Copy all files in curent directory to backup directory
    cp * .fancy_snapshots/1/files

Reminding yourself of what you did
==================================

For good measure, you put a file in the snapshot directory to remind you when
you did the snapshot, and who did it, and what was new for this snapshot. Call
this file ``info.txt``.  So, we write something like this:

.. prizewrite::

    # file: .fancy_snapshots/1/info.txt
    Date: April 1 2012, 14.30
    Author: I. M. Awesome
    Notes: First backup of my amazing idea

Now you have these files in the ``nobel_prize`` directory [#tree-command]_.

.. prizerun::

    # Show representation of directory tree
    tree -a

Every time you do some work on the files, you back them up in the same
way. After a few days:

.. prizerun::

    # Append some text to nobel_prize_paper.txt
    echo "The charts are very impressive" >> nobel_prize_paper.txt
    # Make a new snapshot
    mkdir .fancy_snapshots/2
    mkdir .fancy_snapshots/2/files
    cp * .fancy_snapshots/2/files  # Copy all working directory files

We make a new ``info.txt`` file describing what is new in this snapshot:

.. prizewrite::

    # file: .fancy_snapshots/2/info.txt
    Date: April 1 2012, 18.03
    Author: I. M. Awesome
    Notes: Fruit of enormous thought

The next day we write a little more on our paper:

.. prizerun::

    # Append some more text to nobel_prize_paper.txt
    echo "The graphs are also compelling" >> nobel_prize_paper.txt
    # Make another shapshot
    mkdir .fancy_snapshots/3
    mkdir .fancy_snapshots/3/files
    cp * .fancy_snapshots/3/files  # Copy all working directory files

We add a description of these changes:

.. prizewrite::

    # file: .fancy_snapshots/3/info.txt
    Date: April 2 2012, 11.20
    Author: I. M. Awesome
    Notes: Now seeing things clearly

After three days of work we have three snapshots in our backup directory:

.. prizerun::

    tree -a

You keep doing this for another couple of days, until again the time comes to
talk to Josephine. By now you have 5 snapshots.

.. prizerun::
    :hide:

    # Make the next couple of commits
    cp -r .fancy_snapshots/3 .fancy_snapshots/4
    echo "Increasing anxiety" > .fancy_snapshots/4/files/nobel_prize_paper.txt
    cp -r .fancy_snapshots/3 .fancy_snapshots/5
    # Copy the other version of the stunning figure
    cp ../../extras/stunning_figure.png.v2 .fancy_snapshots/5/files/stunning_figure.png
    echo "random_data[0, 0] = 42" > .fancy_snapshots/5/files/very_clever_analysis.py

The future has not changed. Josephine again thinks the results have
changed. But now - you can check.

You go back and look at the original figure in
``.fancy_snapshots/1/stunning_figure.png``. It does look different.

You go through all the ``.fancy_snapshots`` directories in order, `1, 2, 3, 4,
5`. It turns out that the figure changes in ``.fancy_snapshots/5``.

You look in ``.fancy_snapshots/5/info.txt`` and it says::

    Date: April 4 2012, 01.40
    Author: I. M. Awesome
    Notes: I always get the best ideas after I have been drinking.

Aha. Then you find the problem in ``very_clever_analysis.py`` very quickly
since instead of eyeballing the entire script you need to inspect only the
difference between ``.fancy_snapshots/4/very_clever_analysis.py`` and
``.fancy_snapshots/5/very_clever_analysis.py`` to find the not so ingenious
*optimization* you have introduced.

You fix ``very_clever_analysis.py``.

You make a new snapshot ``.fancy_snapshots/6``.

.. prizerun::
    :hide:

    # Cheat to make a new commit
    cp -r .fancy_snapshots/3 .fancy_snapshots/6

Back on track for a scientific breakthrough.

Terminology breakout
====================

Here are some
`terms <https://www.kernel.org/pub/software/scm/git/docs/gitglossary.html>`__.

Working tree
    The files you are working on in the current directory (``nobel_prize``).
    The files ``very_clever_analysis.py``, ``nobel_prize_paper.txt``,
    ``stunning_figure.png`` are the files in your working tree.

Repository
    The directory containing the snapshots. Your directory ``.fancy_snapshot``
    is the repository.

Commit
    A completed snapshot. For example, ``.fancy_snapshots/1`` contains one
    commit.

We'll use these terms to get used to them.

Breaking up work into chunks
============================

You did some edits to the paper ``nobel_prize_paper.txt`` to edit the
introduction.

You also had a good idea for the analysis, and did some edits to
``very_clever_analysis.py``.

You've got used to breaking each new *commit* (snapshot) up into little
bits of extra work, with their own comments in the ``info.txt``.

You want to make two commits from your changes:

1. a commit containing the changes to ``nobel_prize_paper.txt``, with comment
   "Changes to introduction";
2. Another commit that adds the changes to ``very_clever_analysis.py`` with
   comment "Crazy new analysis"

How can I do that?

The staging area
================

You adapt the workflow. Each time you have done a commit, you copy the contents of
the commit to directory ``.fancy_snapshots/staging_area``. That will be the
default contents to be considered for your next commit.

.. prizerun::

    mkdir .fancy_snapshots/staging_area
    cp .fancy_snapshots/6/files/* .fancy_snapshots/staging_area
    ls .fancy_snapshots/staging_area

Now, you do your edits to ``nobel_prize_paper.txt``, and
``very_clever_analysis.py`` in your *working tree* (the ``nobel_prize``
directory). You want to make a new commit containing the changes to the paper
but not the changes to the script.  You get ready for the next commit with:

.. prizerun::

    cp nobel_prize_paper.txt .fancy_snapshots/staging_area

The staging area now contains all the files for the upcoming commit
(snapshot). The upcoming commit only has the changes in the paper.

You make the commit by making the usual directories, and copying the contents
of the staging area into the commit ``files`` directory:

.. prizerun::

    # Make commit directories
    mkdir .fancy_snapshots/7
    mkdir .fancy_snapshots/7/files
    # Copy contents of staging area into commit directory
    cp .fancy_snapshots/staging_area/* .fancy_snapshots/7/files

with message:

.. prizewrite::

    # file: .fancy_snapshots/7/info.txt
    Date: April 10 2012, 14.30
    Author: I. M. Awesome
    Notes: Changes to introduction

After we have done the commit this way, by copying the staging area to the
commit ``files`` directory, the staging area is identical to the contents of
the commit.  Now you are ready for the next commit, that adds the changes to
the analysis script.

.. prizerun::

    cp very_clever_analysis.py .fancy_snapshots/staging_area

The commit is now *staged* and ready to be saved.

.. prizerun::

    # Our commit procedure; same as last time with "8" instead of "7"
    mkdir .fancy_snapshots/8
    mkdir .fancy_snapshots/8/files
    cp .fancy_snapshots/staging_area/* .fancy_snapshots/8/files

The message is:

.. prizewrite::

    # file: .fancy_snapshots/8/info.txt
    Date: April 10 2012, 14.35
    Author: I. M. Awesome
    Notes: Crazy new analysis

Here is what you have in your ``.fancy_snapshots`` directory:

.. prizerun::

    tree -a .fancy_snapshots

Now the very difficult problem
==============================

Let's say that the figure ``stunning_figure.png`` is large.

Let's say it changes only once across our 8 commits, at commit 5, but
we keep copying it again and again to new commits.

What should we do to save disk space for ``.fancy_snapshots``?

Cryptographic hashes
====================

This section describes "Crytographic hashes". These are the key to an
excellent way to store our snapshots.  Later we will see that they are central
to the way that git works.

See : `Wikipedia on hash
functions <http://en.wikipedia.org/wiki/Cryptographic_hash_function>`__.

A *hash* is the result of running a *hash function* over a block of
data. The hash is a fixed length string that is the *signature* of that
exact block of data.  One common hash function is called SHA1.  Let's run this
via the command line:

.. desktoprun::

    # Make a file with a single line of text
    echo "git is a rude word in UK English" > git_is_rude
    # Show the SHA1 hash
    shasum git_is_rude

Not too exciting so far. However, the rather magical nature of this string is
not yet apparent. This SHA1 hash is a *cryptographic* hash because

* the hash value is (almost) unique to this exact file contents, and
* it is (almost) impossible to find some other file contents with the same
  hash

In other words, there is no practical way for you to find another file with
different contents that will give the same hash.

For example, a tiny change in the string makes the hash completely different.
Here I've just added a full stop at the end:

.. desktoprun::

    echo "git is a rude word in UK English." > git_is_rude_stop
    shasum git_is_rude_stop

So, if you give me some data, and I calculate the SHA1 hash value, and it
comes out as ``30ad6c360a692c1fe66335bb00d00e0528346be5``, then I can be very
sure that the data you gave me was exactly the ASCII string "git is a rude
word in UK English".

Hashes make excellent unique file names
=======================================

Remember that our problem was that many commits may have exactly the same
contents for individual files.

For example, maybe commits 1 through 4 have exactly the same contents for the
file ``stunning_figure.png``.  The figure changes for commit 5 but stays the
same through to commit 8.  Can we find a way to store only the two different
versions of this file, and make our commits point to one of these two
versions?

We can do this by storing only the two unique versions of
``stunning_figure.png`` with unique filenames.  Maybe store the first version
of the file as ``stunning_figure.png.v1`` and the second as
``stunning_figure.png.v2``.  Then make commits 1 through 4 point to
``stunning_figure.png.v1`` for ``stunning_figure.png``, and make commits 5
through 8 point to ``stunning_figure.png.v2``.

But, ``stunning_figure.png.v1`` and ``stunning_figure.png.v2`` are not very
good unique filenames.  What happens if Josephine and I are working at the
same time on different copies of ``nobel_prize``, and she and I both make a
new version of the figure.  My version will be called
``stunning_figure.png.v3`` and her version will have the same name, but they
will be different, and this will cause a mess if we try and merge our work.

What if we use the file hash values as unique filenames?

First we make a directory to put the unique file contents:

.. prizerun::

    mkdir .fancy_snapshots/objects

We calculate the hash for the first version of the figure:

.. prizerun::

    shasum .fancy_snapshots/1/files/stunning_figure.png

We copy the first version of the figure there using its hash as a unique
filename:

.. prizerun::

    cp .fancy_snapshots/1/files/stunning_figure.png .fancy_snapshots/objects/aff88ecead2c7166770969a54dc855c8b91be864

We do the same thing for the second version of the figure:

.. prizerun::

    shasum .fancy_snapshots/5/files/stunning_figure.png

.. prizerun::

    cp .fancy_snapshots/5/files/stunning_figure.png .fancy_snapshots/objects/f86b56e97a3378f313eccadf78ab0a74ce56049f

We will tell commits 1 through 4 to use
``.fancy_snapshots/objects/aff88ecead2c7166770969a54dc855c8b91be864`` for
``stunning_figure.png``.

Commits 5 though 8 will use
``.fancy_snapshots/objects/f86b56e97a3378f313eccadf78ab0a74ce56049f`` for
``stunning_figure.png``.

Now what happens if Josephine and I make different figures and do the same
procedure of copying to ``.fancy_snapshots/objects``?  If our two figures are
different, then they will have different hashes, and therefore different
filenames, so they will not clash with each other.  If the two figures happen
to be the same, then they will have the same hash, and the same filename, so
my object contents and filename will be identical to Josephine's and there is
still no clash.

Building the first commit from unique files
===========================================

Let's use this idea to build a whole commit.

Copying the files
-----------------

So we don't get our commits mixed up, I'll delete everything in the
``objects`` directory and start again:

.. prizerun::

    rm -rf .fancy_snapshots/objects/*

Copy the figure for the first commit again:

.. prizerun::

    cp .fancy_snapshots/1/files/stunning_figure.png .fancy_snapshots/objects/aff88ecead2c7166770969a54dc855c8b91be864

Do the same for the other two files in the first commit.

.. prizerun::

    shasum .fancy_snapshots/1/files/nobel_prize_paper.txt

.. prizerun::

    cp .fancy_snapshots/1/files/nobel_prize_paper.txt .fancy_snapshots/objects/3af8809ecb9c6dec33fc7e5ad330e384663f5a0d

.. prizerun::

    shasum .fancy_snapshots/1/files/very_clever_analysis.py

.. prizerun::

    cp .fancy_snapshots/1/files/very_clever_analysis.py .fancy_snapshots/objects/e7f3ca9157fd7088b6a927a618e18d4bc4712fb6

We now have three new files in ``.fancy_snapshots/objects``, one corresponding
to each unique file *contents* we have hashed:

.. prizerun::

    tree -a .fancy_snapshots/objects

Making the directory listing
----------------------------

We now need to associate the filenames in the commit snapshot with the file
contents we have just copied to ``.fancy_snapshots/objects``. For example, we
have to tell the commit that the file named ``stunning_figure.png`` in first
commit should get its contents from
``.fancy_snapshots/objects/aff88ecead2c7166770969a54dc855c8b91be864``.

We do this by making a *directory listing* for the commit:

.. prizewrite::

    # file: .fancy_snapshots/1/directory_list
    Filename                Hash value
    ========                ==========
    nobel_prize_paper.txt   3af8809ecb9c6dec33fc7e5ad330e384663f5a0d
    stunning_figure.png     aff88ecead2c7166770969a54dc855c8b91be864
    very_clever_analysis.py e7f3ca9157fd7088b6a927a618e18d4bc4712fb6

The next commit - saves space!
==============================

Now we work on the files from the second commit. These are the files in
``.fancy_snapshots/2/files``.

Remember that we only changed ``nobel_prize_paper.txt`` in this commit.

Do we need to copy ``nobel_prize_papar.txt`` or ``stunning_figure.png`` to the
objects directory again?

No - because they have not changed.  Because they have not changed, their hash
values have not changed.  Because their hash values have not changed, and
because the hash values are unique to the contents, we already have the files
we need in the ``objects`` directory.

Specifically, because ``.fancy_snapshots/2/stunning_figure.png`` is the same as
``.fancy_snapshots/1/stunning_figure.png`` we know that the hash is the same,
and the hash is the one we've already calculated,
``aff88ecead2c7166770969a54dc855c8b91be864``.  We already have a file
``.fancy_snapshots/objects/aff88ecead2c7166770969a54dc855c8b91be864``, and we
know, because the hash values are unique, that this file must contain the
exact contents of ``.fancy_snapshots/1/stunning_figure.png``, which is also the
exact contents of ``.fancy_snapshots/2/stunning_figure.png``.

So in general, if we do a hash on a file, and then we find a filename the same
as this hash in the objects directory, we already have that exact contents
backed up, and we don't need to do a copy.

Here are the hashes for the files in commit 2:

.. prizerun::

    shasum .fancy_snapshots/2/files/nobel_prize_paper.txt
    shasum .fancy_snapshots/2/files/stunning_figure.png
    shasum .fancy_snapshots/2/files/very_clever_analysis.py

We already have files ``aff88ecead2c7166770969a54dc855c8b91be864`` and
``e7f3ca9157fd7088b6a927a618e18d4bc4712fb6`` in ``.fancy_snapshots/objects``, so
the only file we need to copy is ``nobel_prize_paper.txt``:

.. prizerun::

    cp .fancy_snapshots/2/files/nobel_prize_paper.txt .fancy_snapshots/objects/90aa1015732676bf63d2d950714a1f11196875fc

Here then is our directory listing for commit 2:

.. prizewrite::

    # file: .fancy_snapshots/2/directory_list
    Filename                Hash value
    ========                ==========
    nobel_prize_paper.txt   90aa1015732676bf63d2d950714a1f11196875fc
    stunning_figure.png     aff88ecead2c7166770969a54dc855c8b91be864
    very_clever_analysis.py e7f3ca9157fd7088b6a927a618e18d4bc4712fb6

Storing the directory listing as a file
=======================================

The directory listing is just a text file.  We can store the directory listing
as we store the other files, by writing an object file with the file hash.
Here is the hash for the directory listing of the first commit:

.. prizerun::

    shasum .fancy_snapshots/1/directory_list

We copy that file to the objects directory as we have for the other files:

.. prizerun::

    cp .fancy_snapshots/1/directory_list .fancy_snapshots/objects/b7c9cd682e7d4bf28b82e76fb2276608f49e16d5

We can do the same for the second commit:

.. prizerun::

    shasum .fancy_snapshots/2/directory_list

.. prizerun::

    cp .fancy_snapshots/2/directory_list .fancy_snapshots/objects/4f379c649a596d2f9cc2cf5b91f4a67a3101b65e

Would I get the same hash for the directory listing if I had had a different
figure? (answer [#list-figure]_).

Storing the commit information as a file
========================================

We've seen that we can make a directory listing file that is unique for the
whole contents of the snapshot files (file contents and file names).
Therefore the *hash* of the directory listing file is also unique for the
contents of the snapshot files.  So, we can make a *commit file* that is the
``info.txt`` file above, but now with the hash of the directory listing file
included.  Adding the hash of the directory listing file means that this
*commit file* is now also unique to the contents of the snapshot.  The commit
file for the first commit might look something like this:

.. prizewrite::

    # file: .fancy_snapshots/1/commit
    Tree: b7c9cd682e7d4bf28b82e76fb2276608f49e16d5
    Date: April 1 2012, 14.30
    Author: I. M. Awesome
    Notes: First backup of my amazing idea

The ``Tree:`` field gives the hash of the directory listing file for this
commit.

The commit file for the second commit might look like this:

.. prizewrite::

    # file: .fancy_snapshots/2/commit
    Tree: 4f379c649a596d2f9cc2cf5b91f4a67a3101b65e
    Date: April 1 2012, 18.03
    Author: I. M. Awesome
    Notes: Fruit of enormous thought

The commit is now just a text file, and I can hash and store this too:

.. prizerun::

    shasum .fancy_snapshots/1/commit
    shasum .fancy_snapshots/2/commit

.. prizerun::

    cp .fancy_snapshots/1/commit .fancy_snapshots/objects/2f5e799e614b4e80c6f5a035ac29e1d16855e409
    cp .fancy_snapshots/2/commit .fancy_snapshots/objects/015447b3e9ec05f476a6daf42484dd28c021e8a7

Would the commit hash value change if the figure changed?  (answer
[#commit-figure]_).

Now look in ``.fancy_snapshots/objects``:

.. prizerun::

    tree -a .fancy_snapshots/objects

That's 3 file copies for the files from the first commit, 1 file copy from the
second commit, 2 directory listings (for first and second commit) and two
commit listings (for the first and second commit) = 8 hash objects.

Linking the commits
===================

Can we completely get rid of our snapshot directories ``.fancy_snapshots/1``,
``.fancy_snapshots/2``?

The reason for our commit names "1", "2", "3" was so we know that commit "2"
comes after commit "1" and before commit "3". Now our commits have hash value
filenames, we can't tell the order from the name.

However, the commit hash does uniquely identify the commit.  For example, we
know that the file beginning ``2f5e799`` completely defines the first
commit:

.. prizerun::

    cat .fancy_snapshots/objects/2f5e799e614b4e80c6f5a035ac29e1d16855e409

We can specify the order by adding the commit hash of the previous (parent)
commit into the current commit. For example, we can make the second commit
point back to the first commit like this:

.. prizewrite::

    # file: .fancy_snapshots/2/info.txt
    Tree: 4f379c649a596d2f9cc2cf5b91f4a67a3101b65e
    Parent: 2f5e799e614b4e80c6f5a035ac29e1d16855e409
    Date: April 1 2012, 18.03
    Author: I. M. Awesome
    Notes: Fruit of enormous thought

Now we have the order of the commits from the links between them, where the
links are given by the hash value in the ``Parent:`` field.

****************************
Git rides in to save the day
****************************

Now you have built your own "fancy_snaphots" system, you know how git works
|--| because it works in exactly the same way.  You will recognize hashes for
files, directories and commits, commits linked by reference to their parents,
the staging area, and the ``objects`` directory.

Armed with this deep_ understanding, we start to explore git.

.. _deep: http://rogerdudler.github.io/git-guide

.. note::

    The rest of this presentation started off as Fernando Perez' git tutorial
    in his `reproducible software repository
    <https://github.com/fperez/reprosw>`__. I changed it quite a bit, so
    please blame me rather than Fernando if you don't like what you read next.

Basic configuration
===================

We need to tell git about us before we start. This stuff will go into
the commit information by default.

.. prizerun::

    git config --global user.name "Matthew Brett"
    git config --global user.email "matthew.brett@gmail.com"

git often needs to call up a text editor. Choose the editor you like here::

    # gedit is a reasonable choice for Linux
    git config --global core.editor gedit

We also turn on the use of color, which is very helpful in making the
output of git easier to read:

.. prizerun::

    git config --global color.ui "auto"

Getting help
============

.. prizerun::

    git

Try ``git help add`` for an example.

Initializing the repository
===========================

We first set this ``nobel_prize`` directory to be version controlled with git.
We start off the working tree with the original files for the paper:

.. prizerun::
    :hide:

    # Delete the current contents of 'nobel_prize'
    rm -rf *
    rm -rf .fancy_snapshots

.. desktoprun::

    unzip -o nobel_prize_files.zip

Create the git repository:

.. desktoprun::

    cd nobel_prize
    git init

What happened when we did ``git init``? Just what we were expecting; we have a
new *repository* directory called ``.git``

.. prizerun::

    ls .git

The ``objects`` directory looks familiar. What's in there?

.. prizerun::

    tree .git/objects

Nothing but a couple of empty directories. That makes sense.

.. _git-add:

git add - put stuff into the staging area
=========================================

.. prizerun::

    git add nobel_prize_paper.txt

.. prizerun::

    tree .git/objects

Doing ``git add nobel_prize_paper.txt`` has added a file to the
``.git/objects`` directory. That filename looks suspiciously like a
hash.

We expect that ``git add`` added the file to the *staging area*.  We will need
to use a command ``git status`` to check that, and that will come soon.

Looking at real git objects
===========================

Git objects are nearly as simple as the objects we were writing in
``.fancy_snapshots``.

The main difference is that, to save space, they are compressed, in fact
using a library called ``zlib``.

These objects are so simple that it's very easy to write small code snippets
to read them - see :doc:`reading_git_objects`.

Git will also show the contents of objects with the command ``git cat-file
-p``.

.. note::

    I will use ``git cat-file -p`` to display the content of nearly raw git
    objects, because this shows the great simplicity of git's internal model.
    I doubt you will need this command in your daily git work |--| I had never
    used it before I wrote this tutorial.  So, feel free to forget the
    ``cat-file`` command as soon as you finish this tutorial.

.. prizevar:: initial-paper-hash

    git hash-object nobel_prize_paper.txt

.. prizevar:: initial-paper-hash-fname

    obj={{ initial-paper-hash }}
    echo \${obj:0:2}/\${obj:2}

.. prizevar:: initial-paper-hash-dirname

    obj={{ initial-paper-hash }}
    echo \${obj:0:2}

When we did ``git add nobel_prize_paper.txt``, we got a new file in
``.git/objects``, with filename |initial-paper-hash-fname|.  The filename is
in fact a hash, where the first two digits form the directory name
(|initial-paper-hash-dirname|) and the rest of the digits are the filename
[#git-object-dir]_.

Here's the contents of the object:

.. prizerun::

    git cat-file -p {{ initial-paper-hash }}

Just as we expected, it is the current contents of the
``nobel_prize_paper.txt``.

In fact we only need to give git enough hash digits for git to uniquely
identify the object.  7 digits is often enough, as in:

.. prizevar:: initial-paper-hash-7

    obj={{ initial-paper-hash }}
    echo \${obj:0:7}

.. prizerun::

    git cat-file -p {{ initial-paper-hash-7 }}

git status - showing the status of files in the working tree
============================================================

The working tree is the ``nobel_prize`` directory.  It now contains a
repository |--| the ``.git`` directory.

``git status`` tells us about the relationship of the files in the working
tree to the repository and staging area.

We have done a ``git add`` on ``nobel_prize_paper.txt``, and that added the
file to the staging area.  We can see that with ``git status``:

.. prizerun::

    git status

Sure enough, the output tells that ``new file: nobel_prize_paper.txt`` is in
the ``changes to be committed``.  It also tells us that the other two files
|--| ``stunning_figure.png`` and ``very_clever_analysis.py`` |--| are
``untracked``.

An untracked file is a file with a filename that has never been added to the
repo with ``git add``.  Until you ``git add`` an untracked file, git will
ignore these files and assume you don't want to keep track of them.

Staging the other files
=======================

We do want to keep track of the other files, so we stage them:

.. prizerun::

    git add stunning_figure.png
    git add very_clever_analysis.py
    git status

We have now staged all three of our files.  We have three objects in
``.git/objects``:

.. prizerun::

    tree .git/objects

git commit - making the snapshot
================================

.. prizecommit:: initial 2012-04-01 11:13:13

    git commit -m "First backup of my amazing idea"

.. note::

    In the line above, I used the ``-m`` flag to specify a message at the
    command line. If I had not done that, git would open the editor we
    specified in our configuration above and ask me to enter a message.  I'm
    using the ``-m`` flag so the commit command runs without interaction in
    this tutorial, but in ordinary use, I virtually never use ``-m``, and I
    suggest you don't either.  Using the editor for the commit message allows
    you to write a more complete commit message, and gives feedback about the
    ``git status`` of the commit to remind you what you are about to do.

We are now expecting to have two new ``.git/object`` files, for the directory
tree, and for the commit.

.. prizerun::

    tree .git/objects

Here's the contents of the tree object (directory listing):

.. prizerun::

    git cat-file -p e129806

These are in fact the file permissions, the type of the entry in the directory
(where "tree" means a sub-directory, and "blob" means a file), the file
hashes, and the file names (see :ref:`git-object-types`).

Here is the contents of the commit object:

.. prizerun::

    git cat-file -p {{ initial }}

git log - what are the commits so far?
======================================

.. prizerun::

    git log

Notice that git log identies each commit with its hash.  As we saw above, the
hash for our commit was |initial|.

We can also ask to the see the parents of each commit in the log:

.. prizerun::

    git log --parents

Why are the output of ``git log`` and ``git log --parents`` the same in this
case? (answer [#no-parents]_).

git branch - which branch are we on?
====================================

Branches are bookmarks. They associate a name (like "master") with a commit
(such as |initial|).

The default branch for git is called ``master``. Git creates it
automatically when we do our first commit.

.. prizerun::

    git branch

Asking for more verbose detail shows us that the branch is pointing to a
particular commit (where the commit is given by a hash):

.. prizerun::

    git branch -v

A branch is just a name that points to a commit.  In fact, git stores branches
as tiny text files, where the filename is the name of the branch, and the
contents is the hash of the commit that it points to:

.. prizerun::

    ls .git/refs/heads

.. prizerun::

    cat .git/refs/heads/master

We will soon see that, if we are working on a branch, and we do a commit, then
git will update the branch to point to the new commit.

git diff - what has changed?
============================

Let's do a little bit more work... Again, in practice you'll be editing
the files by hand, here we do it via shell commands for the sake of
automation (and therefore the reproducibility of this tutorial):

.. prizerun::

    echo "The charts are very impressive" >> nobel_prize_paper.txt

And now we can ask git what is different:

.. prizerun::

    git diff

You need to ``git add`` a file to put it into the staging area
==============================================================

Remember that git only commits stuff that has been added to the staging
area.

At the moment we have changes that have not been staged:

.. prizerun::

    git status

Git status tells us that git is keeping track of changes to
``nobel_prize_paper.txt``, and that the file has changed since the last
commit, and that we haven't yet put these changes into the staging area.

If we try and do a commit, git will tell us there is nothing to commit,
because nothing has been put into the staging area (staged):

.. prizerun::

    git commit

The cycle of git virtue: work, add, commit, work, add, commit...
================================================================

Now we have the main commands we need for working with git.  The typical cycle
is:

* do some changes in the working tree;
* ``git status`` and ``git diff`` to review what has changed;
* ``git add`` to add the changes to the staging area;
* ``git commit``;

and repeat, and repeat.

For example, we have now done some work in the working tree.

We first check what changes there are that we might want to commit:

.. prizerun::

    git status

We might want to check what these changes are before we stage them:

.. prizerun::

    git diff

We add the changes for the one edited file to the staging area:

.. prizerun::

    git add nobel_prize_paper.txt

We might check again that git will add the changes to the next commit:

.. prizerun::

    git status

We do the commit:

.. prizecommit:: fruit 2012-04-01 12:01:01

    git commit -m "Fruit of enormous thought"

Adding with ``-a``
==================

So far we have done our commits with at least two steps: ``git add`` and
then ``git commit``.  It also possible to add files and commit the snapshot in
one step, using ``git commit -a``.

Here is an example.  We first add some text to a couple of files that we are
already tracking:

.. prizerun::

    echo "More self congratulation" >> nobel_prize_paper.txt
    echo "fudge_factor = 42" >> very_clever_analysis.py

Then we create another file that git knows nothing about:

.. prizerun::

    echo "My cat Arthur for dribbling on my keyboard" > acknowledgments.txt

.. prizerun::

    git status

Git tells us, as expected, that we have two modified files that have not been
staged, and one untracked file.   You also see that git has two suggestions
for us |--| that we do ``git add`` on the modified files (as we have done up
to now) |--| and / or ``git commit -a`` (the subject of this section).

Adding the ``-a`` flag to ``git commit`` means that git will:

* ``add`` any modified, tracked files to the staging area
* ``commit`` these modified files.

In our case, it is a shortcut for::

    git add nobel_prize_paper.txt
    git add an_experiment.txt
    git commit

The ``-a`` flag to ``git commit`` ignores any untracked files |--| so
``acknowledgments.txt`` will still be untracked:

.. prizecommit:: a-flag 2012-04-01 12:13:01

    git commit -a -m "Paper ready for submission to Science"

.. prizerun::

    git status

Maybe we aren't ready for the acknowledgments yet, so we'll delete that file
for now:

.. prizerun::

    rm acknowledgments.txt

If you have used other version control systems, the ``-a`` flag may look like
a good idea, but experienced users often find that they use ``git add`` and
``git commit`` much more often than they use ``git commit -a``.  The explicit
``git add`` helps us think about the changes we are adding, whereas the ``-a``
flag makes it easier to add changes we didn't intend.  So I recommend you
always use ``git add``, especially when you are learning, and that's how I'm
going to do the rest of the examples in this tutorial.

Git updates the current branch with the latest commit
=====================================================

Remember branches (bookmarks)?  Git has now moved the "master" branch location
up to the new commit:

.. prizerun::

    git log

.. prizerun::

    git branch -v

.. prizerun::

    cat .git/refs/heads/master

Our most recent commit (|a-flag|) has the previous commit (|fruit|) as a
parent.  |fruit| has |initial| as a parent.  |initial| is the first commit,
and has no parent:

.. prizerun::

    git log --parents

A nicer log command using ``git config``
========================================

It is very often useful to see a summarized version of the log.  Here is a
useful set of command flags for git log:

.. prizerun::

    git log --oneline --topo-order --graph

Git supports *aliases:* new names given to command combinations. Let's
make this handy shortlog an alias, so we only have to type ``git slog``
to get this compact log.

We create our alias (this saves it in git's permanent configuration file):

.. prizerun::

    git config --global alias.slog "log --oneline --topo-order --graph"

And now we can use it:

.. prizerun::

    git slog

.. _git-tag:

git tag makes a static bookmark
===============================

We have seen that a branch is a bookmark that points to a commit.

When we do a new commit on a branch, the branch points to the new commit.

We can also make bookmarks that do not update when we do a commit.  These are
git *tags*.

Imagine we are now happy with the state of our paper, and we submit it to
Science.

We want to record the exact state of the paper when we sent it to Science,
because we may need to go back to this version when those nasty Science
reviewers ask us to make changes.

We could write down the current commit hash somewhere, but then we might lose
it.  Luckily ``git tag`` has a better solution:

.. prizerun::

    git tag submitted-to-science

We now have a tag with name ``submitted-to-science`` that points to this
commit.  As we will see, unlike a branch, the tag does not change when you do
another commit.

The current commit is:

.. prizerun::

    git branch -v

``git tag`` without arguments will list the current tags:

.. prizerun::

    git tag

As you can imagine, this tag is very similar to a branch |--| it is just a
tiny file where the name of the file is the tag name, and the contents is the
commit hash:

.. prizerun::

    ls .git/refs/tags

.. prizerun::

    cat .git/refs/tags/submitted-to-science

This type of tag is what git calls a *lightweight* tag |--| it is just a
bookmark to a commit, with no other information.

We often want to add some extra information to our tags, such as a message
about what the tag is for, the date, and the person doing the tag.  We can do
this with *annotated tags*. To make an annotated tag, use the ``-a`` flag to
``git tag`` and give a tag message:

.. prizecommit:: dummy-label 2012-04-01 12:13:34

    git tag -a -m "Submitted to Science" annotated-to-science

.. note::

    I'm using the the ``-m`` flag as I have for the commits, and for the same
    reason.  Normally you would not use ``-m`` and let the text editor open
    for you to type the tag message.

.. prizerun::

    git tag

There is still a tiny text file for the tag:

.. prizerun::

    ls .git/refs/tags

This time the tag does not point to a commit hash, but to a new tag object:

.. prizerun::

    cat .git/refs/tags/annotated-to-science

.. prizevar:: annotated-to-science

    cat .git/refs/tags/annotated-to-science

The new tag object is very much like a commit object, but pointing to a commit
object rather than a tree object:

.. prizerun::

    git cat-file -p {{ annotated-to-science }}

Unlike a branch, a tag does not change when we make a commit:

.. prizerun::

    git branch -v

.. prizecommit:: after-science 2012-04-01 12:13:01

    echo "Actually, maybe this is all wrong" >> nobel_prize_paper.txt
    git add nobel_prize_paper.txt
    git commit -a -m "Luckily we submitted the paper before we realized"

The branch bookmark has changed:

.. prizerun::

    git branch -v

But the tags have not changed:

.. prizerun::

    cat .git/refs/tags/submitted-to-science

.. prizerun::

    cat .git/refs/tags/annotated-to-science

You have now seen all the possible types of objects in ``.git/objects``.  See
:ref:`git-object-types` for a review.

git mv and rm: moving and removing files
========================================

While ``git add`` is used to add files to the list of files that git tracks,
we must also tell it if we want their names to change or for it to stop
tracking them. In familiar Unix fashion, the ``mv`` and ``rm`` git commands do
precisely this:

.. prizerun::

    git mv very_clever_analysis.py slightly_dodgy_analysis.py
    git status

Note that these changes must be committed too, if we want to keep them.

.. prizecommit:: new-name 2012-04-01 13:15:01

    git commit -m "I like this new name better"

As you can imagine, all that changed is that the tree object for this commit
associates the new filename with the old hash for the file.  I will use the
obscure ``git ls-tree`` command as a shortcut to show me the contents of the
root tree object for this commit:

.. prizerun::

    git ls-tree master

``git rm`` works in a similar fashion.

git branch again - making a new branch
======================================

Remember that a branch is simply a bookmark pointing to a particular commit.

We can have multiple branches in a repository, where each one may point to a
different commit.

At the moment, we only have one branch, "master":

.. prizerun::

    git branch -v

We can make a new branch with the ``git branch`` command:

.. prizerun::

    git branch experiment
    git branch -v

The new branch, by default, points to the commit we are currently on, and this
commit is the also the commit that the "master" branch points to.  So, at the
moment "master" and "experiment" point to the same commit.

Of course this new branch is also just a tiny text file with the name of the
branch and the commit that it currently points to:

.. prizerun::

    ls .git/refs/heads

.. prizerun::

    cat .git/refs/heads/experiment

git ``HEAD`` points to the current branch
=========================================

Remember that, when you make a new commit, git moves the branch pointer to
point at the new commit.  Now we have two branches, how does git know which is
the current branch, in order to move the right branch pointer when you make a
commit?

This information goes in a special git pointer, called ``HEAD``.  HEAD stores
the name of the current branch. HEAD is just a tiny text file storing the
current branch:

.. prizerun::

    cat .git/HEAD

Here, HEAD is pointing to the "master" branch.  The master branch is pointing
to a particular commit:

.. prizerun::

    git branch -v

Heads and refs
==============

For git, a *reference* or a *ref* is a name that refers to another object. A
reference almost invariably points to a commit object [#tag-other-objects]_.

For example a branch is a reference, and so is a tag. Remember that git stores
branches and tags in ``.git/refs``.

A *head* is a reference to a commit that updates when we do ``git commit``.
All branches are heads.  A tag is not a head because it does not update when
we do a commit.  Remember that git stores branches in ``.git/refs/heads``.
The special reference HEAD points to the current head that we are working on.

Developing on different branches
================================

Let's now illustrate all of this with a concrete example. We get our bearings
first:

.. prizerun::

    git status

.. prizerun::

    ls

We are now going to try two different routes of development: we will add one
file on on the ``experiment`` branch, and we will add another file on the
``master`` branch. We will then merge the ``experiment`` branch into
``master``.

How do we start working on this branch ``experiment`` rather than
``master``?

git checkout - set the current branch, set the working tree from a commit
=========================================================================

Up until now we have been on the ``master`` branch. When we make a
commit, the ``master`` branch pointer (``.git/refs/heads/master``) moves
up to point to our most recent commit.

``git checkout`` can switch us to using another branch:

.. prizerun::

    git checkout experiment

What just happened?

The current branch has changed (notice the asterisk in front of
``experiment``).

.. prizerun::

    git branch -v

Git ``HEAD`` now points to the ``experiment`` branch:

.. prizerun::

    cat .git/HEAD

As we'll see later, ``git checkout somebranch`` also sets the contents
of the working tree to match the commit contents for ``somebranch``. In
this case the commits for ``master`` and ``experiment`` are currently
the same, meaning they have the same corresponding working tree, so our
working tree did not change.

Now let's do some changes on the ``experiment`` branch:

.. prizecommit:: an-experiment 2012-04-01 13:13:13

    echo "Some crazy idea" > an_experiment.txt
    git add an_experiment.txt
    git commit -m "Trying something new"

.. prizerun::

    git slog

Notice we have a new file called ``an_experiment.txt`` in this branch:

.. prizerun::

    ls

The ``experiment`` branch pointer has now moved up to the current commit:

.. prizerun::

    cat .git/refs/heads/experiment

But the ``master`` branch points to the same place as it did before:

.. prizerun::

    git branch -v

git checkout again - get a new working tree
===========================================

If ``somewhere`` is a branch name, then ``git checkout somewhere``
selects ``somewhere`` as the current branch. It also resets the working
tree to match the working tree for that commit.

.. prizerun::

    git checkout master

.. prizerun::

    cat .git/HEAD

We're back to the working tree as of the ``master`` branch;
``an_experiment.txt`` has gone now.

.. prizerun::

    ls

Meanwhile we do some more work on master:

.. prizerun::

    git slog

.. prizecommit:: boring 2012-04-01 13:43:13

    echo "All the while, more work goes on in master..." >> boring_idea.txt
    git add boring_idea.txt
    git commit -m "The mainline keeps moving"

.. prizerun::

    git slog

git merge - merging changes from different branches
===================================================

We do a merge when we have made some changes in one branch, and we want to
bring (merge) these changes into another branch.

The typical situation is where we have made some useful changes in an
experimental branch - like our ``experiment`` branch, and we decide we want to
make these changes part of the main line of development - often the ``master``
branch.

Here is the merge:

.. prizecommit:: merge-experiment 2012-04-01 14:01:10

    git merge experiment -m "Merge in the experiment"

Notice now that the master branch has the new file ``an_experiment.txt`` from
the ``experiment`` branch, as well as the new file ``boring_idea.txt`` from
the master branch:

.. prizerun::

    ls

The git log output shows us two lines of development diverging and then
merging:

.. prizerun::

    git slog

The merge commit has two "parent" commits, which are the previous commit on
``master`` and the previous commit on ``experiment``:

.. prizerun::

    git slog --parents

An important aside: conflict management
=======================================

While git is very good at merging, if two different branches modify the
same file in the same location, it simply can't decide which change
should prevail. At that point, human intervention is necessary to make
the decision. Git will help you by marking the location in the file that
has a problem, but it's up to you to resolve the conflict. Let's see how
that works by intentionally creating a conflict.

We start by making another new branch and making a change to our experiment
file:

.. prizerun::

    git branch trouble
    git checkout trouble

.. prizecommit:: trouble-starts 2012-04-01 14:23:13

    echo "This is going to be a problem..." >> an_experiment.txt
    git add an_experiment.txt
    git commit -m "Changes in the trouble branch"

And now we go back to the master branch, where we change the *same*
file:

.. prizecommit:: while-trouble 2012-04-01 14:29:13

    git checkout master
    echo "More work on the master branch..." >> an_experiment.txt
    git add an_experiment.txt
    git commit -m "Mainline work"

So now let's see what happens if we try to merge the ``trouble`` branch
into ``master``:

.. prizerun::

    git merge trouble -m "Unlikely this one will work"

Let's see what git has put into our file:

.. prizerun::

    cat an_experiment.txt

The text between ``<<``... and ``==``... gives the changes added by our
current branch |--| ``HEAD``.  These are the changes introduced by the branch
we are merging *into*.

The text between ``==``... and ``>>``... gives the changes added by the
``trouble`` branch.  These are the changes introduced by the branch we are
merging *from*.

At this point, we can go into the file with a text editor, decide which
changes to keep, and make a new commit that records our decision.

I decided that both pieces of text were useful, but I want to integrate them
with some changes.  I do the edits by writing the file I want directly:

.. prizewrite::

    # file: an_experiment.txt
    Some crazy idea
    More work on the master branch...
    This is no longer going to be a problem...

.. prizerun::

    git status

Let's then make our new commit:

.. prizecommit:: merge-trouble 2012-04-01 14:33:13

    git add an_experiment.txt
    git commit -m "Completed merge of trouble, fixing conflicts along the way"

.. prizerun::

    git slog

.. _git-graph:

Git commits form a graph
========================

The output of ``git slog`` shows the sequence of commits as a graph.

The nodes of the graph are the commits. Each commit has one or more parents,
stored in the commit object.  The parents give the links between the commit
and the previous commit(s) in the *commit history* graph.  The commit parents
therefore give links that form the edges in the graph.

git remotes - working with other people, making backups
=======================================================

Now you are keeping the history of your data, you also want to make sure you
have a backup in case your laptop dies.

You might want to work with a friend on the same project.  Perhaps your friend
Anne is also working on the paper, and you want to merge her changes into
yours.

We use git "remotes" to solve both of these problems.

Keeping backups with remotes
============================

Let's say you have an external backup disk and you want to record all the
history of your work on the backup disk.

To do this you need three steps:

* Make an empty backup repository on the external backup disk
* Point your current git repository at the backup repository with ``git remote
  add``
* Send the changes to the backup repository with ``git push``

Make the empty backup repository
--------------------------------

.. workvar:: usb_mountpoint
    :var_type: render

    echo "/Volumes/my_usb_disk"

.. workvar:: usb_mountpoint
    :var_type: run
    :omit_link:

    echo "\$PWD/repos"

Let's say your external disk is mounted at |usb_mountpoint|.

We make a new empty repository:

.. desktoprun::

    git init --bare {{ usb_mountpoint }}/nobel_prize.git

Notice the ``--bare`` flag.  This tells git to make a repository that does not
have a working tree, but only the ``.git`` repository directory:

.. desktoprun::

    ls {{ usb_mountpoint }}/nobel_prize.git

This is what we want in this case, because we will not ever want to edit the
files in the |usb_mountpoint| backup
repository, we will only be editing files in our local ``nobel_prize``
directory, committing those changes locally (as we have done above), and then
"pushing" these changes to the backup repository [#bare-detail]_.

Tell the current git repository about the backup repository
-----------------------------------------------------------

Check we're in our local git repository:

.. prizerun::

    pwd

Add a remote.  A remote is a link to another repository.

.. prizerun::

    git remote add usb_backup {{ usb_mountpoint }}/nobel_prize.git

List the remotes:

.. prizerun::

    git remote -v

The list shows that we can both ``fetch`` and ``push`` to this repository, of
which more later.

Git has written the information about the remote URL to the repository config
file |--| ``.git/config``:

.. prizerun::

    cat .git/config

git push |--| push all data for a local branch to the remote
------------------------------------------------------------

We now want to synchronize the data in our ``nobel_prize`` repository with the
remote ``usb_backup``.  The command to do this is ``git push``.

Before we do the push, there are no objects in the ``.git/objects`` directory
of the ``usb_backup`` backup repository:

.. desktoprun::

    tree -a {{ usb_mountpoint }}/nobel_prize.git/objects

Then we push:

.. prizerun::

    git push usb_backup master

This command tells git to take all the information necessary to reconstruct
the history of the ``master`` branch, and send it to the remote repository.
Sure enough, we now have files in ``.git/objects`` of the backup repository:

.. desktoprun::

    tree -a {{ usb_mountpoint }}/nobel_prize.git/objects

You'll see that the 'master' branch in the backup repository now points to the
same commit as the master branch in the local repository:

.. prizerun::

    cat .git/refs/heads/master

.. desktoprun::

    cat {{ usb_mountpoint }}/nobel_prize.git/refs/heads/master

The local repository has a copy of the last known position of the master
branch in the remote repository.

.. prizerun::

    cat .git/refs/remotes/usb_backup/master

You can see the last known positions of the remote branches using the ``-r``
flag to ``git branch``:

.. prizerun::

    git branch -r  -v

To see both local and remote branches, use the ``-a`` flag:

.. prizerun::

    git branch -a  -v

.. _git-push:

git push |--| synchronizing repositories
----------------------------------------

``git push`` is an excellent way to do backups, because it only transfers the
information that the remote repository does not have.

Let's see that in action.

First we make a new commit in the local repository:

.. prizecommit:: buffing 2012-04-01 15:13:13

    echo "Express confidence in the study" >> nobel_prize_paper.txt
    git add nobel_prize_paper.txt
    git commit -m "Buff up the paper some more"

Git updated the local ``master`` branch, but the remote does not know about
this update yet:

.. prizerun::

    git branch -a -v

We already know there will be three new objects in ``.git/objects`` after this
commit.  These are:

* a new blob (file) object for the modified ``nobel_prize_paper.txt``;
* a new tree (directory listing) object associating the new hash for the
  contents of ``nobel_prize_paper.txt`` with the ``nobel_prize_paper.txt``
  filename;
* the new commit object.

Usually we don't need to worry about which objects these are, but here we will
track these down to show how ``git push`` works.

The commit object we can see from the top of ``git log``. The ``-1`` flag to
``git log`` tells git to show just the most recent commit from the log:

.. prizerun::

    git log -1

So the commit is |buffing|. We can get the tree object from the commit object:

.. prizerun::

    git cat-file -p {{ buffing }}

We can show the tree object contents to get the object for the new
version of ``nobel_prize_paper.txt``.

.. depends on history

.. prizevar:: buffing-fname

    commit={{ buffing }}
    echo \${commit:0:2}/\${commit:2}

.. prizevar:: buffing-tree

    git log -1 --format="%T"

.. prizevar:: buffing-tree-fname

    tree={{ buffing-tree }}
    echo \${tree:0:2}/\${tree:2}

.. prizerun::

    git cat-file -p {{ buffing-tree }}

.. prizevar:: buffing-paper-obj

    git cat-file -p {{ buffing-tree }} | grep nobel_prize | awk '{print \$3}'

.. prizevar:: buffing-paper-obj-fname

    obj={{ buffing-paper-obj }}
    echo \${obj:0:2}/\${obj:2}

We do have these objects in the local repository:

.. prizerun::

    ls .git/objects/{{ buffing-fname }}
    ls .git/objects/{{ buffing-tree-fname }}
    ls .git/objects/{{ buffing-paper-obj-fname }}

|--| but we don't have these objects in the remote repository yet (we haven't
done a ``push``):

.. prizerun::

    REMOTE_OBJECTS={{ usb_mountpoint }}/nobel_prize.git/objects
    ls \$REMOTE_OBJECTS/{{ buffing-fname }}
    ls \$REMOTE_OBJECTS/{{ buffing-tree-fname }}
    ls \$REMOTE_OBJECTS/{{ buffing-paper-obj-fname }}

Now we do a push:

.. prizerun::

    git push usb_backup master

The branches are synchronized again:

.. prizerun::

    git branch -a -v

We do have the new objects in the remote repository:

.. prizerun::

    REMOTE_OBJECTS={{ usb_mountpoint }}/nobel_prize.git/objects
    ls \$REMOTE_OBJECTS/{{ buffing-fname }}
    ls \$REMOTE_OBJECTS/{{ buffing-tree-fname }}
    ls \$REMOTE_OBJECTS/{{ buffing-paper-obj-fname }}

You might also be able to see how git would work out what to transfer.  See
:doc:`git_push_algorithm` for how it could work in general, and for this case.

git clone |--| make a fresh new copy of the repo
------------------------------------------------

Imagine we have so far been working on our trusty work desktop.

We unplug the external hard drive, put it in our trusty bag, and take the
trusty bus back to our trusty house.

Now we want to start work on the paper.

We plug the hard drive into the laptop, it gets mounted again at
|usb_mountpoint|.

Now we want a repository with a working tree.

.. workrun::
    :hide:

    mkdir repos

The command we want is ``git clone``:

.. laptoprun::

    git clone {{ usb_mountpoint }}/nobel_prize.git

We have a full backup of the repository, including all the history:

.. laptoprun::

    cd nobel_prize
    git slog

git made a ``remote`` automatically for us, because it recorded where we
cloned from.  The default name for a git remote is ``origin``:

.. prizelaprun::

    git remote -v

Of course, just after the clone, the remote and the local copy are
synchronized:

.. prizelaprun::

    git branch -a -v

Now we could make some commits.

.. prizelapcommit:: wine-ideas 2012-04-01 20:13:31

    echo "The brain is a really big network." >> nobel_prize_paper.txt
    git add nobel_prize_paper.txt
    git commit -m "More great ideas after some wine"

The local copy is now ahead of the remote:

.. prizelaprun::

    git branch -a -v

At the end of the night's work, we push back to the remote on the USB disk:

.. prizelaprun::

    git push origin master

The local and remote are synchronized again:

.. prizelaprun::

    git branch -a -v

git fetch |--| get all data from a remote
-----------------------------------------

``git fetch`` fetches data from a remote repository into a local one.

Now we are back at the work desktop.  We don't have the great ideas from last
night in the local repository.  Here is the latest commit in the work desktop
repository:

.. prizerun::

    git log -1

Here are the branch positions in the work desktop repository:

.. prizerun::

    git branch -a -v

As you can see, the last known positions of the remote branches have not
changed from last night.  This reminds us that the last known positions only
get refreshed when we do an explicit git command to communicate with the
remote copy.  Git stores the "last known positions" in ``refs/remotes``.  For
example, if the remote name is ``usb_backup`` and the branch is ``master``,
then the last known position (commit hash) is the contents of the file
``refs/remotes/usb_backup/master``:

.. prizerun::

    cat .git/refs/remotes/usb_backup/master

The commands that update the last known positions are:

* ``git clone`` (a whole new copy, copying the remote branch positions with
  it);
* ``git push`` (copies data and branch positions to the remote repository, and
  updates last known positions in the local repository);
* ``git fetch`` (this section) (copies data and last known positions from
  remote repository into the local repository);
* ``git pull`` (this is just a ``git fetch`` followed by a ``git merge``).

Now we have plugged in the USB drive, we can fetch the data and last known
positions from the remote:

.. prizerun::

    git fetch usb_backup

The last known positions are now the same as those on the remote repository:

.. prizerun::

    git branch -a -v

We can set our local master branch to be the same as the remote master branch
by doing a merge:

.. prizerun::

    git merge usb_backup/master

This does a merge between ``usb_backup/master`` and local ``master``.  In this
case, the "merge" is very straightforward, because there have been no new
changes in local ``master`` since the new edits we have in the remote.
Therefore the "merge" only involves setting local ``master`` to point to the
same commit as ``usb_backup/master``.  This is called a "fast-forward" merge,
because it just involves advancing the branch pointer, rather than fusing two
lines of development:

.. prizerun::

    git slog

git pull |--| git fetch followed by git merge
---------------------------------------------

``git pull`` is just a shortcut for ``git fetch`` followed by ``git merge``.

For example, instead of doing ``git fetch usb_backup`` and ``git merge
usb_backup/master`` above, we could have done ``git pull usb_backup master``.
If we do that now, of course there is nothing to do:

.. prizerun::

    git pull usb_backup master

When you first start using git, I strongly recommend you always use an
explicit ``git fetch`` followed by ``git merge`` instead of ``git pull``.  It
is very common to run into problems using ``git pull`` that are made more
confusing by the fusion of the "fetch" and "merge" step.  For example, it is
not uncommon that you have done more work on a local copy, before you do an
innocent ``git pull`` from a repository with new work on the same file.  You
may well get merge conflicts, which can be rather surprising and confusing,
even for experienced users.  If you do ``git fetch`` followed by ``git
merge``, the steps are clearer so the merge conflict is less confusing and it
is clearer what to do.

Linking local and remote branches
---------------------------------

It can get a bit boring typing all of::

    git push usb_backup master

It may well be that we nearly always want to ``git push`` the ``master``
branch to ``usb_backup master``.

We can set this up using the ``--set-upstream`` flag to ``git push``.

.. prizerun::

    git push usb_backup master --set-upstream

Git then records this association in the ``.git/config`` file of the
repository:

.. prizerun::

    cat .git/config

We do a commit:

.. prizecommit:: no-network 2012-04-02 11:13:13

    echo "Is the network comment too obvious?" >> nobel_prize_paper.txt
    git add nobel_prize_paper.txt
    git commit -m "Rethinking the drinking again"

Now instead of ``git push usb_backup master`` we can just do ``git push``.

Before we try this, we need to set a default configuration variable to avoid a
confusing warning. See ``git config --help`` for more detail:

.. prizerun::

    git config push.default simple

.. prizerun::

    git push

Git also knows what to do if we do ``git fetch`` from this branch.

To show this at work, we go home to the laptop, fetch the desktop work from
the USB drive, and then do another commit from the laptop:

.. laptoprun::

    cd nobel_prize
    git fetch origin
    git merge origin/master

.. prizelapcommit:: convinced 2012-04-02 22:13:31

    echo "More convinced by networks." >> nobel_prize_paper.txt
    git add nobel_prize_paper.txt
    git commit -m "I think better at home"

Then push back to the USB disk, setting the link between the laptop branch and
the remote for good measure:

.. prizelaprun::

    git push origin master --set-upstream

Back to the work desktop to demonstrate "fetch" after we have done ``git
push`` with ``--set-upstream`` above:

.. prizerun::

    git fetch

We still need to do an explicit merge:

.. prizerun::

    git merge usb_backup/master

Remotes in the interwebs
------------------------

So far we've only used remotes on the filesystem of the laptop and desktop.

Remotes can also refer to storage on |--| remote |--| machines, using
communication protocols such as the "git" protocol, ssh, http or https.

For example, here is the remote list for the repository containing this
tutorial:

.. workrun::

    git remote -v

Check out bitbucket_ and github_ for free hosting of your repositories.  Both
services offer free hosting of data that anyone can read (public
repositories).  Bitbucket offers free hosting of private repositories, and
Github will host some private repositories for education users.

Other useful commands
=====================

These commands complete the typical daily toolkit of an experienced git user:

*  `reflog <http://www.kernel.org/pub/software/scm/git/docs/git-reflog.html>`__
*  `rebase <http://www.kernel.org/pub/software/scm/git/docs/git-rebase.html>`__

See :doc:`rebase_without_tears` for a tutorial on ``rebase``.

Git: are you ready?
===================

If you followed this tutorial, you now have a good knowledge of how git works.
This will make it much easier to understand why git commands do what they do,
and what to do when things go wrong.  You know all the main terms that the git
manual pages use, so git's own help will be more useful to you.  You will
likely lead a long life of deep personal fulfillment.

*************
Git resources
*************

Introductions and tutorials
===========================

As you've seen, this tutorial makes the bold assumption that you'll be able to
understand how git works by seeing how it is *built*. These documents take a
similar approach to varying levels of detail:

* This `visual git tutorial
  <http://www.ralfebert.de/blog/tools/visual_git_tutorial_1>`__ gives a nice
  visual idea of git at work.
* `Understanding Git Conceptually
  <http://www.sbf5.com/~cduan/technical/git>`__ gives another review of how
  the ideas behind git.
* For more detail, see the start of the excellent `Pro Git
  <http://progit.org/book>`__ online book, or similarly the early parts of the
  `Git community book <http://book.git-scm.com>`__. Pro Git's chapters are
  very short and well illustrated; the community book tends to have more
  detail and has nice screencasts at the end of some sections.
* The `Git parable
  <http://tom.preston-werner.com/2009/05/19/the-git-parable.html>`__ by Tom
  Preston-Werner.
*  :doc:`foundation`

You might also try:

* For windows users, `an Illustrated Guide to Git on Windows
  <http://nathanj.github.com/gitguide/tour.html>`__ is useful in that it
  contains also some information about handling SSH (necessary to interface
  with git hosted on remote servers when collaborating) as well as screenshots
  of the Windows interface.
* `Git ready <http://www.gitready.com>`__ A great website of posts on specific
  git-related topics, organized by difficulty.
* `QGit <http://sourceforge.net/projects/qgit/>`__: an excellent Git GUI Git
  ships by default with gitk and git-gui, a pair of Tk graphical clients to
  browse a repo and to operate in it. I personally have found `qgit
  <http://sourceforge.net/projects/qgit/>`__ to be nicer and easier to use. It
  is available on modern Linux distros, and since it is based on Qt, it should
  run on OSX and Windows.
* `Git Magic
  <http://www-cs-students.stanford.edu/~blynn/gitmagic/index.html>`_ : Another
  book-size guide that has useful snippets.
* The `learning center <http://learn.github.com>`__ at Github Guides on a
  number of topics, some specific to github hosting but much of it of general
  value.
* A `port <http://cworth.org/hgbook-git/tour>`__ of the Hg book's beginning
  The `Mercurial book <http://hgbook.red-bean.com>`__ has a reputation for
  clarity, so Carl Worth decided to `port
  <http://cworth.org/hgbook-git/tour>`__ its introductory chapter to Git. It's
  a nicely written intro, which is possible in good measure because of how
  similar the underlying models of Hg and Git ultimately are.
* `Intermediate tips
  <http://andyjeffries.co.uk/articles/25-tips-for-intermediate-git-users>`_: A
  set of tips that contains some very valuable nuggets, once you're past the
  basics.

For SVN users
=============

If you want a bit more background on why the model of version control
used by Git and Mercurial (known as distributed version control) is such
a good idea, I encourage you to read this very well written
`post <http://www.joelonsoftware.com/items/2010/03/17.html>`__ by Joel
Spolsky on the topic. After that post, Joel created a very nice
Mercurial tutorial, whose `first page <http://hginit.com/00.html>`__
applies equally well to git and is a very good 're-education' for anyone
coming from an SVN (or similar) background.

In practice, I think you are better off following Joel's advice and
understanding git on its own merits instead of trying to bang SVN
concepts into git shapes. But for the occasional translation from SVN to
Git of a specific idiom, the `Git - SVN Crash
Course <http://git-scm.org/course/svn.html>`__ can be handy.

A few useful tips for common tasks
==================================

Better shell support
--------------------

Adding git branch info to your bash prompt and tab completion for git
commands and branches is extremely useful. I suggest you at least copy:

* `git-completion.bash
  <https://github.com/git/git/blob/master/contrib/completion/git-completion.bash>`__
* `git-prompt.sh
  <https://github.com/git/git/blob/master/contrib/completion/git-prompt.sh>`__

You can then source both of these files in your ``~/.bashrc`` (Linux) or
``~/.bash_profile`` (OSX), and then set your prompt (I'll assume you
named them as the originals but starting with a ``.`` at the front of
the name):

::

    source \$HOME/.git-completion.bash
    source \$HOME/.git-prompt.sh
    PS1='[\u@\h \W\$(__git_ps1 " (%s)")]\\$ ' # adjust this to your prompt liking

See the comments in both of those files for lots of extra functionality
they offer.

.. rubric:: Footnotes

.. [#tree-command] I am using the ``tree`` command to give a nice text-graphic
   listing of directories.  If you want to try running ``tree`` yourself, you
   may need to find an implementation of ``tree`` for your operating system.
   On Debian or Ubuntu you can use ``sudo apt-get install tree``. For OSX you
   might want to use something like homebrew (``brew install tree``).  I have
   not tried installing ``tree`` on Windows.
.. [#list-figure] Would I get the same hash for the directory listing if I had
   had a different figure?  No |--| because the figure hash would be
   different, the directory listing would contain this different hash, and so
   the hash for the directory listing must be different.
.. [#commit-figure] Would the commit hash value change if the figure changed?
   Yes, because the change in the figure would cause a different hash for the
   figure; this would cause a different hash for the directory listing, and
   this hash in turn appears in the commit contents, causing a different hash
   for the commit.
.. [#no-parents] Why are the output of ``git log`` and ``git log --parents``
   the same in this case?  They are the same because this is the first commit,
   and the first commit has no parents.
.. [#git-object-dir] When git stores a file in the ``.git/objects`` directory,
   it makes a hash from the file, takes the first two digits of the hash to
   make a directory name, and then stores a file in this directory with a
   filename from the remaining hash digits.  For example, when adding a file
   with hash ``d92d079af6a7f276cc8d63dcf2549c03e7deb553`` git will create
   ``.git/objects/d9`` directory if it doesn't exist, and stores the file
   contents as ``.git/objects/d9/2d079af6a7f276cc8d63dcf2549c03e7deb553``.  It
   does this so that the number of files in any one directory stay in a
   reasonable range.  If git had to store hash filenames for every object in
   one flat directory, the directory would soon have a very large number of
   files.
.. [#tag-other-objects] You might have guessed by now that a tag can refer to
   any git object, not just a commit.  For example a tag can refer to a tree
   (directory listing) or blob (file) object, although in practice tags almost
   always refer to commits.
.. [#bare-detail] The reason we need a bare repository for our backup goes
   deeper than the fact we do not need a working tree.  We are soon going to
   do a ``push`` to this backup repository.  The ``push`` has the effect of
   resetting the position of a branch (usually ``master``) in the backup repo.
   Git is very reluctant to set a branch position in a remote repository with
   a working tree, because the new branch position will not not match the
   existing content of the working tree.  Git could either leave the remote
   working tree out of sync with the new branch position, or update the remote
   working tree by doing a checkout of the new branch position, but either
   thing would be very confusing for someone trying to use the working tree in
   that repository.  So, by default git will refuse to ``push`` a new branch
   position to a remote repository with a working tree, giving you a long
   explanation as to why it is refusing, and listing things you can do about
   it.  You can force git to go ahead and do the push, but it is much safer to
   use a bare repository.

.. include:: links_names.inc
.. include:: working/object_names.inc
