# Level 16 → 17

## Task

The credentials for the next level can be retrieved by submitting the password of the current level to **a port on localhost in the range 31000 to 32000**.

First, find out which of these ports have a server listening on them. Then determine which of those servers speak SSL/TLS and which do not.

There is only **one server** that will provide the credentials for the next level. The others will simply send back whatever is sent to them.

> **Helpful note:** Getting `DONE`, `RENEGOTIATING`, or `KEYUPDATE`? Read the **CONNECTED COMMANDS** section in the `openssl s_client` manpage.

## Commands Used

- `nmap -sV -p 31000-32000 localhost` — scanned the specified port range and attempted to identify the services running on the open ports.
- `openssl s_client -connect localhost:31790 -quiet` — established a TLS connection to the correct service.
- `chmod 600 cheieprivata` — restricted access to the private SSH key so that only the owner could read and write it.
- `ssh -i cheieprivata -p 2220 bandit17@bandit.labs.overthewire.org` — connected to the next level using the private SSH key.

## Command Breakdown

### 1. Scanning the port range

```bash
nmap -sV -p 31000-32000 localhost
```

The scan found the following open ports:

```text
31046/tcp open  echo
31518/tcp open  ssl/echo
31691/tcp open  echo
31790/tcp open  ssl/unknown
31960/tcp open  echo
```

The `-sV` option performs service detection, while:

```text
-p 31000-32000
```

limits the scan to the port range specified by the challenge.

Most of the discovered services were simple echo services. Port `31790` was more interesting because Nmap detected SSL/TLS but could not identify the exact application behind it.

### 2. Connecting to the TLS service

I connected to port `31790` using:

```bash
openssl s_client -connect localhost:31790 -quiet
```

The `s_client` command creates a TLS connection to the specified host and port.

I used `-quiet` because the current password starts with the character `k`. In the interactive mode of `openssl s_client`, `k` can be interpreted as an OpenSSL command for a TLS Key Update instead of being sent to the server.

Using `-quiet` prevents this behavior and allows the password to be sent normally.

After submitting the current `bandit16` password, the server responded with:

```text
Correct!
-----BEGIN OPENSSH PRIVATE KEY-----
...
-----END OPENSSH PRIVATE KEY-----
```

Instead of a normal password, the credential for the next level was an SSH private key.

### 3. Switching to Kali Linux

Initially, I tried to save and use the private key on Windows.

However, the key was saved through a text file, and the formatting of the file caused problems when I tried to use it with OpenSSH. Differences in text formatting and line endings can affect private key files because their structure must remain exactly as expected.

Because the key was not being accepted correctly on Windows, I switched to Kali Linux, where I could save the key directly in its original format and manage its permissions more easily.

I saved the private key in a file named:

```text
cheieprivata
```

### 4. Setting the private key permissions

Before using the key with SSH, I changed its permissions:

```bash
chmod 600 cheieprivata
```

`600` gives the following permissions:

```text
owner: read + write
group: no permissions
others: no permissions
```

SSH private keys should only be accessible by their owner. Using Kali Linux made it straightforward to configure these permissions with `chmod`.

### 5. Connecting to Bandit17

Finally, I used the private key to authenticate as `bandit17`:

```bash
ssh -i cheieprivata -p 2220 bandit17@bandit.labs.overthewire.org
```

Where:

- `-i cheieprivata` specifies the private key used for authentication.
- `-p 2220` specifies the SSH port used by OverTheWire.
- `bandit17@bandit.labs.overthewire.org` specifies the remote user and server.

The connection was successful and I gained access to `bandit17`.

## Screenshots

### Port and service discovery

![Port scan](screenshots/level16-level17_1.png)

### TLS connection and private key retrieval

![TLS connection](screenshots/level16-level17_2.png)

### SSH connection to Bandit17

![SSH connection](screenshots/level16-level17_3.png)

## Password

<details>
<summary>Click to reveal</summary>

This level does not provide a normal password directly.

The credential obtained from the service is an **OpenSSH private key**, which is used to authenticate as `bandit17`.

</details>
