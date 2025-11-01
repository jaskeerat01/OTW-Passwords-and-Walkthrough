# Bandit Level 6 → 7 Walkthrough

## Level 6 Password
```
morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj
```

## Objective
Find the password for the next level stored **somewhere on the server** with these properties:
- Owned by user **bandit7**
- Owned by group **bandit6**
- **33 bytes** in size

## Step-by-Step Solution

### Step 1: Connect to the Server
```bash
ssh bandit6@bandit.labs.overthewire.org -p 2220
```
Enter the password: `morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj`

### Step 2: Understand the Challenge
Unlike Level 5, this file is **somewhere on the entire server**, not just in a specific directory. This means we need to search from the root directory (`/`).

### Step 3: Search the Entire Server
```bash
find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null
```

**Breaking down the command:**
- `find /` - Search from the root directory (entire system)
- `-type f` - Only find regular files
- `-user bandit7` - File owned by user bandit7
- `-group bandit6` - File owned by group bandit6
- `-size 33c` - File size is exactly 33 bytes
- `2>/dev/null` - Suppress "Permission denied" errors

### Step 4: Analyze the Output
The command will output something like:
```
/var/lib/dpkg/info/bandit7.password
```

This is the only file matching ALL criteria!

### Step 5: Read the Password
```bash
cat /var/lib/dpkg/info/bandit7.password
```

The output is your password for Level 7!

## Why `2>/dev/null` is Essential Here

**Without error suppression:**
```bash
find / -type f -user bandit7 -group bandit6 -size 33c
```

You'll get hundreds of lines like:
```
find: '/root': Permission denied
find: '/home/bandit28-git': Permission denied
find: '/home/bandit30-git': Permission denied
/var/lib/dpkg/info/bandit7.password
find: '/home/bandit5/inhere': Permission denied
...
```

**With error suppression:**
```bash
find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null
```

Clean output:
```
/var/lib/dpkg/info/bandit7.password
```

## Understanding the Redirect

**`2>/dev/null` breakdown:**
- **`2`** - stderr (error stream)
- **`>`** - Redirect operator
- **`/dev/null`** - "Black hole" that discards everything

When searching the entire system, you'll hit many directories you don't have permission to access. Rather than cluttering your screen with errors, we discard them and only see successful results.

## Alternative Methods

### Method 2: Save Errors to a File (for debugging)
```bash
find / -type f -user bandit7 -group bandit6 -size 33c 2> errors.txt
```
This lets you review errors later if needed.

### Method 3: Combine Find and Cat
```bash
find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null -exec cat {} \;
```
Automatically displays the password without a second command.

### Method 4: Use Xargs
```bash
find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null | xargs cat
```
Pipes the result to `cat` for immediate display.

## Key Concepts

### Find Command - Ownership Filters
- `-user [username]` - File owned by specific user
- `-group [groupname]` - File owned by specific group
- `-uid [number]` - File owned by user ID
- `-gid [number]` - File owned by group ID

### File Permissions in Linux
Every file has:
- **Owner** (user) - Usually the creator
- **Group** - Users who share access
- **Permissions** - Read, write, execute rights

Example:
```bash
ls -l /var/lib/dpkg/info/bandit7.password
```
Output:
```
-rw-r----- 1 bandit7 bandit6 33 [date] bandit7.password
```
- Owner: `bandit7`
- Group: `bandit6`
- Size: `33` bytes

### Output Redirection Reference
| Command | Meaning |
|---------|---------|
| `1>` | Redirect stdout (normal output) |
| `2>` | Redirect stderr (errors) |
| `&>` | Redirect both stdout and stderr |
| `2>&1` | Redirect stderr to wherever stdout goes |
| `>/dev/null 2>&1` | Discard everything |

## Common Mistakes to Avoid

1. **Searching from current directory**: Using `find .` instead of `find /`
2. **Forgetting error suppression**: Output becomes unreadable with permission errors
3. **Wrong size unit**: Using `-size 33` instead of `-size 33c`
4. **Swapping user/group**: `-user bandit6 -group bandit7` (wrong order)
5. **Missing `-type f`**: Might match directories with the same properties

## Why This Level is Important

This challenge teaches:
- **System-wide searches** - Real-world security audits
- **Error handling** - Filtering noise from useful data
- **File ownership** - Understanding Linux permissions
- **Efficient searching** - Finding needles in haystacks

## Pro Tips

- Always use `2>/dev/null` when searching from `/`
- The file location (`/var/lib/dpkg/info/`) is intentionally obscure
- Combining multiple criteria dramatically reduces false positives
- In real scenarios, you might search for suspicious files with specific owners

## Exit and Move to Next Level
```bash
exit
```

Now use the password you found to connect to Level 7!

---

## Quick Reference Card

```bash
# Connect
ssh bandit6@bandit.labs.overthewire.org -p 2220
Password: morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj

# Find the file
find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null

# Read the password
cat /var/lib/dpkg/info/bandit7.password
```
