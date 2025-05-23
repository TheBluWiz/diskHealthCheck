  # disk-burnin [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
## Description
A portable, no‑nonsense disk stress & integrity tester for **macOS** and **Linux**.

Disk burn‑in tests are essential for verifying the health and reliability of new, refurbished, or suspicious storage media before putting them into production. `disk-burnin.sh` aggressively fills a target disk with randomized data, then verifies the integrity of every written file—exposing hardware problems and data corruption that quick benchmarks or SMART checks often miss.

---

## Features

- **Cross‑platform:** Works on macOS and most Linux distros.  
- **Aggressive testing:** Fills up most of the free space and pushes the drive with parallel writes.  
- **Data verification:** Confirms data integrity by hashing and checking all test files.  
- **Live progress display:** Shows real‑time progress and throughput stats.  
- **Safety‑first:** Refuses to run on system root `/` unless forced.  
- **Minimal dependencies:** Relies only on standard Unix utilities.  
- **Automated cleanup:** Removes test files when done (unless `-k` is specified).  

---

## Quick Start

```sh
# Download and make executable
curl -O https://raw.githubusercontent.com/TheBluWiz/diskHealthCheck/main/disk-burnin
chmod +x disk-burnin.sh

# Basic usage (will prompt for a target directory)
./disk-burnin.sh
```

---

## Usage

```sh
./disk-burnin.sh [options] [/path/to/target/dir]
```

### Options

| Option            | Description                                                     | Default          |
|-------------------|-----------------------------------------------------------------|------------------|
| `-p <percent>`    | Percentage of free space to use                                 | `90`             |
| `-b <size>`       | Block size for file I/O (e.g., `64M`, `1G`)                     | `64M`            |
| `-r <MiB>`        | Reference file size in MiB                                      | `1024`           |
| `-w <num>`        | Number of parallel worker jobs                                  | CPU cores − 1    |
| `-c <num>`        | Number of files per directory                                   | `10`             |
| `-k`              | **Keep** test data (don’t auto‑delete after the test)           | Off              |
| `-f`              | **Force** (allow running on `/`)                                | Off              |
| `-h`              | Show help and usage                                             |                  |

> **Warning:** All test data is created inside the specified directory.  
> The script will **not** proceed if you specify `/` unless you use `-f` (force).  
> By default, all test data is deleted at the end unless `-k` is specified.

---

## What Does It Do?

1. Checks (if possible) the disk’s SMART health.  
2. Creates a large random reference file.  
3. Writes many copies of the reference file in parallel across the disk, consuming most of the available free space.  
4. Verifies every file’s integrity by hashing and comparing to the reference.  
5. Shows a clear summary: **PASS** (if all files verify) or **FAIL** (with count).  
6. Cleans up all test files by default.  

---

## Example 1 – Default Settings

```sh
./disk-burnin.sh /Volumes/Test
```

<details>
<summary>Sample output</summary>

```
=== SMART health check ===
SMART status unavailable.

[12:34:56] Target dir  : /Volumes/Test
[12:34:56] OS / cores  : Darwin / 16 (workers=15)
[12:34:56] Free (MiB)  : 28541
[12:34:56] Plan        : 20 × 1 GiB (~20 GiB)
[12:34:56] Building 1 GiB reference …
[12:34:59] Reference ready.
[12:34:59] Writing with 15 parallel jobs …
....................
[12:35:20] Verifying files …
....................
==== SUMMARY ====
Verdict        : PASS
Data written   : 20 GiB
Elapsed time   : 19.92 s
Avg throughput : 1028.11 MiB/s
```
</details>

---

## Example 2 – Custom Parameters

Burn in with 60 % of free space, 4 GiB block size, 4 workers, and keep the data:

```sh
./disk-burnin.sh -p 60 -b 4G -w 4 -k /mnt/mydisk
```

<details>
<summary>Sample output</summary>

```
=== SMART health check ===
...
==== SUMMARY ====
Verdict        : PASS
Data written   : 110 GiB
Elapsed time   : 3 m 18 s
Avg throughput : 575.02 MiB/s
```
</details>

Test files remain in `/mnt/mydisk` for manual inspection.

---

## FAQ / Troubleshooting

* **Can I run this on my root disk?**  
  Not unless you use the `-f` (force) flag, and it’s not recommended. Point the script at a data or test drive instead.

* **The script reported a FAIL—now what?**  
  At least one file did not verify correctly. Potential culprits include flaky hardware, a faulty cable, filesystem corruption, or unstable RAM. Re‑run the test, try a different port/cable, or test with another drive.

* **Test data wasn’t deleted!**  
  If you used `-k` or the script exited abnormally, the test files remain. Remove them manually or rerun without `-k`.

---
  ## Going Forward
  ### Potential Features
  - Include File System testing using **fschk**
  - Have the script autodetect file system type and run **fschk** to find and correct errors.
    - Detect OS version
    - Determine filesystem
    - Run **fschk** that matches file system 
  - Have this script added to **Homebrew**
  - Create script that also functions for Windows
---
  ## Questions
  If you would like to contact me or view other projects I'm working on, you can explore my repositories at [TheBluWiz](https://github.com/TheBluWiz), or email me at thebluwiz@icloud.com.

## License

MIT License
