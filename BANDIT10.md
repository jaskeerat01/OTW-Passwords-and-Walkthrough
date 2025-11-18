# Bandit Level 10 → 11 Walkthrough

## Level 9 Password
```
dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr
```

## Objective
Find the password for the next level stored in the file **data.txt**, which contains **base64 encoded data**.

## Step-by-Step Solution

### Step 1: Connect to the Server
```bash
ssh bandit10@bandit.labs.overthewire.org -p 2220
```
Enter the password: `dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr`

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

This tells us it's a regular text file (not binary like the previous level).

### Step 3: View the File Contents
```bash
cat data.txt
```
**Output:**
```
VGhlIHBhc3N3b3JkIGlzIDZ6UGV6aUxkUjJSS05kTllGTmI2blZDS3pwaGxYSEJNCg==
```

This is **base64 encoded** data! You can recognize it by:
- Only contains letters (A-Z, a-z), numbers (0-9), `+`, `/`, and `=`
- Often ends with `=` or `==` (padding characters)
- No spaces or special characters

### Step 4: Decode the Base64 Data

```bash
base64 -d data.txt
```

**Output:**
```
The password is 6zPeziLdR2RKNdNYFNb6nVCKzphlXHBM
```

The password is: **6zPeziLdR2RKNdNYFNb6nVCKzphlXHBM**

### How It Works

**`base64 -d data.txt`**
- `base64` - The base64 encoding/decoding utility
- `-d` - Decode flag (convert FROM base64 TO plain text)
- `data.txt` - The file containing encoded data

**Flow:**
```
data.txt → base64 -d → [decoded plain text] → password revealed
```

## Understanding Base64 Encoding

### What is Base64?

Base64 is a **binary-to-text encoding** scheme that converts binary data into ASCII text using only 64 printable characters.

**The 64 characters are:**
- **A-Z** (26 uppercase letters)
- **a-z** (26 lowercase letters)
- **0-9** (10 digits)
- **+** and **/** (2 special characters)
- **=** (padding character)

### Why Use Base64?

Base64 is used to:
- **Transmit binary data** over text-only channels (email, JSON, XML)
- **Embed images** in HTML/CSS (`data:image/png;base64,...`)
- **Store binary data** in text formats
- **Encode credentials** in HTTP Basic Authentication
- **Obfuscate data** (NOT encryption, just encoding!)

### How Base64 Encoding Works

**Example: Encoding "Cat"**

1. **Convert to binary:**
   ```
   C   a   t
   67  97  116  (ASCII values)
   01000011 01100001 01110100  (binary)
   ```

2. **Group into 6-bit chunks:**
   ```
   010000 110110 000101 110100
   ```

3. **Convert to base64 characters:**
   ```
   16  54  5   52  (decimal)
   Q   2   F   0   (base64)
   ```

4. **Add padding if needed:**
   ```
   Q2F0  (no padding needed, evenly divisible)
   ```

**Result:** "Cat" → "Q2F0"

### Recognizing Base64

**Base64 encoded strings have these characteristics:**

✅ **Only contains:** A-Z, a-z, 0-9, +, /, =
✅ **Length is multiple of 4** (due to padding)
✅ **= appears only at the end** (0, 1, or 2 times)
✅ **Looks random but readable**

**Examples:**
```
SGVsbG8gV29ybGQh           ← Valid base64
VGhlIHBhc3N3b3JkIGlzIA==   ← Valid base64 (with padding)
Hello World!                ← NOT base64 (has spaces, !)
```

## The `base64` Command

### Basic Syntax
```bash
base64 [options] [file]
```

### Common Options

| Option | Description |
|--------|-------------|
| `-d` / `--decode` | Decode base64 to plain text |
| `-e` / `--encode` | Encode plain text to base64 (default) |
| `-w NUM` | Wrap lines at NUM characters (0 = no wrap) |
| `-i` / `--ignore-garbage` | Ignore non-base64 characters |

### Usage Examples

#### Encoding (Plain Text → Base64)
```bash
# Encode a string
echo "Hello World" | base64
# Output: SGVsbG8gV29ybGQK

# Encode a file
base64 file.txt > encoded.txt

# Encode without newline
echo -n "Hello World" | base64
# Output: SGVsbG8gV29ybGQ=
```

#### Decoding (Base64 → Plain Text)
```bash
# Decode a string
echo "SGVsbG8gV29ybGQ=" | base64 -d
# Output: Hello World

# Decode a file
base64 -d encoded.txt > decoded.txt

# Decode from data.txt (our challenge)
base64 -d data.txt
```

#### Advanced Usage
```bash
# Encode without line wrapping
base64 -w 0 largefile.txt

# Decode and ignore invalid characters
base64 -di data.txt

# Encode and save to file
cat secret.txt | base64 > secret.b64

# Decode directly to a new file
base64 -d secret.b64 > secret_decoded.txt
```

## Alternative Methods

### Method 1: Using cat and pipe (Most Common)
```bash
cat data.txt | base64 -d
```

### Method 2: Direct file reading (Our Approach)
```bash
base64 -d data.txt
```

### Method 3: Using redirect
```bash
base64 -d < data.txt
```

### Method 4: Save to file and view
```bash
base64 -d data.txt > output.txt
cat output.txt
```

All methods produce the same result!

## Common Mistakes to Avoid

1. **Forgetting the `-d` flag**
   ```bash
   base64 data.txt  # This ENCODES instead of decodes!
   ```

2. **Including newlines when encoding manually**
   ```bash
   echo "Hello" | base64      # Includes newline
   echo -n "Hello" | base64   # No newline (correct)
   ```

3. **Trying to decode non-base64 data**
   ```bash
   echo "Hello World!" | base64 -d  # Error: invalid input
   ```

4. **Not checking file contents first**
   - Always `cat` the file to verify it's base64 before decoding

## Real-World Applications

Base64 encoding is used in:
- **Email attachments** (MIME encoding)
- **Web development** (data URIs for images)
- **APIs** (JWT tokens, authentication)
- **Certificates** (PEM format SSL/TLS certificates)
- **Passwords** (HTTP Basic Authentication)
- **Data storage** (embedding binary in JSON/XML)

**Important:** Base64 is **NOT encryption**! It's encoding. Anyone can decode it easily.

## Practice Exercises

```bash
# View the encoded data
cat data.txt

# Decode the data
base64 -d data.txt

# Try encoding your own text
echo "This is a test" | base64

# Decode your own encoded text
echo "VGhpcyBpcyBhIHRlc3QK" | base64 -d

# Encode a longer message
echo -n "The password is hidden" | base64

# Practice encoding and decoding
echo "Secret" | base64 | base64 -d

# Encode a file
echo "My secret password: 12345" > test.txt
base64 test.txt

# Decode back
base64 test.txt | base64 -d
```

## Understanding the Password Format

The decoded output shows:
```
The password is 6zPeziLdR2RKNdNYFNb6nVCKzphlXHBM
```

Notice:
- The message explicitly states what it is
- The password is clearly marked
- OverTheWire passwords are typically 32 characters
- Mix of uppercase, lowercase, and numbers

## Comparison with Previous Level

| Level 9 → 10 | Level 10 → 11 |
|--------------|---------------|
| Binary file | Text file |
| Used `strings` | Used `base64 -d` |
| Searched with `grep` | Direct decoding |
| Multiple equals signs | Base64 encoding |

Both levels teach **data extraction from obscured formats**!

## Base64 vs Other Encodings

### Base64
- 64 characters (A-Z, a-z, 0-9, +, /)
- Commonly used for binary-to-text
- ~33% size increase

### Hex (Base16)
- 16 characters (0-9, A-F)
- Each byte = 2 hex digits
- 100% size increase
- Example: "Cat" → "436174"

### Base32
- 32 characters (A-Z, 2-7)
- More robust, fewer ambiguous characters
- ~60% size increase

### URL-Safe Base64
- Replaces `+` with `-` and `/` with `_`
- No padding `=` characters
- Used in URLs and filenames

## Why This Level Matters

This challenge teaches:
- **Encoding vs. Encryption** - Understanding the difference
- **Base64 recognition** - Identifying encoded data
- **Command-line tools** - Using `base64` utility
- **Data transformation** - Converting between formats
- **Common web practices** - Base64 is everywhere in web development

## Security Note

**Base64 is NOT encryption!**
- ❌ Does NOT provide security
- ❌ Does NOT protect data
- ❌ Anyone can decode it
- ✅ Only useful for encoding binary data as text
- ✅ Often misused as "obfuscation"

Never store sensitive data in base64 thinking it's secure!

## Pro Tips

1. **Recognize base64 by sight** - Only A-Z, a-z, 0-9, +, /, =
2. **Check for padding** - Strings ending in = or == are likely base64
3. **Use `-d` to decode** - Remember: d for decode
4. **Base64 is reversible** - Encoding → Decoding → Original
5. **File type doesn't matter** - Can encode/decode any data
6. **Length is always multiple of 4** - Due to padding

## Quick Verification

To verify if something is base64:
```bash
# Try to decode it
echo "VGVzdA==" | base64 -d
# If it works without errors, it was base64!

# If you get "invalid input", it's not base64
echo "This is not base64!" | base64 -d
# Error: invalid input
```

## Exit and Move to Next Level
```bash
exit
```

Now use the password you found to connect to Level 11!

---

## Quick Reference Card

```bash
# Connect
ssh bandit10@bandit.labs.overthewire.org -p 2220
Password: dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr

# View the encoded data
cat data.txt

# Decode base64 (SOLUTION)
base64 -d data.txt

# Alternative: pipe method
cat data.txt | base64 -d

# Practice encoding
echo "test" | base64

# Practice decoding
echo "dGVzdAo=" | base64 -d
```

## Base64 Command Cheat Sheet

| Command | Description |
|---------|-------------|
| `base64 file` | Encode file to base64 |
| `base64 -d file` | Decode base64 file |
| `echo "text" \| base64` | Encode string |
| `echo "encoded" \| base64 -d` | Decode string |
| `base64 -w 0` | Encode without line wrap |
| `base64 -di` | Decode, ignore errors |

## Summary

**Level 10 → 11** is straightforward:
1. Connect to the server
2. View the file (it's base64 encoded)
3. Decode with `base64 -d data.txt`
4. Extract the password: **6zPeziLdR2RKNdNYFNb6nVCKzphlXHBM**
5. Move to next level!

This level introduces you to base64, one of the most common encoding schemes you'll encounter in cybersecurity, web development, and general computing.
