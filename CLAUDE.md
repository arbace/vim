# CLAUDE.md

@AGENTS.md

The rules in AGENTS.md (C style, tests, help file style) still apply to the
code.  This file adds what is specific to this fork and overrides AGENTS.md
where the two disagree.

## What this repository is

This is the fork https://github.com/arbace/vim.  Its only purpose is to keep
one feature alive on top of current Vim: the `\%f)` / `\%t)` regexp atoms,
which match forward to the delimiter closing the current nesting level
(`\%)` `\%]` `\%}` `\%>`, the `\%f`/`\%t` forms, and the `\_%` forms that
cross line breaks).  They are implemented in both engines, `src/regexp_bt.c`
and `src/regexp_nfa.c`, documented in `runtime/doc/pattern.txt` and tested in
`src/testdir/test_regexp_latin.vim` and `src/testdir/test_regexp_utf8.vim`.

The feature was proposed upstream as vim/vim #21244 and was declined.  It is
not going to be submitted again.

## Branches and remotes

- `regexp-delimiter-atoms` is the default branch, locally and on GitHub.
  All work happens here.
- `master` is a plain mirror of vim/vim's master, locally and on arbace/vim.
  It only ever fast-forwards to upstream.  Never commit to it and never
  merge `regexp-delimiter-atoms` into it.
- `origin` is arbace/vim, the only remote that is pushed to.
- `upstream` is vim/vim, only ever fetched from.  Never open pull requests,
  issues or comments against vim/vim.

## Following upstream

The only connection with vim/vim is following its commits and rebasing this
branch onto them:

    git fetch upstream
    git fetch . upstream/master:master      # fast-forward local master
    git rebase master                       # on regexp-delimiter-atoms

`git fetch . upstream/master:master` updates `master` without checking it
out and refuses anything but a fast-forward.  If it refuses, `master` has
diverged from upstream: stop and ask, do not force it.

When a conflict comes up, keep upstream's change and re-apply the feature
on top of it; upstream code is never changed just to make the feature fit.
Conflicts usually come from upstream adding code next to the feature's code
in `regexp_nfa.c` or `regexp_bt.c`.

After every rebase, before pushing:

1. Build from `src/` with `make` and check there are no new warnings.  The
   configure line used for this checkout is in `.claude/BUILD.md`.
2. Run the regexp tests from `src/testdir/`:

       make test_regexp_latin.res test_regexp_utf8.res \
            test_regex_char_classes.res test_search.res

   Every `Test_delimiter_atoms_*` test must pass.  Read `messages` after a
   fresh run, since it keeps output from earlier runs.
3. Push both branches:

       git push origin master
       git push --force-with-lease origin regexp-delimiter-atoms

   `master` is a normal push, since it only moves forward.  The branch needs
   `--force-with-lease` because a rebase rewrites it.

A failure that also happens on a clean `master` checkout (for
example `test_codestyle` complaining about `sign.c`) is upstream's and not
something to fix here.

## Commits

Keep the feature as one commit on top of upstream: fold fixes to the
feature into it with `git commit --amend` or a fixup, so a rebase only ever
has one commit to carry.  Changes that are not part of the feature, such as
this file, go in their own commit after it.

The AGENTS.md rules about `src/version.c` and the `patch 9.2.NNNN:` subject
are for patches sent to vim/vim and do not apply here.  Keep
`Signed-off-by:`.

Ask before every commit and every push.
