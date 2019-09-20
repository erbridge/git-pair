# Git Pair

Easily attribute commits when pair programming!

## Installation

1. Clone / copy this repository locally. Replace `<GIT_PAIR_PATH>` in the
   subsequent commands with the repository's absolute path (run `pwd` in the
   repository).
2. Set Git up to use the [hooks provided by this project](/githooks) using
   [`core.hooksPath`](https://git-scm.com/docs/githooks) (don't worry, they're
   set up to still run local repository hooks):

   ```bash
   git config --global core.hooksPath '<GIT_PAIR_PATH>/githooks'
   ```

3. Create aliases:

   ```bash
   git config --global alias.pair '!<GIT_PAIR_PATH>/bin/git-pair'
   git config --global alias.solo '!<GIT_PAIR_PATH>/bin/git-solo'
   ```

## Usage

When you start pairing, run

```bash
git pair $ALIAS
```

where `$ALIAS` is a memorable alias for the person you're pairing with (I
recommend using their GitHub username). You can provide as many aliases at once
as you like in the case you're pairing with more than one person.

If this is the first time pairing with someone, you'll be prompted to enter
their name and email address. Use the values they want the commits attributed
to. If you're using GitHub, you can also use their
[GitHub-provided `no-reply` email address](https://help.github.com/en/articles/creating-a-commit-with-multiple-authors#required-co-author-information)
and still get proper attribution.

Then write code and commit as normal. Your commit messages will have trailers
like these appended to them:

```
Co-authored-by: name <name@example.com>
Co-authored-by: another-name <another-name@example.com>"
```

Make sure you leave a blank line between your commit message and any trailers.

When you're done pairing, run

```bash
git solo
```

to clear your pairs.

**Note: pairs are per-repository. If you're working across more than one
repository, you'll need to set up (and clear) your pairs in each one.**

## How it works

`git pair` creates a file, `$GIT_DIR/current-co-authors`, which stores the
details of your co-authors.

Git looks into `core.hooksPath` for hooks. The hooks included in this repository
all look for, and run, any executable hooks local to the Git repository you're
working in, so any existing local hooks should continue to function as normal.
The `prepare-commit-msg` hook looks in `$GIT_DIR/current-co-authors`, and adds
co-author trailers to the commit message as the last step (so it will run after
any local hooks).

`git-solo` then removes `$GIT_DIR/current-co-authors`, clearing any configured
co-authors from future commits.

The first time you run `git-pair` for someone, it prompts you for their name and
email address, which it stores in `$HOME/.gitpairs`. If an alias already exists
there, `git-pair` will just use that data instead of prompting.

## Possible improvements

- Make pairs editable. Currently, you have to manually edit `$HOME/.gitpairs`.
- Make amending a commit authored by someone else add the current Git user as a
  co-author.
- Create an installation script to simplify set up.
