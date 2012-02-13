% FASD(1) fasd user manual
% Wei Dai <x@wei23.net>
% Feb 12, 2012

# NAME

fasd - quick access to files and directories

# SYNOPSIS

fasd [options] [query ...]

[f|a|s|d|z] [options] [query ...]

# OPTIONS

    -s        show list of files with their ranks
    -l        list paths only
    -i        interactive mode
    -e <cmd>  set command to execute on the result file
    -b <name> only use <name> backend
    -B <name> add addition backend <name>
    -a        match files and directories
    -d        match directories only
    -f        match files only
    -r        match by rank only
    -t        match by recent access only
    -h        show a brief help message

# DESCRIPTION

Fasd keeps track of files and directories you access in your shell and gives you
quick access to them. You can use fasd to reference files or directories by just
a few key identifying characters. You can use fasd to boost your command line
productivity by defining your own aliases to launch programs on files or
directories. Fasd, by default, provides some basic aliases, including a shell
function "z" that resembles the functionality of "z" and "autojump."

The name "fasd" comes from the default suggested aliases `f`(files),
`a`(files/directories), `s`(show/search/select), `d`(directories).

Fasd ranks files and directories by "frecency," that is, by both "frequency"
and "recency." The term "frecency" was first coined by Mozilla and used in
Firefox.

Fasd uses Bayesian Inference and Bayesian Ranking to rank files and directories
for a set of given matching patterns. "Frecency" is used as the prior
probability distribution, and a simple algorithm is used to calculate the
likelihood of the given set of patterns.

# EXAMPLES

    z bundle
    f -e vim nginx conf
    vi `f nginx conf`
    cp update.html `d www`
    open `sf pdf`

# SHELL INITIALIZATION

To get fasd working in a shell, some initialization code must be run. Put
lines below in your POSIX compatible shell rc.

    eval "$(fasd --init auto)"

These will setup a command hook that executes on every command and advanced tab
completion for zsh and bash.

If you want more control over what gets into your shell environment, you can
pass customized set of arguments to `fasd --init`.

    zsh-hook             # define _fasd_preexec and add it to zsh preexec array
    zsh-ccomp            # zsh command mode completion definitions
    zsh-ccomp-install    # setup command mode completion for zsh
    zsh-wcomp            # zsh word mode completion definitions
    zsh-wcomp-install    # setup word mode completioin for zsh
    bash-hook            # add hook code to bash $PROMPT_COMMAND
    bash-ccomp           # bash command mode completion definitions
    bash-ccomp-install   # setup command mode completion for bash
    bash-wcomp           # bash word mode completion definitions (experimental)
    bash-wcomp-install   # setup word mode completion for bash (experimental)
    posix-alias          # define alias that applies to all posix shells
    posix-hook           # setup $PS1 hook for shells that's posix compatible

Example for a minimal zsh setup (no tab completion):

    eval "$(fasd --init posix-alias zsh-hook)"

Optionally, if you can also source `fasd` if you want `fasd` to be a shell
function instead of an executable.

# COMPATIBILITY

Fasd's basic functionalities are POSIX compliant, meaning that you should be
able to use fasd in all POSIX compliant shells. Your shell need to support
command substitution in $PS1 in order for fasd to automatically track your
commands and files. This feature is not specified by the POSIX standard, but
it's nonetheless present in many POSIX compliant shells.

# TAB COMPLETION

Fasd offers two completion modes, command mode completion and word mode
completion.

Command mode completion is just like completion for any other commands. It is
triggered when you hit tab on a fasd command or its aliases. Under this mode
your queries can be separated by a space. Tip: if you find that the completion
result overwrites your queries, type an extra space before you hit tab.

Word mode completion can be triggered on *any* command. This can be a powerful
feature if you make good use of it. Word completion is triggered by any command
line argument that starts with ","(a comma). Example:

    $ vim ,rc,lo<Tab>
    $ vim /etc/rc.local

If you use zsh, word completion is enabled by default. There are also three
zle widgets: "fasd-complete", "fasd-complete-f", "fasd-complete-d". You can
bind them to keybindings you like:

    bindkey '^X^A' fasd-complete    # C-x C-a to do fasd-complete (fils and directories)
    bindkey '^X^F' fasd-complete-f  # C-x C-f to do fasd-complete-f (only files)
    bindkey '^X^D' fasd-complete-d  # C-x C-d to do fasd-complete-d (only directories)

If you use bash, you can turn on this experimental feature by executing
`eval "$(fasd --init bash-wcomp bash-wcomp-install)"` after sourcing `fasd`
and after any bash completion setup. This will alter your existing completion
setup, so you might get a *broken* completion system.

# BACKENDS

Fasd can take advantage of different sources of recent / frequent files. Most
desktop environments (like Gtk) and some editors (like Vim) keep a list of
accessed files. Fasd can use them as additional backends if the data can be
converted into fasd's native format. As of now, fasd supports Gtk's
`recently-used.xbel` and Vim's `viminfo` backends. You can define your own
backend by declaring a function by that name in your `.fasdrc`. You set default
backend with `_FASD_BACKENDS` variable in our `.fasdrc`.

# TWEAKS

Upon every execution, fasd will source "/etc/fasdrc" and "$HOME/.fasdrc" if
they are present. Below are some variables you can set:

    $_FASD_DATA
    Path to the f data file, default "$HOME/.fasd".

    $_FASD_BLACKLIST
    List of blacklisted strings. Commands matching them will not be processed.
    Default is "--help".

    $_FASD_SHIFT
    List of all commands that needs to be shifted, defaults to "sudo busybox".

    $_FASD_IGNORE
    List of all commands that will be ignored, defaults to "fasd cd ls echo".

    $_FASD_TRACK_PWD
    f defaults to track your "$PWD". Set this to 0 to disable this behavior.

    $_FASD_AWK
    Which awk to use. fasd can detect and use a compatible awk.

    $_FASD_SINK
    File to log all STDERR to, defaults to "/dev/null".

    $_FASD_MAX
    Max total score / weight, defaults to 2000.

    $_FASD_SHELL
    Which shell to execute. Some shells will run faster than others. fasd
    is faster with ksh variants.

    $_FASD_BACKENDS
    Default backends.

# DEBUGGING

Fasd is hosted on GitHub: https://github.com/clvv/fasd

If fasd does not work as expected, please file a bug report on GitHub describing
the unexpected behavior along with your OS version, shell version, awk version,
sed version, and a log file.

You can set `_FASD_SINK` in your `.fasdrc` to obtain a log.

    _FASD_SINK="$HOME/.fasd.log"

# COPYING

Fasd is originally written based on code from z(https://github.com/rupa/z) by
rupa deadwyler under the WTFPL license. Most if not all of the code has been
rewritten. Fasd is licensed under the "MIT/X11" license.
