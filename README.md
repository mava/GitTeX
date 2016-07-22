GitTeX
======

How to add Git version control info to your LaTeX documents
-----------------------------------------------------------

### Preliminary setup

Create once and for all a `git tex` alias by running the following command from inside your `git` repository:

    git config alias.tex "log -1 --pretty=format:'\newcommand*{\gitdate}{%ad}%n\newcommand*{\githash}{\texttt{%h}}%n\newcommand*{\gitrefnames}{{\scriptsize\texttt{%d}}}%n'"

If you want to have the alias available everywhere on your local machine, use the `--global` flag:  

    git config --global alias.tex "log -1 --pretty=format:'\newcommand*{\gitdate}{%ad}%n\newcommand*{\githash}{\texttt{%h}}%n\newcommand*{\gitrefnames}{{\scriptsize\texttt{%d}}}%n'"

Alternatively, you could add the following lines to your `.git/config` or `$HOME/.gitconfig` file:

    [alias]
            tex = log -1 --pretty=format:'\newcommand*{\gitdate}{%ad}%n\newcommand*{\githash}{\texttt{%h}}%n\newcommand*{\gitrefnames}{{\scriptsize\texttt{%d}}}%n'

Then invoking `git tex` produces an output like this:

    \newcommand*{\gitdate}{Fri Jul 22 10:55:58 2016 -0400}
    \newcommand*{\githash}{\texttt{a0155de}}
    \newcommand*{\gitrefnames}{{\scriptsize\texttt{ (HEAD -> master, github/master)}}}

(You can use options like `git tex --date=short` and `git tex --decorate=full`, too.)


### LaTeX files

Add the following lines to the preamble of your LaTeX file:

    % see github.com/mava/GitTeX
    \immediate\write18{git tex > \jobname.gtx}
    \InputIfFileExists{\jobname.gtx}{}{}
    \providecommand*{\gitdate}{\today}
    \providecommand*{\githash}{info not available}
    \providecommand*{\gitrefnames}{}

If `git tex` ran successfully then:

- `\gitdate` = author date (format respects `--date=` option)
- `\githash` = abbreviated commit hash
- `\gitrefnames` = ref names, like the `--decorate` option of `git log` (format respects `--decorate=` option)

otherwise:

- `\gitdate` = `\today`
- `\githash` = `info not available`
- `\gitrefnames` = empty string

(or whatever you specified in the `\providecommand`s above).

These commands could then be used as follows, for example:

    \date{\gitdate, revision \githash\gitrefnames}

You need to invoke `(pdf)latex --shell-escape` or to enable shell escape globally, for example by running `tlmgr conf texmf shell_escape t`.

Oddly enough, if shell escape is only enabled partially (set to `p`) then adding `git` to `shell_escape_commands` does *not* work, and running `(pdf)latex` produces the following error message:

    fatal: ambiguous argument '>': unknown revision or path not in the working tree.
    Use '--' to separate paths from revisions, like this:
    'git <command> [<revision>...] -- [<file>...]'
