# Vim with delimiter atoms

**Vim, plus regexp atoms that match up to the bracket closing the current
nesting level.**

This is a fork of [Vim](https://github.com/vim/vim) that carries one
feature on top of current Vim: the `\%f)` / `\%t)` pattern atoms. A regular
expression can't count nesting, so `([^)]*)` stops at the first `)`. These
atoms match forward to the delimiter that *closes* the level the match is
on, counting the nested pairs on the way:

```vim
/(\%)          " in f(a(b)c)d, matches (a(b)c)   -- not just (a(b)
/{\%t}         " a { block up to, not including, its }
/{\_%}         " a { block with its }, however many lines it spans
:s/(\%)/(...)/ " in f(a(b)c)d, gives f(...)d
```

| Atom | Matches |
|---|---|
| `\%)` `\%]` `\%}` `\%>` | up to and including the `)` `]` `}` `>` that closes this level |
| `\%f)` `\%f]` `\%f}` `\%f>` | the same ("find", as the `f` command) |
| `\%t)` `\%t]` `\%t}` `\%t>` | up to just before it ("till", as the `t` command) |
| `\_%)`, `\_%f)`, `\_%t)`, ... | the same, continuing over line breaks |

The opening delimiter is not part of the atom; it is normally matched just
before it, as in `(\%)`. Without `\_` the search stops at the end of the
line, the way `\_.` and `\_[]` extend `.` and `[]`. `\%>` is the new atom
only when no digit, `.` or `'` follows, so the position atoms (`\%>23l`,
`\%>'m`, ...) keep their meaning. Both regexp engines implement the atoms,
and the full description is in `:help /\%)` (`runtime/doc/pattern.txt`).

## Relation to Vim

- The default branch, `regexp-delimiter-atoms`, is current Vim with the
  feature as a single commit on top. It is rebased onto upstream as Vim
  moves on, so expect it to be force-pushed.
- `master` mirrors [vim/vim](https://github.com/vim/vim) unchanged.
- The feature was proposed upstream as
  [vim/vim#21244](https://github.com/vim/vim/pull/21244) and declined, so it
  lives here. This is not an official Vim repository. Report bugs in Vim
  itself [upstream](https://github.com/vim/vim/issues), not here. Vim's own
  README is [README.md](../README.md).

## Building

Build like Vim:

```sh
cd src
./configure --with-features=normal   # or as you usually configure Vim
make
```

The feature's tests are in `src/testdir/test_regexp_latin.vim` and
`test_regexp_utf8.vim` (`Test_delimiter_atoms_*`):

```sh
cd src/testdir
make test_regexp_latin.res test_regexp_utf8.res
```
