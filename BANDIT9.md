# Bandit Level 9 → 10 Walkthrough

## Level 8 Password
```
FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey
```

## Objective
Find the password for the next level stored in the file **data.txt** in one of the few **human-readable strings**, preceded by several **'='** characters.

## Step-by-Step Solution

### Step 1: Connect to the Server
```bash
ssh bandit9@bandit.labs.overthewire.org -p 2220
```
Enter the password: `FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey`

### Step 2: Explore the File
```bash
ls -lh
```
You'll see `data.txt` with a certain file size.

```bash
file data.txt
```
**Output:**
```
data.txt: data
```

This tells us it's a **binary file** (not plain text)!

### Step 3: Try Regular grep (This Will Fail)
```bash
grep '=====' data.txt
```
**Output:**
```
Binary file data.txt matches
```

Grep detected binary content and won't display results by default.

### Step 4: Solution - Extract Human-Readable Strings

## Method 1: Using `strings` (Recommended)

```bash
strings data.txt | grep '====='
```

**Output will show several lines, look for the one with the password:**
```
========== the
========== password
========== is
========== truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk
```

The password is: `truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk`

### How Method 1 Works

**Step-by-step breakdown:**

1. **`strings data.txt`**
   - Extracts all human-readable text from the binary file
   - Filters out binary data, null bytes, and non-printable characters
   - Only shows sequences of printable characters

2. **`|`** - Pipe operator passes text to next command

3. **`grep '====='`**
   - Searches for lines containing `=====`
   - Filters down to only relevant lines

**Flow:**
```
data.txt → strings → [readable text only] → grep → [lines with =====]
```

## Method 2: Force grep to Treat as Text

```bash
grep -a '=====' data.txt
```

**The `-a` flag:**
- Stands for "text" or "all"
- Forces grep to treat the file as text
- Shows results even from binary files

**Output:**
```
Binary�data�here========== truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk�more�binary
```

The output may contain garbled characters but you can still find the password.

### Comparing Both Methods

| Method | Command | Pros | Cons |
|--------|---------|------|------|
| **strings + grep** | `strings data.txt \| grep '='` | Clean output, only readable text | Requires two commands |
| **grep -a** | `grep -a '=' data.txt` | Single command, faster | May show binary garbage |

**Recommendation:** Use **Method 1** (`strings | grep`) for cleaner, more readable output.

## Understanding the Commands

### The `strings` Command

**`strings`** extracts printable character sequences from files.

**Basic syntax:**
```bash
strings [options] file
```

**Common options:**
- `-n [num]` - Minimum string length (default: 4)
- `-a` - Scan entire file (default skips some sections)
- `-t [format]` - Print offset (location) of string

**Examples:**
```bash
# Show all readable strings
strings data.txt

# Only strings of 10+ characters
strings -n 10 data.txt

# Show location of each string
strings -t x data.txt

# Find specific pattern in strings
strings data.txt | grep password
```

**What counts as "printable":**
- Letters: A-Z, a-z
- Numbers: 0-9
- Common symbols: !, @, #, $, %, etc.
- Spaces and tabs

**NOT printable:**
- Binary data (0x00, 0xFF, etc.)
- Control characters
- Null bytes

### Why Files Become "Binary"

A file is considered binary if it contains:
- **Null bytes** (0x00)
- **Non-ASCII characters** (> 127)
- **Control characters** (0x01-0x1F)
- **Mixed text and binary data**

**Example:**
```
Hello\x00\xFF\x00World  ← Binary file
Hello World             ← Text file
```

### The grep `-a` Flag

**`-a` / `--text`** - Process binary files as if they were text

**Other grep binary-related options:**
- `-I` - Ignore binary files completely
- `--binary-files=text` - Same as `-a`
- `--binary-files=without-match` - Same as `-I`

## Looking for Patterns with Multiple '='

The challenge says "preceded by several '=' characters". Let's search more specifically:

```bash
# Find lines with multiple equals signs
strings data.txt | grep '===='

# More specific: 5 or more equals signs
strings data.txt | grep '====='

# Very specific: 10 or more equals signs
strings data.txt | grep '=========='

# Show lines with context (before and after)
strings data.txt | grep -C 2 '====='
```

## Understanding the Data Structure

The `data.txt` file contains:
```
[Binary data]
[Binary data]
========== some text
[Binary data]
========== the password
[Binary data]
========== truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk
[Binary data]
```

The password is hidden among binary data but marked with `=====` signs.

## Common Mistakes to Avoid

1. **Using grep without handling binary**
   ```bash
   grep '=====' data.txt  # Returns "Binary file matches"
   ```

2. **Not using strings first**
   - Binary data makes output unreadable
   - `strings` cleanly extracts only readable text

3. **Wrong number of equals signs**
   ```bash
   grep '=' data.txt      # Too broad, matches single =
   grep '=====' data.txt  # Just right
   ```

4. **Forgetting to pipe with strings**
   ```bash
   strings data.txt       # Shows all strings (too much)
   strings data.txt | grep '====='  # Filters to relevant lines
   ```

## Real-World Applications

Working with binary files is common in:
- **Malware analysis** - Extracting strings from executables
- **Forensics** - Finding hidden data in files
- **Reverse engineering** - Analyzing compiled programs
- **Data recovery** - Extracting text from corrupted files
- **CTF challenges** - Finding hidden flags in binary data

## Practice Exercises

```bash
# View the file type
file data.txt

# See raw binary content (first 100 bytes in hex)
xxd data.txt | head

# Extract all readable strings
strings data.txt

# Find strings with specific length
strings -n 20 data.txt

# Search for multiple patterns
strings data.txt | grep -E '=====|password'

# Count how many readable strings exist
strings data.txt | wc -l

# Show longest strings
strings data.txt | awk '{print length, $0}' | sort -rn | head
```

## Advanced Techniques

### Extract Specific String Lengths
```bash
# Only strings between 20-40 characters
strings data.txt | awk 'length($0) >= 20 && length($0) <= 40'
```

### Find Strings with Pattern
```bash
# Strings that look like passwords (alphanumeric, 20+ chars)
strings data.txt | grep -E '^[a-zA-Z0-9]{20,}$'
```

### Multiple Filters
```bash
# Find password-like strings preceded by equals
strings data.txt | grep '=====' | grep -E '[a-zA-Z0-9]{20,}'
```

## Hex Dump Analysis (Optional Deep Dive)

To see what's actually in the file:

```bash
xxd data.txt | less
```

**Output example:**
```
00000000: 89ab cdef 0123 4567 89ab cdef 0123 4567  .....#Eg.....#Eg
00000010: 3d3d 3d3d 3d20 7472 756b 4c64 6a73 624a  ===== truKLdjsbJ
```

You can see:
- Left: Hexadecimal bytes
- Right: ASCII representation (`.` for non-printable)

## Why This Level Matters

This challenge teaches:
- **Binary vs. text files** - Understanding file types
- **String extraction** - Essential for security analysis
- **Grep limitations** - When it works and when it doesn't
- **Command chaining** - Combining tools for complex tasks
- **Pattern matching** - Finding specific markers in data

## Pro Tips

1. **Always use `file` command first** to identify file type
2. **`strings` is your friend** for binary file analysis
3. **Pipe with grep** to filter large outputs
4. **Use specific patterns** (multiple `=`) to reduce false positives
5. **`-a` flag works** but `strings` gives cleaner output
6. **Look for markers** - equals signs often mark important data

## Exit and Move to Next Level
```bash
exit
```

Now use the password you found to connect to Level 10!

---

## Quick Reference Card

```bash
# Connect
ssh bandit9@bandit.labs.overthewire.org -p 2220
Password: FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey

# Check file type
file data.txt

# Method 1: Extract strings and search (RECOMMENDED)
strings data.txt | grep '====='

# Method 2: Force grep to treat as text
grep -a '=====' data.txt

# View all readable strings
strings data.txt

# View hex dump
xxd data.txt | head
```

## Binary File Commands Cheat Sheet

| Command | Description |
|---------|-------------|
| `file filename` | Identify file type |
| `strings filename` | Extract readable text |
| `strings -n 10 file` | Min 10 char strings |
| `grep -a pattern file` | Force text search |
| `xxd filename` | Hex dump of file |
| `hexdump -C file` | Another hex viewer |
| `cat -v file` | Show non-printables |
