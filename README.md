# MD5 Hash Cracker

A simple Python tool for recovering plaintext candidates from **MD5 hashes using a local wordlist**.

The script reads words from a supplied file, calculates the MD5 hash of each candidate, and compares the result against the target hash.

> **Important:** Use this tool only with hashes and credentials you own or have explicit authorization to test.

## Features

* MD5 hash comparison
* Local wordlist support
* Sequential dictionary-based cracking
* ASCII banner using `pyfiglet`
* Stops immediately when a matching candidate is found
* Simple interactive command-line interface

## Requirements

* Python 3
* `pyfiglet`

The script also uses the Python standard-library modules:

```text
hashlib
sys
```

## Installation

Install the required dependency:

```bash
pip install pyfiglet
```

Using a virtual environment is recommended:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install pyfiglet
```

On Windows:

```powershell
.venv\Scripts\Activate.ps1
```

## Important Fix

Make sure the script imports:

```python
import hashlib
```

and not:

```python
import haslib
```

`hashlib` is the Python standard-library module used to calculate MD5 hashes.

## Usage

Save the script as:

```text
hash_cracker.py
```

Run it with:

```bash
python3 hash_cracker.py
```

The program displays an ASCII banner:

```text
Hash Cracker
```

and asks for two values.

First, provide the path to your wordlist:

```text
Enter the path to the wordlist file:
```

For example:

```text
wordlist.txt
```

Then enter the MD5 hash you want to test:

```text
Enter the hash to crack:
```

## Example

Suppose the wordlist contains:

```text
admin
hello
password
testing
example
```

Run:

```bash
python3 hash_cracker.py
```

Then provide:

```text
Enter the path to the wordlist file: wordlist.txt
Enter the hash to crack: <MD5_HASH>
```

If one of the words produces the supplied MD5 digest, the program prints:

```text
Hash cracked! The original word is: <matching-word>
```

The program then terminates.

## How It Works

### 1. Display the Banner

The script uses `pyfiglet`:

```python
ascii_banner = pyfiglet.figlet_format(
    "Hash Cracker"
)

print(ascii_banner)
```

This is purely cosmetic and does not affect the cracking process.

## 2. Read User Input

The program asks for the wordlist location:

```python
wordlist_path = input(
    "Enter the path to the wordlist file: "
)
```

It then asks for the target digest:

```python
hash_to_crack = input(
    "Enter the hash to crack: "
)
```

## 3. Open the Wordlist

The supplied file is opened and processed line by line:

```python
with open(
    wordlist_path,
    "r"
) as wordlist_file:
```

Processing the file line by line means the entire wordlist does not have to be loaded into memory at once.

## 4. Normalize Each Candidate

Newline and surrounding whitespace are removed:

```python
word = word.strip()
```

For example:

```text
password\n
```

becomes:

```text
password
```

## 5. Calculate the MD5 Digest

Each candidate is converted to bytes and hashed:

```python
hashed_word = hashlib.md5(
    word.encode()
).hexdigest()
```

Conceptually:

```text
word
  |
  v
UTF-8 encoding
  |
  v
MD5
  |
  v
hexadecimal digest
```

The result is a 32-character hexadecimal MD5 digest.

## 6. Compare Hashes

The calculated digest is compared with the supplied target:

```python
if hashed_word == hash_to_crack:
```

If they match, the corresponding word has been found in the wordlist.

## 7. Stop on Success

When a match is found, the script prints:

```python
print(
    f"Hash cracked! "
    f"The original word is: {word}"
)
```

It then exits successfully:

```python
sys.exit(0)
```

This prevents unnecessary processing of the remaining wordlist.

## Algorithm

The basic process is:

```text
Target MD5 hash
      |
      v
Read candidate from wordlist
      |
      v
Calculate MD5(candidate)
      |
      v
Compare with target
      |
   +--+--+
   |     |
 Match  No match
   |     |
   v     v
 Print   Read next
 word    candidate
   |
   v
  Exit
```

## Wordlist Format

The wordlist should contain one candidate per line:

```text
password
admin
letmein
example
testing
```

A normal UTF-8 text file is recommended.

## Limitations

The current implementation is intentionally simple.

It:

* supports only MD5;
* performs a dictionary attack rather than brute force;
* only finds values already present in the supplied wordlist;
* processes candidates sequentially;
* does not detect the hash algorithm automatically;
* does not handle salted password hashes;
* does not provide command-line arguments;
* does not display progress;
* does not report the number of candidates tested;
* does not explicitly report when no match is found;
* has limited file-error handling.

## Why a Hash May Not Be Found

If the script reaches the end of the wordlist without finding a match, possible reasons include:

* the plaintext is not present in the wordlist;
* the supplied hash is incorrect;
* the hash is not MD5;
* the password was hashed with a salt;
* the input used a different character encoding;
* whitespace or other characters were part of the original value.

## Security Note

MD5 is a legacy cryptographic hash function and should not be used for modern password storage.

Applications should use a dedicated password-hashing algorithm with appropriate parameters and unique salts rather than fast general-purpose hashes such as MD5.

Examples of password-hashing approaches include:

```text
Argon2id
scrypt
bcrypt
PBKDF2
```

The appropriate choice and parameters depend on the application's environment and security requirements.

## Troubleshooting

### `ModuleNotFoundError: No module named 'pyfiglet'`

Install the dependency:

```bash
pip install pyfiglet
```

### `ModuleNotFoundError: No module named 'haslib'`

Change:

```python
import haslib
```

to:

```python
import hashlib
```

No additional installation is required for `hashlib`.

### `FileNotFoundError`

Verify that the wordlist path is correct.

Instead of:

```text
wordlist.txt
```

you can provide an absolute path such as:

```text
/home/user/wordlists/wordlist.txt
```

### No Result

The current script produces no final message when it reaches the end of the wordlist without finding a match.

This does not necessarily indicate an error. It usually means that none of the supplied candidates generated the target MD5 digest.

## Responsible Use

This project is intended for:

* CTF challenges;
* password-hashing demonstrations;
* local security labs;
* recovery of your own test hashes;
* authorized password auditing;
* cybersecurity education.

Do not use recovered credentials to access systems or accounts without authorization.

## Disclaimer

This project is provided for educational and authorized security-testing purposes.

You are responsible for ensuring that you have permission to test the hashes, credentials, and systems involved.
