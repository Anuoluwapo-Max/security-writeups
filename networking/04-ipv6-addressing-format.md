# IPv6 Addressing Format: Hextets and Hexadecimal

## Goal
Understand why IPv6 addresses are written in hexadecimal rather than binary or decimal, and how the 128-bit address is structured into readable groups.

## Problem
Needed to move past "IPv6 addresses use hex" as a memorized fact and actually derive why hex was chosen, and how the address's structure (8 groups of 4 hex digits) is built from that reasoning.

## Fix
- IPv6 addresses are 128 bits — far too long to write or read reliably in raw binary.
- Hexadecimal was chosen specifically because of a clean mathematical fit: 4 binary bits produce exactly 2^4 = 16 possible values, and hexadecimal has exactly 16 symbols (0–9, then a–f representing 10–15). This gives a lossless, one-to-one mapping between every 4-bit binary pattern and a single hex digit.
- Building the address structure from this:
  - 1 hex digit = 4 bits
  - 4 hex digits = 16 bits = one "hextet"
  - 8 hextets = 128 bits = one full IPv6 address
- Each hextet ranges from `0000` to `ffff` in hex — verified against the binary range (`0000000000000000` to `1111111111111111`, i.e., 0 to 2^16−1 = 65535, which is `ffff` in hex).
- Confirmed the full picture with an example address: `2001:0db8:0000:0000:0000:0000:0000:0001` — 8 hextets separated by colons.

## What I Learned
- Hex isn't an arbitrary convention — it's the natural, lossless way to compress binary because both are powers of 2 (16 = 2^4), unlike decimal, which doesn't divide binary evenly.
- Deriving the address structure (8 hextets, 128 bits) from first principles, rather than memorizing it, made the number "340 undecillion possible addresses" (2^128) make sense as a direct consequence of the format rather than an isolated statistic.
- This is a foundational literacy skill for reading any IPv6 address correctly going forward, including recognizing shorthand/compressed forms in later material.
