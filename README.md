## 🗄️ RAID Simulator (C)

A command-line simulation of **RAID 1, RAID 1E, RAID 4 and RAID 5**, written in C for a university assignment at the Department of Digital Systems, University of the Peloponnese (March 2026). Each "disk" is a plain text file of `0`/`1` characters, so you can open the files and see how the data is laid out.

### What it does

1. **Initialises N disks** as text files (`RAIDX_0.txt`, `RAIDX_1.txt`, ...) filled with `0`s, and checks that the input fits the array's capacity.
2. **Writes the input bit stream** across the disks using the chosen RAID layout (mirroring, striping, or striping with parity).
3. **Applies updates** from a file. Only the changed bit (and its parity, for RAID 4/5) is rewritten, not the whole array.
4. **Simulates a disk failure.** You pick a disk, it is deleted, and the program rebuilds it from the surviving disks.
5. **Verifies the result** by reassembling all the data and comparing it with a backup of the original input. It prints `100% similarity` if the recovery was lossless.

### Supported levels

| Level | Layout | Capacity | Recovery method |
|-------|--------|----------|-----------------|
| RAID 1 | Two-disk mirror | `SIZE` | Copy from the surviving mirror |
| RAID 1E | Blocks and their mirrors striped across N disks | `(SIZE × N) / 2` | Locate the mirrored block with `get_1e_block_offset` |
| RAID 4 | Striping with a dedicated parity disk | `SIZE × (N − 1)` | XOR of all surviving disks |
| RAID 5 | Striping with rotating parity | `SIZE × (N − 1)` | XOR of all surviving disks |

Parity is computed bit by bit with XOR. On an update the new parity is `old_parity ⊕ old_bit ⊕ new_bit`.

### Build and run

```bash
gcc -o ergasia1 ergasia1.c
./ergasia1 RAIDX SIZE BLOCKSIZE N inputFile.txt updates.txt backup.txt allData.txt
```

| Argument | Meaning |
|----------|---------|
| `RAIDX` | `RAID1`, `RAID1E`, `RAID4` or `RAID5` |
| `SIZE` | Size of each disk in bits (use a multiple of `BLOCKSIZE`) |
| `BLOCKSIZE` | Block (stripe unit) size in bits |
| `N` | Number of disks |
| `inputFile.txt` | Input bit string, e.g. `input1.txt` |
| `updates.txt` | Updates as `position value` lines, e.g. `update1.txt` |
| `backup.txt` | Output: copy of the (updated) original data |
| `allData.txt` | Output: data reassembled from the array after recovery |

**Example** (RAID 5, 4 disks, 12 bits per disk, 4-bit blocks):

```bash
./ergasia1 RAID5 12 4 4 input1.txt update1.txt backup.txt allData.txt
# Poios diskos parousiazei sfalma: 1
# 100% similarity
```

### Repository contents

- `ergasia1.c`: the simulator source
- `input*.txt` / `update*.txt`: sample inputs of increasing size (30 bits up to about 50,000 bits) with matching update files
- `Report.docx`: detailed write-up of each code section (in Greek)

### Concepts practised

Striping, mirroring, XOR parity, degraded-mode recovery, file I/O in C (`fseek`, `fgetc`, `fputc`), and address mapping from a logical block to a disk and offset.
