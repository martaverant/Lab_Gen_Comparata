# 00_Bash: Command Line Notes
This document contains basic Bash commands and notes for the *Laboratorio di Genomica Comparata* course.

---

## 1. Basic Navigation & File Operations

| Command | Description | Example |
| :--- | :--- | :--- |
| `pwd` | Show your current folder (working directory) | `pwd` |
| `ls` | List files and folders in the current directory | `ls` |
| `cd <dir>` | Change directory (`cd` alone goes back to `~` home; `cd ..` goes one folder up) | `cd ~/Lab_Gen_Comparata` |
| `mkdir <dir>` | Create directory (`-p` creates nested subdirectories at once) | `mkdir -p project/{data,scripts}` |
| `cp <src> <dst>` | Copy a file or directory (`-r` for directories; `.` means current folder) | `cp file.txt .` |
| `mv <src> <dst>` | Move or rename a file or directory | `mv old.txt new.txt` |
| `rm <file>` | Delete a file permanently (**use with caution!**) | `rm temp.txt` |
| `rm -r <dir>` | Delete a folder and everything inside it | `rm -r old_folder/` |

Moving through multiple folders at once (e.g., jumping up multiple levels with `../../` or chaining subfolders with `Tab` auto-completion):
```bash
cd ../../                        # Move up two directory levels
cd Lab_Gen_Comparata/03.../00.../ # Chain folders and complete names with Tab
```

*Tip: Avoid spaces in file and directory names—use underscores (`_`) or hyphens (`-`) instead.*

---

## 2. Reading, Inspecting & System Commands

| Command | Description | Example |
| :--- | :--- | :--- |
| `cat <file>` | Display full file content in standard output | `cat file.txt` |
| `less <file>` | Open a file to scroll through interactively (press `q` to exit) | `less sequences.fastq` |
| `head -n 10 <file>` | Show first 10 lines of a file | `head -n 10 genome.fasta` |
| `tail -n 10 <file>` | Show last 10 lines of a file | `tail -n 10 genome.fasta` |
| `wc -l <file>` | Count total lines in a file (`wc` alone shows lines, words, bytes) | `wc -l reads.fastq` |
| `nano <file>` | Open a simple text editor in the terminal (`Ctrl+s` save, `Ctrl+x` exit) | `nano script.sh` |
| `man <cmd>` / `--help` | Display manual or help options for a command | `grep --help` |
| `df -h` / `du -sh` | Check available disk space / folder size | `du -sh *` |

---

## 3. Redirection & Pipes

- `>` : Save output to a file (overwrites existing content).
- `>>` : Append output to the end of an existing file without deleting its contents.
- `|` (Pipe) : Pass the standard output of one command as input into another command.

**Example: Count how many sequences are in a FASTA file and save the total to a text file:**
```bash
grep ">" input.fasta | wc -l > sequence_count.txt
```

---

## 4. Text Processing Tools

**`grep` (Search for text patterns):**
```bash
grep ">" genome.fasta          # Find all FASTA header lines
grep -c ">" genome.fasta       # Count how many FASTA header lines exist
grep -v "^#" annotation.gff3   # Exclude comment lines starting with "#"
```

**`awk` (Column extraction and processing):**
```bash
awk '{print $1}' table.tsv     # Print only the first column of a table
```

**`sed` (Search and replace text):**
```bash
sed 's/old/new/g' file.txt     # Replace every occurrence of "old" with "new"
```

**`sort` & `uniq` (Sorting and counting unique entries):**
```bash
sort file.txt | uniq -c        # Sort lines and count unique occurrences
```

---

## 5. File Permissions & Running Scripts

A basic robust Bash script starts with a shebang (`#!/bin/bash`) and safety flags:
```bash
#!/bin/bash
set -e          # Exit immediately if a command fails
set -u          # Treat unset variables as an error
set -o pipefail # Fail if any command in a pipe fails
```

Change permissions to make a script executable and run it:
```bash
chmod +x script.sh    # Make script executable
./script.sh           # Execute script
```

---

## 6. Essential Keyboard Shortcuts

- **`Tab` (Auto-completion):** Type the first few letters of a command, file, or folder name and press `Tab`. Press `Tab` twice to see all matching options.
- **`Up / Down Arrows` (Command history):** Scroll back and forward through previously used commands.
- **`Home` / `Ctrl + a`:** Move cursor to the beginning of the current line.
- **`End` / `Ctrl + e`:** Move cursor to the end of the current line.
- **`Ctrl + c` (Cancel):** Stops and cancels a running command immediately.
- **`Ctrl + l` or `clear` (Clear screen):** Clears the terminal window to keep your workspace clean.
