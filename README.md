Resolving Zsh Corrupt History File Errors
=========================================

Overview
--------

This repository documents a reliable and repeatable method to resolve the following Zsh error:

`zsh: corrupt history file ~/.zsh_history`

The issue commonly occurs on Linux distributions using Zsh, particularly Kali Linux and Arch-based systems. It is frequently observed after unexpected system shutdowns, terminal crashes, or virtual machine suspensions.

The purpose of this repository is to provide a single, consistent reference for resolving the issue without repeatedly searching for solutions.

* * * * *

Root Cause
----------

Zsh maintains command history by writing to the `.zsh_history` file.\
This file is updated asynchronously. If the shell session terminates unexpectedly, non-text or partially written data may be stored in the history file.

Zsh enforces a strict history file format. The presence of invalid bytes causes the file to be flagged as corrupted, preventing it from being loaded.

This behavior is documented and is not indicative of user misconfiguration.

* * * * *

Standard Recovery Procedure
---------------------------

### Step 1: Create a backup

Before making changes, back up the existing history file.

```zsh
cp ~/.zsh_history ~/.zsh_history.bak
```

* * * * *

### Step 2: Rebuild the history file

Extract readable command entries and remove corrupted data.

```zsh
strings ~/.zsh_history > ~/.zsh_history.cleaned
mv ~/.zsh_history.cleaned ~/.zsh_history
```

This operation preserves valid command history while discarding invalid characters.

* * * * *

### Step 3: Enforce correct permissions

Zsh requires strict permissions on its history file.

```zsh
chmod 600 ~/.zsh_history
```

* * * * *

### Step 4: Reload the shell

```zsh
exec zsh
```

At this point, Zsh should load without reporting a corrupted history file.

* * * * *

Alternative: Full Reset
-----------------------

If the history file is severely corrupted and recovery is not required, a clean reset can be performed.

```zsh
rm ~/.zsh_history
touch ~/.zsh_history
chmod 600 ~/.zsh_history
exec zsh
```

This will remove all previous history and initialize a fresh file.

* * * * *

Preventive Configuration
------------------------

To reduce the likelihood of future history corruption, add the following options to `~/.zshrc`:

```zsh
setopt INC_APPEND_HISTORY
setopt SHARE_HISTORY
setopt HIST_REDUCE_BLANKS
```

Reload the configuration:

```zsh
source ~/.zshrc
```

### Explanation

-   **INC_APPEND_HISTORY**\
    Writes each command to the history file immediately instead of waiting for shell exit.

-   **SHARE_HISTORY**\
    Synchronizes command history across multiple shell sessions safely.

-   **HIST_REDUCE_BLANKS**\
    Removes unnecessary whitespace and malformed entries.

These options are recommended in official Zsh documentation and community-maintained guides.

* * * * *

Environment Validation
----------------------

The steps described in this repository have been tested on:

-   Kali Linux

-   Arch Linux

-   Ubuntu (Zsh as default shell)

-   VMware and VirtualBox virtual machines

* * * * *

References
----------

-   Zsh Official Documentation -- History Options\
    https://zsh.sourceforge.io/Doc/Release/Options.html#History

-   Arch Linux Wiki -- Zsh\
    <https://wiki.archlinux.org/title/Zsh>

-   Unix StackExchange -- Zsh corrupt history file discussion\
    https://unix.stackexchange.com/questions/273199/zsh-corrupt-history-file

* * * * *

Purpose of This Repository
--------------------------

This issue tends to recur periodically.\
Maintaining this repository eliminates the need to repeatedly diagnose or search for a solution and provides a consistent, verifiable fix that can be reused or shared.
