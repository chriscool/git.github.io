---
layout: default
title: SoC 2015 Ideas
---

This is the idea page for Summer of Code 2015 for Git and libgit2.

## About applying for SoC with the Git project

It is required that students who want to apply to the Git
project for the Summer of Code 2015 complete a tiny, code-related
"microproject" as part of their application.  Please refer to our
[guidelines and suggestions for microprojects](http://git.github.io/SoC-2015-Microprojects.html)
for more information. Completing a microproject is not only an important
way for us to get experience with applicants, but it will also help
applicants become familiar with Git's development and submission
process.

## Summer of code main project ideas

**Students**: Please consider these ideas as starting points for
generating proposals.  We are also more than happy to receive
proposals for other ideas related to Git or libgit2.

### Tighten configuration and hook execution based on the file ownership.

In addition to what we discussed, I think files named by include.path
should be honored without checking the ownership.  Cf. <http://article.gmane.org/gmane.comp.version-control.git/243628>

### "git apply --fix-mta-corruption".

Takes eol attributes into account to correct lossage/addition of CR at
the end of line, with updates to "am".  Cf. <http://article.gmane.org/gmane.comp.version-control.git/257445>

### git bisect improvements

#### "git bisect --first-parent".

When your project is strictly "new features are merged into trunk,
never the other way around", it is handy to be able to first find
a merge on the trunk that merged a topic to point fingers at when
a bug appears, instead of having to drill down to the individual
commit on the faulty side branch.

#### "git bisect fixed/unfixed"

"git bisect" is initially meant to find regressions (i.e. the new code
is bad, and the old one used to work). The opposite may happen too:
look for the commit which fixed a bug. It is already possible using
"git bisect", but the user has to type "good" to mean "the bug is
there" and "bad" to mean "the bug is fixed", which isn't convenient.

It would be nice to allow the user to explicitely say "git bisect
fixed" and "git bisect unfixed" instead. It is actually much harder
than defining "fixed"/"unfixed" as aliases for "bad"/"good".

A patch for this feature was sent to the mailing list in this thread a
few years ago by Dscho:
<http://thread.gmane.org/gmane.comp.version-control.git/86063>

Unfortunately there were some problems with the patch and no one tried
to fix them. So the same problem/suggestion is often reported on the
mailing list, for example:

 - <http://thread.gmane.org/gmane.comp.version-control.git/165141>
 - <http://thread.gmane.org/gmane.comp.version-control.git/170113>
 - <http://thread.gmane.org/gmane.comp.version-control.git/182398>

A patch series to let "old/new" be used instead of "good/bad" was
started here:

<http://thread.gmane.org/gmane.comp.version-control.git/199758/focus=199869>

There is still work to be done to complete it.

Note than another feature with the name "git bisect fix" was suggested
to do something else:

<http://thread.gmane.org/gmane.comp.version-control.git/169026>

Since there have already been discussions and patches, the remaining
work is probably less than a GSoC, so this project should be grouped
with another one (like git bisect --first-parent) to make a full GSoC
proposal.

### Unifying `git branch -l`, `git tag -l`, and `git for-each-ref`

These three commands are all about selecting a subset of a repository's
refs, and then printing the result. However, the implementations are not
shared. Some commands know selection options the others do not
(e.g., `--contains`, `--merged`), and others know formatting options the
others do not (e.g., `for-each-ref`'s `--format`).

There have been experimental patches to unify the selection process, and
some discussion on unifying the formatting. Based on this previous work,
factor out a common library which can cleanly handle both selection and
formatting, and use it consistently in the three commands.

 - Language: C
 - Difficulty: medium
 - Possible mentors: Jeff King


### "git config --unset" improvement

"git config", when removing the last variable in a section, leaves an
empty section header behind. Anybody who wants to improve this needs
to consider ramifications of leaving or removing comments.
Cf. <http://article.gmane.org/gmane.comp.version-control.git/219524>

### "git fetch --deepen"?

Cf. <http://article.gmane.org/gmane.comp.version-control.git/213180> <http://article.gmane.org/gmane.comp.version-control.git/212950>

### Introduce threading to checkout codepath, possibly helping Windows folks.

Cf. <http://article.gmane.org/gmane.comp.version-control.git/235874>

### Be nicer to the user on tracked/untracked merge conflicts

When merging a commit which has tracked files with the same name as
local untracked files, Git refuses to proceed. It could be nice to:

 - Accept the situation without conflict when the tracked file has the
   exact same content as the local untracked file (which would become
   tracked). No data is lost, nothing can be committed accidentally.

 - Possibly, for fast-forward merges, if a local files belongs to the
   index but not to the last commit, attempt a merge between the
   upstream version and the local one (resulting in the same content
   as if the file had just been committed, but without introducing an
   extra commit).

Recent versions SVN do something similar: on update, it considers
added but not committed files like normal tracked files, and attempts
a merge of the upstream version with the local one (which always
succeeds when the files have identical content). Attempting a merge
for non-fast forward cases would probably not make sense: it would mix
changes coming from the merge with other changes that do not come from
a commit.

One difficulty with this project is to identify uncontroversial cases
(where Git should merge without complaining) and all the possible
corner-cases.

### Convert scripts to builtins

Many components of Git are still in the form of shell and Perl scripts.
While this is an excellent choice as long as the functionality is
improved, it causes problems in production code – in particular on
multiple platforms, e.g. Windows (think: POSIX-to-Windows path
conversion issues).

The idea of this project is to dive into the Git source code and
convert, say, `git-add--interactive.perl` and/or `git stash` into
proper C code, making it a so-called "built-in".
