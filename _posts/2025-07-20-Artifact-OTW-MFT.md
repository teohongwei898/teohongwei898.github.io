---
title: "Artifact of the Week - MFT"
date: 2025-07-20 11:20:00 +0800
categories: [DFIR]
tags: [AOTW, DFIR, TH]
---

# Artifact of the Week: Mastering the MFT – NTFS Metadata Powerhouse

## What Is the MFT?

The **Master File Table (MFT)** is the central database of the NTFS file system. Every file and directory is tracked in it, and each MFT record includes attributes like:

- `$STANDARD_INFORMATION` ($SI)
- `$FILE_NAME` ($FN)
- `$DATA`
- `$SECURITY_DESCRIPTOR`

Each record is typically 1024 bytes, sometimes up to 4096 on advanced drives, and starts with a recognizable **magic number** signature.

---

## Why It Matters in DFIR

The MFT provides essential information in digital forensic investigations:

- Reconstruct **file timelines** (MACB: Modified, Accessed, Changed, Birth).
- Detect **time stomping** (compare $SI and $FN timestamps).
- Recover traces of **deleted files** (via $I30, $LogFile, $UsnJrnl).
- Track attacker behavior, malware staging, and data staging paths.
- Build a correlated, accurate **timeline of activity**.

---

## Supporting Artifacts: Enhancing MFT Analysis

### **$LogFile** – Transaction Journal

- Records low-level NTFS changes: creation, rename, delete.
- Retains changes only for a **short time**.
- Crucial for identifying file ops before MFT record reuse.

---

### `$I30` – Directory Indexing Entries

- Present in directory records.
- Tracks **current and deleted** filenames in a folder.
- Changes recorded in **B-Tree structure** (can leave slack data behind).
- Useful for identifying **deleted file names and sizes**.

---

### **$UsnJrnl** – Update Sequence Number Journal

- Located at: `$Extend\$UsnJrnl:$J`
- Logs file creation, deletion, modification (volume-wide).
- Doesn’t show **full paths**, but can be **correlated with MFT**.

_New Insight (Jan 6, 2025)_: You can recover original file locations from USN even after MFT reuse:  
[usnjrnl_rewind – CyberCX DFIR](https://github.com/CyberCX-DFIR/usnjrnl_rewind)

---

## How to Extract & Parse These Artifacts

You can extract all these NTFS artifacts with:

### Tools:

- [Eric Zimmerman's Tools](https://ericzimmerman.github.io/)
- FTK Imager (disk image or live acquisition)
- `KAPE` (for automated collection + parsing)
- `logfileparser` for `$LogFile`
