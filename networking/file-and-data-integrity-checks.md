# Packet Tracer: File and Data Integrity Checks

**Day 57 of my 100-day cybersecurity challenge**
Platform: Cisco Networking Academy (Endpoint Security) | Tools: Packet Tracer, Kali Linux (VMware), `md5sum`, `openssl`

## Objective

After a simulated cyber attack, I had to find out whether the backup files could still be trusted. I did this in three parts:

1. Recover the client files from a backup FTP server.
2. Use MD5 hashes to check which files had changed since they were archived.
3. Use HMAC to verify the integrity of a critical file, `income.txt`.

## Setup

The lab says to use the CSE-LABVM for the hashing steps. I used my own Kali Linux VM instead. `md5sum` and `openssl` come preinstalled, and the commands behave the same. Everything else (browsing, FTP, email) happened inside Packet Tracer.

## Part 1: Recover the files

1. On **Laptop BR-1** (Mike's PC), I opened a web browser and visited `http://branch.corp`, then `http://hq.corp`.
2. The HQ hash page lists the hash of each file at the time it was archived. This is the known-good reference. I copied it into a file in Kali (`hashes.txt`) so I could compare against it later.
3. From the command prompt, I connected to the HQ FTP server and logged in as `mike`:

```
ftp hq.corp
ftp> dir
ftp> get NEclients.txt
```

I downloaded all six client files this way (NEclients, NWclients, Nclients, SEclients, SWclients, Sclients). The byte counts matched the server listing: 582, 583, 698, 597, 649 and 780.

Two things I ran into:
- `mget *.txt` is not supported in Packet Tracer ("Invalid or non supported command"), so each file needs its own `get`.
- FTP file names are case-sensitive. `Nwclients.txt` failed with "No such file", while `NWclients.txt` worked.

## Part 2: Verify file integrity with hashing

For each file, I opened it in Packet Tracer's Text Editor, copied the contents, and hashed them in Kali:

```
echo -n 'file-contents-pasted-here' | md5sum
grep NEclients hashes.txt
```

`echo -n` prints the text without a trailing newline, because a newline would change the hash.

### Results

| File | Result |
|------|--------|
| NEclients.txt | Match |
| NWclients.txt | Match |
| Nclients.txt | Match |
| SEclients.txt | **Mismatch** |
| SWclients.txt | Match |
| Sclients.txt | Match |

For SEclients.txt:

```
Computed: f8a01d43eef5271e680dacaf1d1642d1
Listed:   520eb6c72cc2574decb731bc6230e223
```

The two hashes are completely different. That means SEclients.txt was changed after it was archived.

### Escalation

Following the lab, I emailed Sally (`sally@branch.corp`) to report that the file server had been tampered with. I then logged in to the HQ FTP server as `sally` from HQ-Laptop-1 and downloaded the tampered file so she could analyse it later.

## Part 3: Verify file integrity with HMAC

HMAC is a hash that also needs a secret key. On HQ-Laptop-2, I copied the contents of `income.txt` into Kali, saved it as `income.txt`, and ran:

```
openssl dgst -sha256 -hmac cisco123 income.txt
```

My first attempt produced a different hash from the one on the HQ page:

```
18ac6a160edc53859eea975530b128a65355e38e7771d8a9c56a8df794c3faa9
```

The pasted file was missing the newline at the end of the original. When I piped the file through `(cat income.txt; echo) | openssl dgst -sha256 -hmac cisco123`, the result matched. After fixing the file in nano, the normal command gave the expected value:

```
b138706cb55787d2a01934b224edad32203f87470ff6c7ffb9bd126786d1830d
```

This matches the hash listed on the HQ page, so `income.txt` was unchanged.

## Lab questions

| Question | Answer |
|----------|--------|
| Are the two hash values for NEclients.txt the same? | Yes |
| Which file was tampered with? | SEclients.txt |
| What is the computed HMAC for income.txt? | `b138706cb55787d2a01934b224edad32203f87470ff6c7ffb9bd126786d1830d` |
| How is HMAC more secure than general hashing? | Producing a valid HMAC requires both the original message and a secret key. |
| Does the HMAC for income.txt match the original hash? | Yes |

## What I learned

- A hash is a fingerprint. Change one character in the file and the hash changes completely, so comparing hashes is a quick way to check integrity.
- A hash can show that a file differs from a reference. It can't show who changed it or why. That is why the tampered file went to Sally for analysis.
- A plain hash can be recomputed by anyone who alters the file. HMAC needs a secret key, so an attacker can't forge a matching hash without it.
- Integrity checks are exact. My first HMAC didn't match because of a single missing newline, a reminder to be careful with copy-paste when hashing.
- A backup is only useful if you can verify it hasn't been changed, and hashing is how you check that.
