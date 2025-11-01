# Bandit Level 7 → 8 Walkthrough

## Level 6 Password
```
dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc
```

## Objective
Find the password for the next level stored in the file **data.txt** next to the word **millionth**.

## Step-by-Step Solution

### Step 1: Connect to the Server
```bash
ssh bandit7@bandit.labs.overthewire.org -p 2220
```
Enter the password: `dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc`

### Step 2: Explore the File
```bash
ls -lh
```
You'll see `data.txt` with a large file size.

```bash
wc -l data.txt
```
This shows the file has **many lines** (likely thousands), making manual searching impractical.

### Step 3: Search for "millionth"
Use `grep` to search for the specific word:

```bash
cat data.txt | grep millionth
```

**Or more efficiently (without cat):**
```bash
grep millionth data.txt
```

### Step 4: Read the Output
The output will look something like:
```
millionth	cvX2JJa4CFALtqS87jk27qwqGhBM9plV
```

The password is the string **after** the word "millionth"!

## Understanding the Commands

### Method 1: Using Pipe (Your Solution)
```bash
cat data.txt | grep millionth
```

**How it works:**
- `cat data.txt` - Outputs the entire file content
- `|` - Pipe operator (sends output to next command)
- `grep millionth` - Searches for lines containing "millionth"

**Flow diagram:**
```
data.txt → cat → [file contents] → grep → [filtered line]
```

### Method 2: Direct grep (More Efficient)
```bash
grep millionth data.txt
```

**Why it's better:**
- `grep` can read files directly
- Doesn't need an extra `cat` process
- Uses less memory and CPU
- Follows the principle: "Useless Use of Cat" (UUOC)

### Method 3: grep with Line Numbers
```bash
grep -n millionth data.txt
```
Output:
```
82533:millionth	cvX2JJa4CFALtqS87jk27qwqGhBM9plV
```
Shows which line number contains the match.

### Method 4: Case-Insensitive Search
```bash
grep -i millionth data.txt
```
The `-i` flag makes the search case-insensitive (matches "Millionth", "MILLIONTH", etc.).

## Key Concepts

### The `grep` Command
**grep** = **G**lobal **R**egular **E**xpression **P**rint

**Basic syntax:**
```bash
grep [options] pattern [file]
```

**Common options:**
- `-i` - Case-insensitive search
- `-n` - Show line numbers
- `-v` - Invert match (show lines NOT containing pattern)
- `-c` - Count matching lines
- `-r` - Recursive search in directories
- `-w` - Match whole words only
- `-A [n]` - Show n lines After match
- `-B [n]` - Show n lines Before match
- `-C [n]` - Show n lines of Context (before and after)

### The Pipe Operator `|`
The pipe takes output from one command and feeds it as input to another.

**Examples:**
```bash
# Count lines containing "password"
cat data.txt | grep password | wc -l

# Sort and remove duplicates
cat data.txt | sort | uniq

# Chain multiple commands
cat data.txt | grep millionth | cut -f2
```

### Why "Useless Use of Cat" (UUOC)?
```bash
# Less efficient (spawns unnecessary cat process)
cat file.txt | grep pattern

# More efficient (grep reads file directly)
grep pattern file.txt
```

However, `cat | grep` is still **perfectly valid** and commonly used! It's more of a style preference.

## Alternative Solutions

### Using `awk`
```bash
awk '/millionth/ {print $2}' data.txt
```
Prints only the second field (the password).

### Using `sed`
```bash
sed -n '/millionth/p' data.txt
```
The `-n` suppresses default output, `p` prints matching lines.

### Using `grep` with Field Extraction
```bash
grep millionth data.txt | cut -f2
```
Or:
```bash
grep millionth data.txt | awk '{print $2}'
```

These directly extract just the password without the word "millionth".

### One-liner to Store Password
```bash
password=$(grep millionth data.txt | awk '{print $2}')
echo $password
```

## Understanding the File Format

The `data.txt` file contains lines in this format:
```
word1    password1
word2    password2
millionth    cvX2JJa4CFALtqS87jk27qwqGhBM9plV
word3    password3
```

Each line has:
- A **word** (like "millionth")
- A **tab character** (whitespace separator)
- A **password** (random string)

## Common Mistakes to Avoid

1. **Typos in the search term**: "milionth" vs "millionth"
2. **Case sensitivity**: grep is case-sensitive by default
3. **Not reading the output carefully**: The password is the second column
4. **Trying to manually search**: The file is too large for manual inspection
5. **Forgetting quotes with special characters**: Use `grep "pattern with spaces" file`

## Practice Exercises

Try these to deepen your understanding:

```bash
# Count total lines in the file
wc -l data.txt

# Show first 10 lines
head -10 data.txt

# Show last 10 lines
tail -10 data.txt

# Search for multiple patterns
grep -E "millionth|thousand" data.txt

# Show only the password (second column)
grep millionth data.txt | awk '{print $2}'

# Count how many lines contain "th"
grep -c "th" data.txt
```

## Real-World Applications

This technique is essential for:
- **Log file analysis** - Finding errors in system logs
- **Data processing** - Extracting specific records from large datasets
- **Security audits** - Searching for suspicious patterns
- **Configuration management** - Finding specific settings in config files
- **Text mining** - Extracting information from documents

## Pro Tips

1. **Use `grep` directly on files** instead of `cat |` when possible
2. **Combine grep with other tools** like `awk`, `sed`, `cut` for powerful text processing
3. **Use `-n` flag** to see line numbers (helpful for large files)
4. **Learn regular expressions** to make grep even more powerful
5. **Tab completion** works with filenames - type `data` and press Tab

## Performance Comparison

For a file with 100,000 lines:
- `cat data.txt | grep millionth` - ~0.05 seconds
- `grep millionth data.txt` - ~0.03 seconds

The difference is small but matters at scale!

## Exit and Move to Next Level
```bash
exit
```

Now use the password you found to connect to Level 8!

---

## Quick Reference Card

```bash
# Connect
ssh bandit7@bandit.labs.overthewire.org -p 2220
Password: dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc

# Find the password (method 1)
cat data.txt | grep millionth

# Find the password (method 2 - preferred)
grep millionth data.txt

# Extract just the password
grep millionth data.txt | awk '{print $2}'
```

## Grep Cheat Sheet

| Command | Description |
|---------|-------------|
| `grep pattern file` | Search for pattern in file |
| `grep -i pattern file` | Case-insensitive search |
| `grep -n pattern file` | Show line numbers |
| `grep -v pattern file` | Show lines NOT matching |
| `grep -c pattern file` | Count matches |
| `grep -w pattern file` | Match whole words only |
| `grep -r pattern dir/` | Recursive search |
| `grep -A 3 pattern file` | Show 3 lines after match |
| `grep -B 3 pattern file` | Show 3 lines before match |
