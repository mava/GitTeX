GitTeX
======

How to add Git version control info to your LaTeX documents
-----------------------------------------------------------

### Preliminary setup

Create once and for all a `git tex` alias by running the following command from inside your `git` repository:

    git config alias.tex "log -1 --pretty=format:'\\newcommand*{\\githash}{\texttt{%h}}%n\\newcommand*{\\gitdate}{%ad}%n\\newcommand*{\\gitrefnames}{\\scriptsize\\texttt{%d}}%n'"

If you want to have the alias available everywhere on your local machine, use the `--global` flag:  

    git config --global alias.tex "log -1 --pretty=format:'\\newcommand*{\\githash}{\texttt{%h}}%n\\newcommand*{\\gitdate}{%ad}%n\\newcommand*{\\gitrefnames}{\\scriptsize\\texttt{%d}}%n'"

Alternatively, you could add the following lines to your `.git/config` or `$HOME/.gitconfig` file:

    [alias]
            tex = log -1 --pretty=format:'\\newcommand*{\\githash}{\texttt{%h}}%n\\newcommand*{\\gitdate}{%ad}%n\\newcommand*{\\gitrefnames}{\\scriptsize\\texttt{%d}}%n'

Then invoking `git tex` produces an output like this:

    \newcommand*{\githash}{\texttt{34f8465}}
    \newcommand*{\gitdate}{Fri Nov 2 13:58:33 2012 -0400}
    \newcommand*{\gitrefnames}{\scriptsize\texttt{ (HEAD -> master, github/master)}}

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

- `\githash` = abbreviated commit hash
- `\gitdate` = author date (format respects `--date=` option)
- `\gitrefnames` = ref names, like the `--decorate` option of `git log` (format respects `--decorate=` option)

otherwise:

- `\githash` = `info not available`
- `\gitdate` = `\today`
- `\gitrefnames` = empty string

(or whatever you specified in the `\providecommand`s above).

These commands could then be used as follows, for example:

    \date{\gitdate, revision \githash \gitrefnames}

If you want to use `\gitrefnames`, you need `\usepackage[utf8]{inputenc}`, because it will contain characters like `->`.

You need to invoke `(pdf)latex --shell-escape` or you need to enable shell escape globally, for example by running `tlmgr conf texmf shell_escape t`.

Oddly enough, if shell escape is only enabled partially (set to `p`) then adding `git` to `shell_escape_commands` does *not* work, and running `(pdf)latex` produces the following error message:

    fatal: ambiguous argument '>': unknown revision or path not in the working tree.
    Use '--' to separate paths from revisions, like this:
    'git <command> [<revision>...] -- [<file>...]'
