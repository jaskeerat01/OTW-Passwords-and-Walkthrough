# Bandit Level 8 → 9 Walkthrough

## Level 7 Password
```
4CKMh1JI91bUIZZPXDqGanal4xvAg0JM
```

## Objective
Find the password for the next level stored in the file **data.txt**. The password is **the only line of text that occurs only once**.

## Step-by-Step Solution

### Step 1: Connect to the Server
```bash
ssh bandit8@bandit.labs.overthewire.org -p 2220
```
Enter the password: `4CKMh1JI91bUIZZPXDqGanal4xvAg0JM`

### Step 2: Explore the File
```bash
ls -lh
```
You'll see `data.txt`.

```bash
wc -l data.txt
```
Shows the file has **many lines** with duplicate entries.

```bash
head -20 data.txt
```
Preview the file - you'll notice many repeated lines.

### Step 3: Find the Unique Line
Use `sort` and `uniq` to find the line that appears only once:

```bash
sort data.txt | uniq -u
```

**Output:**
```
UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR
```

This is your password for Level 9!

## Understanding the Solution

### Your Approach Breakdown

```bash
sort data.txt | uniq -u
```

**Step-by-step process:**

1. **`sort data.txt`** - Sorts all lines alphabetically
   ```
   Before sort:          After sort:
   apple                 apple
   banana                apple
   apple                 apple
   cherry                banana
   banana                cherry
   ```

2. **`|`** - Pipe operator passes sorted output to next command

3. **`uniq -u`** - Shows only unique lines (lines that appear exactly once)
   - `-u` flag = "unique" (occurs only once)
   ```
   Input:                Output:
   apple                 cherry
   apple
   apple
   banana
   cherry
   ```

### Why Sorting is Required

**`uniq` only detects adjacent duplicates!**

**Without sorting (WRONG):**
```bash
uniq -u data.txt
```
This would miss duplicates that aren't next to each other.

**Example:**
```
Input file:           uniq -u output (WRONG):
apple                 apple
banana                banana
apple                 apple
cherry                cherry
```

**With sorting (CORRECT):**
```bash
sort data.txt | uniq -u
```
```
Sorted:               uniq -u output (CORRECT):
apple                 cherry
apple
banana
cherry
```

## Alternative Methods

### Method 1: Using `uniq -c` to Count Occurrences
```bash
sort data.txt | uniq -c | grep "^ *1 "
```

**Breakdown:**
- `uniq -c` - Count occurrences of each line
- `grep "^ *1 "` - Find lines that occur exactly 1 time

**Output:**
```
      1 UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR
```

### Method 2: Extract Just the Password
```bash
sort data.txt | uniq -c | grep "^ *1 " | awk '{print $2}'
```
Prints only the password without the count.

### Method 3: Using `awk` (Advanced)
```bash
awk '{count[$0]++} END {for (line in count) if (count[line] == 1) print line}' data.txt
```
This counts occurrences in a single pass without sorting.

### Method 4: Full Pipeline Visualization
```bash
sort data.txt | uniq -c | sort -n | head -1
```
Shows the line with the lowest count (should be 1).

## Key Concepts

### The `sort` Command

**Basic usage:**
```bash
sort [options] [file]
```

**Common options:**
- `-n` - Numeric sort (1, 2, 10 instead of 1, 10, 2)
- `-r` - Reverse order (descending)
- `-u` - Remove duplicates (combines sort + unique)
- `-k [n]` - Sort by column n
- `-t [char]` - Use char as field delimiter

**Examples:**
```bash
# Sort and remove duplicates in one command
sort -u data.txt

# Sort numbers properly
sort -n numbers.txt

# Reverse sort
sort -r data.txt

# Sort by second column
sort -k2 data.txt
```

### The `uniq` Command

**`uniq`** = Remove or count duplicate adjacent lines

**Common options:**
- `-u` - Show only unique lines (appear once)
- `-d` - Show only duplicate lines (appear more than once)
- `-c` - Count occurrences
- `-i` - Case-insensitive comparison

**Important:** `uniq` only works on **adjacent** lines, so always sort first!

**Examples:**
```bash
# Show only duplicates
sort data.txt | uniq -d

# Count all occurrences
sort data.txt | uniq -c

# Show unique lines (case-insensitive)
sort data.txt | uniq -ui
```

### Pipe Chains

Pipes let you chain multiple commands together:

```bash
command1 | command2 | command3
```

**Data flow:**
```
data.txt → sort → [sorted lines] → uniq -u → [unique line]
```

**More complex example:**
```bash
cat data.txt | sort | uniq -c | sort -rn | head -10
```
Shows top 10 most common lines.

## Understanding the Data

The `data.txt` file looks something like:
```
VkBAEWyIibVkeURZV5mowiGg6i3m7Be0
zdd2ctVveROGeiS2WE3TeLZMeL5jL7iM
sYSokIATVvFUKU4sAHTtMarfjlZWWj5i
VkBAEWyIibVkeURZV5mowiGg6i3m7Be0  ← duplicate
zdd2ctVveROGeiS2WE3TeLZMeL5jL7iM  ← duplicate
UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR  ← UNIQUE (password!)
sYSokIATVvFUKU4sAHTtMarfjlZWWj5i  ← duplicate
VkBAEWyIibVkeURZV5mowiGg6i3m7Be0  ← duplicate
```

Most lines appear multiple times, but one line appears exactly once.

## Common Mistakes to Avoid

1. **Forgetting to sort first**
   ```bash
   uniq -u data.txt  # WRONG - misses non-adjacent duplicates
   ```

2. **Using wrong uniq flag**
   ```bash
   sort data.txt | uniq -d  # Shows duplicates, not unique!
   ```

3. **Confusing -u with sort -u**
   ```bash
   sort -u data.txt  # Removes ALL duplicates (leaves one copy)
   uniq -u           # Shows lines that appear ONLY once
   ```

4. **Not understanding the requirement**
   - "Occurs only once" = appears exactly 1 time
   - NOT "unique values after deduplication"

## Comparing Commands

| Command | What it does |
|---------|--------------|
| `sort -u data.txt` | Remove duplicates, keep one copy of each |
| `sort data.txt \| uniq` | Same as `sort -u` |
| `sort data.txt \| uniq -u` | Show lines that appear only once |
| `sort data.txt \| uniq -d` | Show lines that appear more than once |
| `sort data.txt \| uniq -c` | Count occurrences of each line |

## Visual Example

**Original file:**
```
AAA
BBB
AAA
CCC
BBB
AAA
DDD
```

**After `sort data.txt`:**
```
AAA
AAA
AAA
BBB
BBB
CCC
DDD
```

**After `sort data.txt | uniq -c`:**
```
      3 AAA
      2 BBB
      1 CCC
      1 DDD
```

**After `sort data.txt | uniq -u`:**
```
CCC
DDD
```

## Real-World Applications

This technique is crucial for:
- **Log analysis** - Finding unique error messages
- **Data deduplication** - Identifying anomalies in datasets
- **Security monitoring** - Detecting one-time events vs repeated patterns
- **Troubleshooting** - Finding outliers in system outputs
- **Data validation** - Ensuring uniqueness constraints

## Practice Exercises

Try these to master the concepts:

```bash
# Count how many lines are in the file
wc -l data.txt

# Show all duplicate lines
sort data.txt | uniq -d

# Count total unique lines (after deduplication)
sort data.txt | uniq | wc -l

# Show top 5 most common lines
sort data.txt | uniq -c | sort -rn | head -5

# Find lines that appear exactly 2 times
sort data.txt | uniq -c | grep "^ *2 "

# Compare: all unique values vs. appears-once-only
sort -u data.txt > all_unique.txt
sort data.txt | uniq -u > appears_once.txt
diff all_unique.txt appears_once.txt
```

## Performance Comparison

For large files:
- `sort | uniq -u` - Fast, memory-efficient (uses disk if needed)
- `awk` solution - Faster but uses more memory
- Manual search - Impossible for files with thousands of lines

## Pro Tips

1. **Always sort before using uniq** unless you're 100% sure data is pre-sorted
2. **Use `uniq -c` for debugging** to see occurrence counts
3. **Combine with grep** to filter specific patterns
4. **Remember the difference:**
   - `sort -u` = deduplicate (keep one of each)
   - `uniq -u` = truly unique (appears only once)
5. **Pipeline debugging:** Run each command step-by-step to understand the flow

## Exit and Move to Next Level
```bash
exit
```

Now use the password you found to connect to Level 9!

---

## Quick Reference Card

```bash
# Connect
ssh bandit8@bandit.labs.overthewire.org -p 2220
Password: 4CKMh1JI91bUIZZPXDqGanal4xvAg0JM

# Find the unique line (your solution)
sort data.txt | uniq -u

# Alternative: with count visualization
sort data.txt | uniq -c | grep "^ *1 "

# Extract just the password
sort data.txt | uniq -c | grep "^ *1 " | awk '{print $2}'
```

## Sort & Uniq Cheat Sheet

| Command | Description |
|---------|-------------|
| `sort file` | Sort lines alphabetically |
| `sort -n file` | Sort numerically |
| `sort -r file` | Reverse sort |
| `sort -u file` | Sort and remove duplicates |
| `uniq file` | Remove adjacent duplicates |
| `uniq -u file` | Show lines appearing once |
| `uniq -d file` | Show duplicate lines |
| `uniq -c file` | Count occurrences |
| `sort \| uniq -c` | Count all occurrences |
| `sort \| uniq -u` | Find truly unique lines |
