# FTP Client–Server File Transfer Lab

> A hands-on Cisco Packet Tracer lab: upload a file to an FTP server, rename it, download it, and remove the server copy.

## Lab overview

I used the command-line FTP client on a Packet Tracer PC to exchange a text file with the lab FTP server. The activity covered the full file-transfer workflow—from locating a local file to verifying the server-side changes and cleaning up afterward.

| Item | Lab value |
|---|---|
| Client | PC-A in Cisco Packet Tracer |
| FTP server | `209.165.200.226` (`ftp.pka` was also listed as a name) |
| Local starting file | `C:\sampleFile.txt` |
| Uploaded/downloaded file | `sampleFile_FTP.txt` after the server-side rename |
| Transfer result | 26 bytes transferred successfully |

The lab credentials were supplied by the course and are intentionally not included here.

## What I did

### 1. Located the file on the PC

From **PC-A → Desktop → Command Prompt**, I listed the contents of the PC’s `C:\` directory and confirmed that `sampleFile.txt` was present:

```text
C:\> dir
```

### 2. Connected to the FTP server

I connected to the server by its IP address and signed in with the course-provided credentials:

```text
C:\> ftp 209.165.200.226
```

The server confirmed the login and indicated that passive mode was on. At the `ftp>` prompt, I listed the server directory:

```text
ftp> dir
```

### 3. Uploaded the local file

I sent the file from the PC to the server with `put`:

```text
ftp> put sampleFile.txt
```

The server reported that the transfer completed and that **26 bytes** were copied. I used `dir` again to verify that the file appeared in the server listing.

### 4. Renamed the server copy

At the FTP prompt, I renamed the uploaded file:

```text
ftp> rename sampleFile.txt sampleFile_FTP.txt
```

I checked the server listing with `dir` to confirm the new filename.

### 5. Downloaded the renamed file

I retrieved the renamed file from the server with `get`:

```text
ftp> get sampleFile_FTP.txt
```

The transfer completed successfully (**26 bytes**). I then exited the FTP client and checked the PC directory to confirm the downloaded copy was present:

```text
ftp> quit
C:\> dir
```

### 6. Deleted the server copy and disconnected

I connected to the FTP server again, signed in, and removed the renamed file from the server:

```text
C:\> ftp 209.165.200.226
ftp> delete sampleFile_FTP.txt
```

The server confirmed that `sampleFile_FTP.txt` was deleted successfully. I exited the FTP client:

```text
ftp> quit
```

The prompt returned to `C:\>`, confirming that the FTP session had closed. The downloaded copy on the PC was separate from the server copy I deleted.

## Command reference

| Command | What it does in this lab |
|---|---|
| `dir` at `C:\>` | Lists files on the PC |
| `ftp 209.165.200.226` | Connects to the FTP server |
| `dir` at `ftp>` | Lists files on the FTP server |
| `put sampleFile.txt` | Uploads a file from the PC to the server |
| `rename old-name new-name` | Renames a file on the server |
| `get sampleFile_FTP.txt` | Downloads a file from the server to the PC |
| `delete sampleFile_FTP.txt` | Deletes a file from the server |
| `quit` | Closes the FTP session |

**Prompt matters:** `C:\>` is the PC command prompt; `ftp>` is the FTP client prompt. FTP-specific commands such as `put`, `get`, `rename`, and `delete` are entered at `ftp>`.

## Key takeaways

- `put` moves a file **from client to server**; `get` moves a file **from server to client**.
- A successful transfer message and a follow-up `dir` provide useful verification.
- Renaming or deleting the server copy does not automatically rename or delete a separate local copy.
- FTP is a legacy protocol and does not encrypt credentials or file contents by default. This Packet Tracer exercise uses a controlled lab server; for real sensitive transfers, use a secure alternative such as SFTP or HTTPS.

## Result

**Completed:** I uploaded `sampleFile.txt`, renamed the server copy to `sampleFile_FTP.txt`, downloaded it to the PC, verified the successful transfer, deleted the renamed file from the FTP server, and exited the FTP client.
