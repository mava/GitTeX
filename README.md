GitTeX
======

How to add Git version control info to your LaTeX documents
-----------------------------------------------------------

1. Create once and for all a `git tex` alias by running the following command from inside your `git` repository:

        git config alias.tex "log -1 --pretty=format:'\\newcommand*{\\githash}{%h}%n\\newcommand*{\\gitdate}{%ad}%n\\newcommand*{\\gitrefnames}{%d}%n'"

    If you want to have the alias available everywhere on your local machine, use the `--global` flag:  

        git config --global alias.tex "log -1 --pretty=format:'\\newcommand*{\\githash}{%h}%n\\newcommand*{\\gitdate}{%ad}%n\\newcommand*{\\gitrefnames}{%d}%n'"

    Alternatively, you could edit your `.git/config` or `$HOME/.gitconfig` file respectively and add the following lines:

        [alias]
                tex = log -1 --pretty=format:'\\newcommand*{\\githash}{%h}%n\\newcommand*{\\gitdate}{%ad}%n\\newcommand*{\\gitrefnames}{%d}%n'

    Then invoking `git tex` produces an output like this:

        \newcommand*{\githash}{92b54de}
        \newcommand*{\gitdate}{Wed Feb 19 21:57:31 2011 -0500}
        \newcommand*{\gitrefnames}{ (HEAD, origin/master, master)}

    (You can use options like `git tex --date=short`, too.)

2. Add the following lines to the preamble of your LaTeX file:

        \InputIfFileExists{\jobname.gtx}{}{}
        \immediate\write18{git tex > \jobname.gtx}
        \providecommand*{\gitdate}{\today}
        \providecommand*{\githash}{info not available}
        \providecommand*{\gitrefnames}{}

    If `git tex` ran successfully then:

    - `\githash` = abbreviated commit hash
    - `\gitdate` = author date (format respects `--date=` option)
    - `\gitrefnames` = ref names, like the `--decorate` option of `git log`

    otherwise:

    - `\githash` = `info not available`
    - `\gitdate` = `\today`
    - `\gitrefnames` = empty string

    (or whatever you specified in the `\providecommand`s above).

    For this to work, you need to invoke `(pdf)latex --shell-escape` or you need to enable shell escape globally, for example by running `tlmgr conf texmf shell_escape t`.

    Oddly enough, if shell escape is only enabled partially (set to `p`) then adding `git` to `shell_escape_commands` does *not* work, and running `(pdf)latex` produces the following error message:

        fatal: ambiguous argument '>': unknown revision or path not in the working tree.
        Use '--' to separate paths from revisions
