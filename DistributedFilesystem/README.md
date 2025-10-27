# PA4 Automated Grading Script

This repository provides an **automated grading script** for the Distributed File System project (**PA4**) in CSCI 4273/5273.  
The grader evaluates the correctness and robustness of your DFS/DFC implementation across multiple server configurations.

---

## 🧩 Overview

The grading script (`grade_pa4.py`) automatically:

1. Builds your code (`make clean && make`)
2. Starts up to **4 DFS servers**
3. Uploads (`PUT`) and downloads (`GET`) test files using your `dfc` client
4. Checks for:
   - File integrity (hash match)
   - Correct distribution of file pieces
   - Redundancy and fault tolerance
   - Proper handling of incomplete file reconstruction
   - Concurrent client support

Each phase corresponds to one **test case** with assigned points.

---

## ⚙️ Prerequisites

- Python ≥ 3.8  
- Ubuntu 20.04 / 22.04 environment  
- `make`, `gcc`, `openssl`, and networking permissions
- Your compiled executables must be named:
  - `dfs`
  - `dfc`
- Ensure `dfc.conf` and `Makefile` are in the same working directory

---

## 🗂 Directory Layout

During grading, the following directories will be created:

```
/home/student/PA4/
├── dfs1/
├── dfs2/
├── dfs3/
├── dfs4/
├── dfc.conf
├── sample_file/
│   ├── wine3.jpg
│   └── apple_ex.png
├── dfc1/
├── dfc2/
├── dfc3/
├── dfc4/
└── grade_pa4.py
```

The `sample_file/` directory holds reference files downloaded from the course test server.

---

## 🚀 Usage

Run the grader with **4 port numbers** for your DFS servers:

```bash
python3 grade_pa4.py <port_1> <port_2> <port_3> <port_4>

# Example
python3 grade_pa4.py 10001 10002 10003 10004
```

The script:
- Cleans and rebuilds your code (`make -s clean && make -s`)
- Kills any processes already bound to the given ports
- Starts servers under:
  ```bash
  ./dfs ./dfs1 10001 &
  ./dfs ./dfs2 10002 &
  ./dfs ./dfs3 10003 &
  ./dfs ./dfs4 10004 &
  ```
- Executes client commands and evaluates output

---

## 🧪 Tests and Scoring

| Test | Description | Points |
|------|--------------|--------|
| **Test 1: 4 Servers** | Uploads both sample files, verifies full reconstruction, correct piece distribution (4 pieces/server = 16 total), and `dfc list` output. | **50 pts** |
| **Test 2: 3 Servers** | Terminates **server 4**, ensures redundancy allows full `list` + `get` success and hash match. | **20 pts** |
| **Test 3: 2 Servers** | Terminates **server 2**, again verifies redundancy and hash correctness. | **20 pts** |
| **Test 4: 1 Server** | Terminates **server 3**, leaving one active server. `list` output should show `(incomplete)` entries and `get` should print “<filename> is incomplete”. | **10 pts** |
| **Test 5: Multiple DFCs** | Restarts all 4 servers, spawns **4 concurrent clients** (`dfc1–dfc4`), each fetching `wine3.jpg`. All 4 hashes must match the sample for credit. | **10 pts** |

**Total:**  110 points

---

## 🧾 Output Example

```
=== Test 1: 4 servers ===
[*] Uploading (PUT) ...
[*] Downloading (GET) ...
[*] Checking hashes (Test 1)...
    - wine3.jpg: <md5> vs <md5>
    - apple_ex.png: <md5> vs <md5>
[*] Checking server piece counts ...
    - dfs1: 4 files
    - dfs2: 4 files
    - dfs3: 4 files
    - dfs4: 4 files
---- ./dfc list output (Test 1) ----
apple_ex.png
wine3.jpg
---------------------------------
==== RESULT (Test 1) ====
Score: 50 / 50
...
==== TOTAL ====
Total Score: 110 / 110
```

---

## 🧹 Cleanup

All DFS server processes are safely terminated at the end of the run:

```
[*] Stopping servers ...
```

To manually stop lingering servers:

```bash
pkill -f dfs
```

---

## 🧠 Tips

- Make sure `dfc` reads `dfc.conf` from the **current working directory**.
- Ensure your DFS properly replicates file pieces and handles missing servers gracefully.
- Test your own client using:
  ```bash
  ./dfc put sample_file/wine3.jpg
  ./dfc get wine3.jpg
  ./dfc list
  ```
- Avoid hardcoding absolute paths—`getcwd()` is used dynamically.

---

