# Level 02 → 03

## Task

The password for the next level is stored in a file called `--spaces in this filename--` located in the home directory.

## Commands Used

- `ls -la` — lists all files, including hidden files, in a detailed format.
- `cat "./--spaces in this filename--"` — displays the file contents. `./` refers to the current directory, while the quotes keep the filename with spaces as a single argument.

### Alternative

- `cat -- "--spaces in this filename--"` — also works. `--` tells `cat` to stop interpreting following arguments as options.

## Screenshot

![Bandit Level 02 → 03](screenshots/level02-level03.png)

## Password

<details>
<summary>Click to reveal</summary>

`7ZZ2LFrykP2zEyvBl4m3clcL7tGYJPME`

</details>
