# Bandit Level 5 → 6 Walkthrough

## Objective
Find the password for the next level stored in a file somewhere under the `inhere` directory with these properties:
- Human-readable
- 1033 bytes in size
- Not executable

## Step-by-Step Solution

### Step 1: Connect to the Server
```bash
ssh bandit5@bandit.labs.overthewire.org -p 2220
```
Enter the password from Level 4 when prompted.

### Step 2: Explore the Directory Structure
```bash
ls -la
```
You'll see the `inhere` directory.

```bash
cd inhere
ls
```
You'll notice there are many subdirectories (maybehere00, maybehere01, etc.).

### Step 3: Check How Many Files Exist
```bash
ls -R | wc -l
```
This shows there are MANY files to search through manually—impractical!

### Step 4: Use the `find` Command
Use `find` with specific criteria to locate the exact file:

```bash
find . -type f -size 1033c ! -executable
```

**Breaking down the command:**
- `find .` - Search in the current directory
- `-type f` - Only find regular files (not directories)
- `-size 1033c` - File size is exactly 1033 bytes (c = bytes)
- `! -executable` - File is NOT executable (! means NOT)

### Step 5: Read the File
The command will output something like:
```
./maybehere07/.file2
```

Read the file's contents:
```bash
cat ./maybehere07/.file2
```

The output is your password for Level 6!

## Alternative Methods

### Method 2: More Explicit Find Command
```bash
find . -type f -readable -size 1033c ! -executable
```
Adding `-readable` explicitly checks for human-readable files.

### Method 3: Find + Xargs (Advanced)
```bash
find . -type f -size 1033c ! -executable -exec cat {} \;
```
This automatically displays the file content without needing a second command.

### Method 4: Find with File Command
```bash
find . -type f -size 1033c ! -executable -exec file {} \;
```
This shows file type information to verify it's human-readable.

## Key Concepts

**Find Command Operators:**
- `-type f` = files only
- `-type d` = directories only
- `-size [n]c` = size in bytes
- `-size [n]k` = size in kilobytes
- `! [condition]` = NOT (negation)
- `-executable` = file can be executed
- `-readable` = file can be read

**Size Units:**
- `c` = bytes
- `k` = kilobytes
- `M` = megabytes
- `G` = gigabytes

## Common Mistakes to Avoid

1. **Forgetting the 'c' in size**: `-size 1033` without `c` looks for 1033 blocks, not bytes
2. **Missing the '!' operator**: Without `!`, you'd search for executable files instead
3. **Not using `-type f`**: You might accidentally match directories
4. **Manual searching**: With hundreds of files, manual checking is inefficient

## Tips for Future Levels

- Always read the file properties carefully
- Use `find` for complex search criteria
- Combine multiple conditions to narrow results
- Remember: `!` negates conditions
- The `c` suffix means bytes in `-size`

## Exit and Move to Next Level
```bash
exit
```
Key:
```
HWasnPhtq9AVKe0dmk45nxy20cvUa6EG
```
Now use the password you found to connect to Level 6!
