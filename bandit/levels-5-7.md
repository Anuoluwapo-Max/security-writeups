Bandit Level 4 → 7 (in progress) — Hidden Files & Filtering with find
Goal

Locate specific hidden or disguised files across multiple directories using file properties (size, permissions, readability) rather than filenames alone.

Problem

Several levels hid the target password inside directories full of decoy files — some with misleading names (-file00 through -file09), others nested inside 20 subdirectories (maybehere00–maybehere19) with no visible clue which one held the real file. Filenames also included spaces and leading dashes, which the shell misinterprets by default (leading dashes get read as command flags; spaces split one filename into multiple arguments).

Fix

Handling tricky filenames:

bash
cat "./--spaces in this filename--"
cat ./-file07

Wrapping the name in quotes preserves spaces as part of the filename. Prefixing with ./ tells the shell "this is a path," preventing it from reading a leading - as a flag.

Finding a file by properties, not name:

bash
find . -type f -size 1033c ! -perm /111
-type f — only regular files
-size 1033c — exact size in bytes
! -perm /111 — excludes anything executable by owner, group, or others

This let me isolate a single file out of 20 identical-looking directories in one command, instead of manually checking each one.

What I Learned
Bash treats leading dashes as flags unless explicitly told otherwise (./)
find can filter on multiple file properties simultaneously — powerful for narrowing down a search space fast
-perm permission bitmasks (e.g. 111 for execute across all three permission classes) are worth understanding at the bit level, not just memorizing
A single missing space (find. vs find .) is enough to break a whole command — attention to syntax detail matters as much as understanding the logic
