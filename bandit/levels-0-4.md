# Bandit Level 0 → 4

## Level 0: Getting Connected

**Goal:** Connect to the Bandit server via SSH.

**Command:**
```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

**What I learned:**
- Basic SSH syntax: `ssh user@host -p port`
- OverTheWire uses port 2220, not the default SSH port (22)
- Password for Level 0 is given openly (`bandit0`) to get started

---

## Level 1: A File Named `-`

**Goal:** Read a file literally named `-` in the home directory.

**The problem:**
Running `cat -` doesn't read the file — `-` is a special symbol most 
commands interpret as "read from standard input," so the terminal just 
hangs waiting for keyboard input instead.

**The fix:**
```bash
cat ./-
```

**What I learned:**
- The `./` prefix tells the shell "treat this as a path, not a flag" — 
  it points explicitly to the current directory
- Any filename starting with `-` risks being misread as a command option 
  unless you protect it this way

---

## Level 2: A Filename With Spaces

**Goal:** Read a file named `--spaces in this filename--`.

**The problem:**
Typing the filename unquoted (`cat ./--spaces in this filename--`) 
causes bash to treat each space as a separator between *different* 
arguments, instead of one filename. Even wrapping it in quotes alone 
(`cat "--spaces in this filename--"`) still fails, because the leading 
`--` gets misread as an option flag.

**Two working fixes:**
```bash
cat -- "--spaces in this filename--"
cat "./--spaces in this filename--"
```

**What I learned:**
- Quotes (`"..."`) tell bash "treat everything inside as ONE argument," 
  spaces included — but that alone doesn't stop a leading `--` from 
  being misread as a flag
- `--` is a separate, standalone argument meaning "stop parsing anything 
  after this as an option/flag" — that's why it needs a space before the 
  filename (they're two distinct arguments)
- `./` glued directly onto a filename (no space) is describing a single 
  path, so it doesn't need — and can't have — a space in the middle. 
  This also sidesteps the flag-parsing issue entirely, since `cat` now 
  sees a path, not something starting with `-`

---

## Level 3: A Hidden File Inside a Directory

**Goal:** Find a file inside the `inhere` directory — Bandit's password 
file isn't shown by default (`ls` alone hides files starting with a dot).

**Steps:**
```bash
cd inhere
ls -a
cat -- "...Hiding-From-You"
```

**What I learned:**
- `ls -a` reveals hidden files (anything starting with `.`)
- Same `--` trick from Level 2 applies here since the filename starts 
  with dots that could confuse the parser

---

## Level 4: Finding the One Readable File Among Many

**Goal:** Ten files (`-file00` through `-file09`) exist in the `inhere` 
directory. Only one is human-readable — the rest are binary/garbage data 
designed to mislead.

**The smart approach — using `file` and wildcards instead of checking 
each one manually:**
```bash
file ./-file*
```

This runs the `file` command (which identifies content type without 
opening it) against every filename matching the pattern `-file*` — the 
`*` is a wildcard meaning "match anything after this point."

**Result:** Only `-file07` came back as `ASCII text` — everything else 
was `data`, or in one case, an `OpenPGP Secret Key`.

**Reading it:**
```bash
cat ./-file07
```

**What I learned:**
- `file` inspects content type without printing everything — efficient 
  for scanning many files at once
- Wildcards (`*`) must be typed exactly matching the real filename 
  pattern — a stray space (`- f*` vs `-f*`) completely breaks the match
- `-f*` would've worked just as well as `-file*` here, since nothing 
  else in the directory started with `-f`

---

## Key Takeaway Across All Levels
Odd filenames (dashes, spaces, hidden dots) are a deliberate teaching 
tool — in real-world pentesting/sysadmin work, attackers and defenders 
alike sometimes name files this way to confuse tools or hide things. 
Learning `./`, `--`, quoting, and wildcards early makes handling these 
cases second nature.
