# Level 13 → 14

## Task

The password for the next level is stored in `/etc/bandit_pass/bandit14` and can only be read by user `bandit14`.

Instead of receiving the password directly, this level provides a **private SSH key** that can be used to log in as `bandit14`.

## Commands Used

- `ls -la` — lists all files in the current directory, including hidden files and permissions.
- `cat HINT` — displays the hint provided for the level.
- `cat sshkey.private` — displays the private SSH key.
- `ssh -i <key> user@host -p <port>` — connects through SSH using a private key instead of a password.
- `exit` — closes the current SSH session.
- `scp` — securely copies files between a local machine and a remote system through SSH.

## Command Breakdown

First, I listed the files in the home directory:

```bash
ls -la
```

Two important files were present:

```text
HINT
sshkey.private
```

I inspected the hint:

```bash
cat HINT
```

The hint explained that the current OverTheWire configuration blocks SSH connections from one Bandit level to another through `localhost`.

I then inspected the provided key:

```bash
cat sshkey.private
```

The file contained:

```text
-----BEGIN OPENSSH PRIVATE KEY-----
...
-----END OPENSSH PRIVATE KEY-----
```

This identified it as an **OpenSSH private key**.

Normally, a private key can be supplied to SSH with the `-i` option:

```bash
ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220
```

where:

```text
-i sshkey.private
→ use this private key as the identity file

bandit14
→ remote username

bandit.labs.overthewire.org
→ remote SSH server

-p 2220
→ use SSH port 2220
```

I first tried this command while still connected as `bandit13`.

The server returned:

```text
You are trying to log into this SSH server from localhost.
Connecting from/to localhost is blocked to conserve resources.
Please log out and log in again, directly from your client machine.
```

Therefore, I exited the `bandit13` session:

```bash
exit
```

Back on my local Windows machine, I downloaded the private key using `scp`:

```powershell
scp -P 2220 bandit13@bandit.labs.overthewire.org:sshkey.private .
```

Here:

```text
scp
→ securely copies a file through SSH

-P 2220
→ specifies the SSH port for SCP

bandit13@bandit.labs.overthewire.org:sshkey.private
→ remote source file

.
→ save the file in the current local directory
```

After entering the password for `bandit13`, the key was downloaded successfully:

```text
sshkey.private    100%
```

Finally, from my local machine, I connected to `bandit14` using the downloaded private key:

```powershell
ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220
```

The authentication succeeded and the shell changed to:

```text
bandit14@bandit:~$
```

This confirmed successful access to the next level.

## Key Concept

This level introduced **SSH key-based authentication**.

Instead of authenticating with:

```text
username + password
```

SSH can authenticate using:

```text
username + private key
```

The private key remains on the client machine and is supplied to SSH using:

```bash
ssh -i private_key user@host
```

It also introduced `scp`, which uses SSH to securely transfer files between local and remote systems.

## Screenshots

### Step 1 — Discovering the SSH Private Key

![Bandit Level 13 → 14 - Part 1](screenshots/level13-level14_1.png)

### Step 2 — Localhost SSH Restriction

![Bandit Level 13 → 14 - Part 2](screenshots/level13-level14_2.png)

### Step 3 — Downloading the Key with SCP and Connecting with SSH

![Bandit Level 13 → 14 - Part 3](screenshots/level13-level14_3.png)

### Step 4 — Successful Login as Bandit14

![Bandit Level 13 → 14 - Part 4](screenshots/level13-level14_4.png)

## Result

Successfully authenticated as:

```text
bandit14
```

using the provided SSH private key.
