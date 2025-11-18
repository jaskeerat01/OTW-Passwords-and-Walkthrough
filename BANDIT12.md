# Bandit Level 12 → 13 Walkthrough

## Level 11 Password
```
JVNBBFSmZwKKOP0XbFXOoW8chDz5yVRv
```

## Objective
Find the password for the next level stored in the file **data.txt**, which is a **hexdump of a file that has been repeatedly compressed**. The file has been compressed multiple times using different compression algorithms.

## Step-by-Step Solution

### Step 1: Connect to the Server
```bash
ssh bandit12@bandit.labs.overthewire.org -p 2220
```
Enter the password: `JVNBBFSmZwKKOP0XbFXOoW8chDz5yVRv`

### Step 2: Explore the File
```bash
ls -lh
```
You'll see `data.txt` in the directory.

```bash
file data.txt
```
**Output:**
```
data.txt: ASCII text
```

```bash
head data.txt
```
**Output:**
```
00000000: 1f8b 0808 dfcd eb66 0203 6461 7461 322e  .......f..data2.
00000010: 6269 6e00 013f 02c0 fd42 5a68 3931 4159  bin..?...BZh91AY
00000020: 2653 5982 c194 8a00 0019 ffff dbfb adfb  &SY.............
```

This is a **hexdump** - not the actual file, but a hexadecimal representation of it!

### Step 3: Create a Working Directory

**Important:** You don't have write permissions in the home directory, so create a temporary directory.

```bash
mktemp -d
```
**Output:**
```
/tmp/tmp.aBc123XyZ
```

This creates a random temporary directory. Copy the path shown in your output.

```bash
cd /tmp/tmp.aBc123XyZ
```

### Step 4: Copy the Data File
```bash
cp ~/data.txt .
```

Verify it's copied:
```bash
ls -lh
```

### Step 5: Reverse the Hexdump

The file is a hexdump, so we need to convert it back to binary format using `xxd -r` (reverse).

```bash
xxd -r data.txt > file
```

**What this does:**
- `xxd -r` - Reverse a hexdump back to binary
- `data.txt` - Input hexdump file
- `> file` - Output to a file called "file"

### Step 6: Check File Type
```bash
file file
```
**Output:**
```
file: gzip compressed data, was "data2.bin", last modified: ...
```

It's a **gzip** file! But it has the wrong extension.

### Step 7: Start Decompressing - Round 1 (gzip)

Rename to proper extension:
```bash
mv file file.gz
```

Decompress:
```bash
gzip -d file.gz
```

**Output:** Creates a file called `file` (without .gz extension)

### Step 8: Check File Type Again
```bash
file file
```
**Output:**
```
file: bzip2 compressed data, block size = 900k
```

Now it's **bzip2** compressed!

### Step 9: Decompress - Round 2 (bzip2)

Rename with proper extension:
```bash
mv file file.bz2
```

Decompress:
```bash
bzip2 -d file.bz2
```

**Output:** Creates `file` again

### Step 10: Check File Type Again
```bash
file file
```
**Output:**
```
file: gzip compressed data, was "data4.bin", last modified: ...
```

It's **gzip** again!

### Step 11: Decompress - Round 3 (gzip)

Rename and decompress:
```bash
mv file file.gz
gzip -d file.gz
```

### Step 12: Check File Type
```bash
file file
```
**Output:**
```
file: POSIX tar archive (GNU)
```

Now it's a **tar archive**!

### Step 13: Extract - Round 4 (tar)

```bash
tar xvf file
```

**Output:**
```
data5.bin
```

A new file appears!

### Step 14: Continue the Pattern

Check the new file:
```bash
file data5.bin
```
**Output:**
```
data5.bin: POSIX tar archive (GNU)
```

Another tar file!

```bash
tar xvf data5.bin
```
**Output:**
```
data6.bin
```

### Step 15: Keep Going...

```bash
file data6.bin
```
**Output:**
```
data6.bin: bzip2 compressed data, block size = 900k
```

```bash
bzip2 -d data6.bin
```
**Output:**
```
bzip2: Can't guess original name for data6.bin -- using data6.bin.out
```

### Step 16: Continue Decompression

```bash
file data6.bin.out
```
**Output:**
```
data6.bin.out: POSIX tar archive (GNU)
```

```bash
tar xvf data6.bin.out
```
**Output:**
```
data8.bin
```

### Step 17: Almost There...

```bash
file data8.bin
```
**Output:**
```
data8.bin: gzip compressed data, was "data9.bin", last modified: ...
```

```bash
mv data8.bin data8.gz
gzip -d data8.gz
```

### Step 18: Final Check

```bash
file data8
```
**Output:**
```
data8: ASCII text
```

Finally! It's a text file!

```bash
cat data8
```
**Output:**
```
The password is FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn
```

The password is: **FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn**

---

## Complete Solution Summary

Here's the full sequence of compressions (in reverse order):

1. **Hexdump** → `xxd -r` → Binary file
2. **gzip** → `gzip -d` → Decompressed
3. **bzip2** → `bzip2 -d` → Decompressed
4. **gzip** → `gzip -d` → Decompressed
5. **tar** → `tar xvf` → Extracted data5.bin
6. **tar** → `tar xvf` → Extracted data6.bin
7. **bzip2** → `bzip2 -d` → Decompressed
8. **tar** → `tar xvf` → Extracted data8.bin
9. **gzip** → `gzip -d` → Final file with password

**Total layers:** 9 layers of compression/archiving!

---

## Understanding the Commands

### 1. Creating Temporary Directories

#### Using `mktemp -d`
```bash
mktemp -d
```
**What it does:**
- Creates a temporary directory with a random name
- Ensures the directory name is unique
- More secure than manually creating directories

**Output example:**
```
/tmp/tmp.Xy8K3mN2Lp
```

#### Alternative (Manual)
```bash
mkdir /tmp/mywork
cd /tmp/mywork
```

**Why use /tmp?**
- World-writable directory
- Designed for temporary files
- Automatically cleaned on reboot
- Everyone has write access

### 2. Hexdump Operations

#### What is a Hexdump?

A **hexdump** is a hexadecimal representation of binary data.

**Example:**
```
Original file: "Hello"
Hexdump:
00000000: 4865 6c6c 6f0a                           Hello.
```

- Left: Offset (position in file)
- Middle: Hexadecimal bytes
- Right: ASCII representation

#### Reversing a Hexdump with `xxd`

```bash
xxd -r data.txt > outputfile
```

**Options:**
- `-r` - Reverse operation (hex → binary)
- Without `-r`: Creates a hexdump from binary

**Creating a hexdump:**
```bash
xxd file > file.hex
```

**Reversing it back:**
```bash
xxd -r file.hex > file.original
```

### 3. File Type Identification

#### The `file` Command

```bash
file filename
```

**What it does:**
- Examines file contents (not just extension)
- Uses "magic numbers" to identify file types
- Shows compression type, archive type, etc.

**Common outputs:**
```bash
file: gzip compressed data
file: bzip2 compressed data
file: POSIX tar archive
file: ASCII text
file: data (generic binary)
```

**Why it's important:**
- Extensions can be wrong or missing
- You need the correct tool to decompress
- `file` tells you the actual format

### 4. Compression Tools

#### gzip / gunzip

**Compress:**
```bash
gzip file              # Creates file.gz, removes original
gzip -k file           # Keep original file
```

**Decompress:**
```bash
gzip -d file.gz        # Decompress, removes .gz
gunzip file.gz         # Same as gzip -d
```

**Common options:**
- `-d` - Decompress
- `-k` - Keep original file
- `-r` - Recursive (directories)
- `-v` - Verbose output
- `-1` to `-9` - Compression level (1=fast, 9=best)

**File extension:** `.gz`

**Recognizing gzip:**
```bash
file file.gz
# Output: gzip compressed data
```

#### bzip2 / bunzip2

**Compress:**
```bash
bzip2 file             # Creates file.bz2, removes original
bzip2 -k file          # Keep original
```

**Decompress:**
```bash
bzip2 -d file.bz2      # Decompress, removes .bz2
bunzip2 file.bz2       # Same as bzip2 -d
```

**Common options:**
- `-d` - Decompress
- `-k` - Keep original file
- `-v` - Verbose
- `-1` to `-9` - Compression level

**File extension:** `.bz2`

**Recognizing bzip2:**
```bash
file file.bz2
# Output: bzip2 compressed data, block size = 900k
```

**Note:** bzip2 typically achieves better compression than gzip but is slower.

#### tar (Tape Archive)

**Extract:**
```bash
tar xvf archive.tar
```

**Create:**
```bash
tar cvf archive.tar file1 file2 dir/
```

**Common options:**
- `x` - Extract files
- `c` - Create archive
- `v` - Verbose (show files being processed)
- `f` - File (specify archive filename)
- `z` - gzip compression (creates .tar.gz)
- `j` - bzip2 compression (creates .tar.bz2)
- `t` - List contents without extracting

**File extension:** `.tar`, `.tar.gz`, `.tar.bz2`, `.tgz`

**Examples:**
```bash
# Extract
tar xvf archive.tar

# List contents
tar tvf archive.tar

# Create archive with gzip
tar czvf archive.tar.gz directory/

# Extract .tar.gz
tar xzvf archive.tar.gz

# Extract .tar.bz2
tar xjvf archive.tar.bz2
```

**Recognizing tar:**
```bash
file archive.tar
# Output: POSIX tar archive (GNU)
```

### 5. Moving and Renaming Files

#### The `mv` Command

```bash
mv oldname newname     # Rename file
mv file directory/     # Move file to directory
mv file1 file2 dir/    # Move multiple files
```

**Why rename files?**
- Compression tools expect proper extensions
- `gzip` looks for `.gz`
- `bzip2` looks for `.bz2`
- Makes commands simpler

**Examples:**
```bash
# Add extension for gzip
mv file file.gz

# Add extension for bzip2
mv file file.bz2

# Add extension for tar
mv file file.tar
```

---

## Compression Comparison

| Tool | Extension | Command to Decompress | Compression Ratio | Speed |
|------|-----------|----------------------|-------------------|-------|
| **gzip** | `.gz` | `gzip -d` or `gunzip` | Good | Fast |
| **bzip2** | `.bz2` | `bzip2 -d` or `bunzip2` | Better | Slower |
| **xz** | `.xz` | `xz -d` or `unxz` | Best | Slowest |
| **tar** | `.tar` | `tar xvf` | None (archiving only) | Fast |

**Combined formats:**
- `.tar.gz` or `.tgz` - Tar + gzip
- `.tar.bz2` or `.tbz2` - Tar + bzip2
- `.tar.xz` - Tar + xz

---

## The Decompression Loop Strategy

When dealing with multiple compression layers:

### Step-by-Step Process

1. **Check file type**
   ```bash
   file filename
   ```

2. **Rename if needed**
   ```bash
   mv filename filename.gz    # For gzip
   mv filename filename.bz2   # For bzip2
   ```

3. **Decompress/Extract**
   ```bash
   gzip -d filename.gz        # For gzip
   bzip2 -d filename.bz2      # For bzip2
   tar xvf filename.tar       # For tar
   ```

4. **Repeat until you get ASCII text**
   ```bash
   file newfilename
   ```

### Automation with a Script (Optional)

```bash
#!/bin/bash
while true; do
    TYPE=$(file -b "$1")
    echo "File type: $TYPE"
    
    if [[ "$TYPE" == *"gzip"* ]]; then
        mv "$1" "$1.gz"
        gzip -d "$1.gz"
        echo "Decompressed gzip"
    elif [[ "$TYPE" == *"bzip2"* ]]; then
        mv "$1" "$1.bz2"
        bzip2 -d "$1.bz2"
        echo "Decompressed bzip2"
    elif [[ "$TYPE" == *"tar"* ]]; then
        tar xvf "$1"
        echo "Extracted tar"
        break
    elif [[ "$TYPE" == *"ASCII"* ]]; then
        echo "Found text file!"
        cat "$1"
        break
    else
        echo "Unknown type: $TYPE"
        break
    fi
done
```

---

## Common Mistakes to Avoid

### 1. Forgetting to Create a Working Directory
```bash
# WRONG - No write permission in home directory
xxd -r data.txt > file
# Error: Permission denied

# CORRECT - Work in /tmp
mktemp -d
cd /tmp/tmp.XXXXXX
cp ~/data.txt .
xxd -r data.txt > file
```

### 2. Wrong File Extensions
```bash
# WRONG - gzip expects .gz extension
gzip -d file
# Error: unknown suffix

# CORRECT - Rename first
mv file file.gz
gzip -d file.gz
```

### 3. Not Checking File Type
```bash
# WRONG - Guessing compression type
gzip -d file  # Might not be gzip!

# CORRECT - Check first
file file
# Then use appropriate tool
```

### 4. Using xxd Without Reverse Flag
```bash
# WRONG - Creates hexdump instead of reversing
xxd data.txt > file

# CORRECT - Use -r to reverse
xxd -r data.txt > file
```

### 5. Forgetting to Track New Files
```bash
# After tar extraction, check what was created
ls -lh
# Look for new files like data5.bin, data6.bin, etc.
```

---

## Understanding Magic Numbers

How does `file` identify file types? It uses **magic numbers** - specific byte sequences at the start of files.

### Common Magic Numbers

| File Type | Magic Bytes | Hex |
|-----------|-------------|-----|
| **gzip** | `1f 8b` | 0x1F 0x8B |
| **bzip2** | `BZ` | 0x42 0x5A |
| **tar** | `ustar` | (at offset 257) |
| **ZIP** | `PK` | 0x50 0x4B |
| **PNG** | `‰PNG` | 0x89 0x50 0x4E 0x47 |
| **PDF** | `%PDF` | 0x25 0x50 0x44 0x46 |

**View magic numbers:**
```bash
xxd file | head -n 1
```

**Example for gzip:**
```
00000000: 1f8b 0808 dfcd eb66 0203 6461 7461 322e
          ^^^^ 
          gzip magic number
```

---

## Real-World Applications

This challenge teaches skills used in:

- **Malware analysis** - Unpacking compressed/obfuscated malware
- **Forensics** - Extracting data from damaged or hidden archives
- **Data recovery** - Dealing with corrupted or mislabeled files
- **CTF competitions** - Multi-layer file challenges
- **System administration** - Managing backups and compressed logs
- **Reverse engineering** - Unpacking software distributions

---

## Practice Exercises

### Exercise 1: Create Multi-Layer Compression

```bash
# Create a test file
echo "Secret message" > original.txt

# Compress multiple times
gzip original.txt                    # Creates original.txt.gz
mv original.txt.gz temp1
bzip2 temp1                         # Creates temp1.bz2
tar czf temp2.tar.gz temp1.bz2      # Creates tar.gz archive

# Now try to decompress it!
```

### Exercise 2: Identify Without Extensions

```bash
# Create files without proper extensions
echo "Test" | gzip > mystery1
echo "Test" | bzip2 > mystery2

# Use file to identify
file mystery1
file mystery2

# Decompress them
```

### Exercise 3: Work with Hexdumps

```bash
# Create a hexdump
echo "Hello World" > test.txt
xxd test.txt > test.hex

# View it
cat test.hex

# Reverse it
xxd -r test.hex > test2.txt
cat test2.txt
```

---

## Quick Command Reference

### Essential Commands for This Level

```bash
# Create working directory
mktemp -d
cd /tmp/tmp.XXXXXX

# Copy data file
cp ~/data.txt .

# Reverse hexdump
xxd -r data.txt > file

# Check file type (use repeatedly!)
file filename

# Decompress gzip
mv file file.gz
gzip -d file.gz

# Decompress bzip2
mv file file.bz2
bzip2 -d file.bz2

# Extract tar
tar xvf file.tar

# View final result
cat finalfile
```

### The Complete Workflow

```bash
# 1. Setup
mktemp -d                              # Create temp directory
cd /tmp/tmp.XXXXXX                     # Go to temp directory
cp ~/data.txt .                        # Copy data file

# 2. Convert hexdump
xxd -r data.txt > file                 # Reverse hexdump

# 3. Loop until done
file file                              # Check type
# Based on output, use:
gzip -d file.gz        # OR
bzip2 -d file.bz2      # OR
tar xvf file.tar       # THEN repeat

# 4. Get password
cat final_text_file
```

---

## Why This Level Matters

This challenge teaches:

- **File format identification** - Using `file` command effectively
- **Compression tools** - gzip, bzip2, tar usage
- **Working with hexdumps** - Understanding `xxd`
- **Iterative problem solving** - Repeating steps until complete
- **Linux file operations** - mv, cp, working in /tmp
- **Patience and persistence** - 9 layers requires methodical work!

---

## Pro Tips

1. **Always use `file` first** - Don't guess the compression type
2. **Keep organized** - Track which files you've processed
3. **Use tab completion** - Press Tab to autocomplete filenames
4. **Check `ls` often** - See what new files appear after extraction
5. **Rename properly** - Compression tools expect correct extensions
6. **Work in /tmp** - You don't have write permissions elsewhere
7. **Use `mktemp -d`** - Safer than manually creating directories
8. **Be patient** - This level has many layers on purpose
9. **Document your steps** - Helps if you need to start over
10. **The last file is ASCII text** - Keep going until `file` says "ASCII text"

---

## Exit and Move to Next Level

```bash
exit
```

Now use the password you found to connect to Level 13!

---

## Quick Reference Card

```bash
# Connect
ssh bandit12@bandit.labs.overthewire.org -p 2220
Password: JVNBBFSmZwKKOP0XbFXOoW8chDz5yVRv

# Setup
mktemp -d
cd /tmp/tmp.XXXXXX
cp ~/data.txt .

# Convert and decompress
xxd -r data.txt > file
file file                  # Check type
# Then use appropriate tool:
gzip -d file.gz           # For gzip
bzip2 -d file.bz2         # For bzip2
tar xvf file.tar          # For tar
# Repeat until you get ASCII text

# Final password
FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn
```

## Summary

**Level 12 → 13** is about persistence:
1. Create temporary working directory (`mktemp -d`)
2. Copy and reverse the hexdump (`xxd -r`)
3. Identify file type with `file`
4. Decompress/extract with appropriate tool
5. Repeat steps 3-4 nine times
6. Final file contains password: **FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn**

This level teaches you to handle multiple compression layers - a common real-world scenario in malware analysis, forensics, and system administration!
