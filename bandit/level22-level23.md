# Bandit Level 22 → Level 23

## Task

A program is running automatically at regular intervals using `cron`, the time-based job scheduler.

The goal is to inspect `/etc/cron.d/`, identify the configuration related to `bandit23`, and determine what command is being executed.

The script used in this level is intentionally easy to read. If necessary, it can also be executed manually to observe the debug information it prints and better understand how it works.

## Commands Used

- `ls -la` — lists files and their permissions.
- `cat` — displays the contents of a file.
- `echo` — outputs text and adds a newline by default.
- `md5sum` — calculates the MD5 hash of input.

## Command Breakdown

First, I inspected the cron jobs:

```bash
ls -la /etc/cron.d/
```

I found:

```text
cronjob_bandit23
```

I then inspected its configuration:

```bash
cat /etc/cron.d/cronjob_bandit23
```

The cron job executes:

```text
/usr/bin/cronjob_bandit23.sh
```

as the user `bandit23` every minute.

Next, I inspected the script:

```bash
cat /usr/bin/cronjob_bandit23.sh
```

The important part was:

```bash
myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)
```

Because the script runs as `bandit23`, `whoami` returns:

```text
bandit23
```

Therefore, the script calculates the MD5 hash of:

```text
I am user bandit23
```

![Inspecting the cron job and script](screenshots/level22-level23_1.png)

To reproduce the same calculation, I used:

```bash
echo "I am user bandit23" | md5sum
```

This produced:

```text
8ca319486bfbbc3663ea0fbe81326349
```

The script uses this hash as the filename inside `/tmp`.

I then checked the generated file:

```bash
ls -la /tmp/8ca319486bfbbc3663ea0fbe81326349
```

and read it using:

```bash
cat /tmp/8ca319486bfbbc3663ea0fbe81326349
```

![Calculating the hash and reading the generated file](screenshots/level22-level23_2.png)

### Note about the newline

`echo` automatically adds a newline (`\n`) after the text.

Therefore:

```bash
echo "I am user bandit23" | md5sum
```

calculates the hash of the text **including a real newline character**.

Typing `\n` manually into an online MD5 generator is not necessarily the same thing, because the website may interpret `\` and `n` as two normal characters instead of an actual newline.

This is why the online generator produced a different hash.

![Difference when using an online MD5 generator](screenshots/level22-level23_3.png)

## Password

<details>
<summary>Click to reveal</summary>

`gKXDTAXnIz3OBxiPjRZ2uqutUlPZrBsw`

</details>
