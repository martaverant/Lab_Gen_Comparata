# 00_Bash: Command Line Notes
This document contains basic Bash commands and notes for the *Laboratorio di Genomica Comparata* course.

---

## 1. Basic Navigation & File Operations

| Command | Description | Example |
| :--- | :--- | :--- |
| `pwd` | Show your current folder (working directory) | `pwd` |
| `ls` | List files and folders in the current directory | `ls` |
| `cd <dir>` | Change directory (`cd` alone goes to `~` home; `cd ..` goes one folder up) | `cd ~/Lab_Gen_Comparata` |
| `mkdir <dir>` | Create directory (`-p` creates nested subdirectories at once) | `mkdir -p project/{data,scripts}` |
| `cp <src> <dst>` | Copy a file or directory (`-r` for directories; `.` means current folder) | `cp file.txt .` |
| `mv <src> <dst>` | Move or rename a file or directory | `mv old.txt new.txt` |
| `ln -s <target> .` | Create a symbolic link (shortcut) in the current directory (`.`) | `ln -s /abs/path/to/file.fasta .` |
| `rm <file>` | Delete a file permanently (**use with caution!**) | `rm temp.txt` |
| `rm -r <dir>` | Delete a folder and everything inside it | `rm -r old_folder/` |

Moving through multiple folders at once (using `../../` or chaining subfolders with `Tab` auto-completion):
```bash
cd ../../                         # Move up two directory levels
cd Lab_Gen_Comparata/03.../00.../ # Chain folders and complete names with Tab
```

*Tip: Avoid spaces in file and directory names—use underscores (`_`) or hyphens (`-`) instead.*

---

## 2. Reading, Inspecting & Editing Files

| Command | Description | Example |
| :--- | :--- | :--- |
| `cat <file>` | Print the full content of a file to standard output (good for small files) | `cat stats.log` |
| `less <file>` | Open a file for interactive scrolling (`q` to quit; `less -S` keeps long lines on a single line) | `less -S sequences.fastq` |
| `head -n 10 <file>` | Show the first 10 lines of a file (or inspect the start of a piped stream) | `head -n 10 genome.fasta` |
| `tail -n 10 <file>` | Show the last 10 lines of a file (or use `tail -f` to follow a growing log file) | `tail -n 10 genome.fasta` |
| `wc -l <file>` | Count total lines in a file (`wc` alone shows lines, words, and characters) | `wc -l reads.fastq` |
| `nano <file>` | Create or open a text/script file in the terminal to edit and save | `nano mapping.sh` |

---

## 3. Redirection & Pipes (`|`)

- `>` : Redirect standard output to a file (overwrites existing content).
- `>>` : Append standard output to the end of an existing file without deleting its contents.
- `2>` : Redirect standard error (warnings/messages) to a file.
- `|` (Pipe) : Pass the standard output of one command directly as the standard input of the next command—without needing an intermediate file. Only the first command in a pipe takes the original input file.

**Examples:**
```bash
grep ">" input.fasta | wc -l > sequence_count.txt   # Count FASTA sequences and save total
grep "word" file1 | sed 's/ /\t/g' | head -n 10     # Chain commands and peek at first 10 lines
```

---

## 4. Text Processing Tools (`grep`, `awk`, `sed`)

**`grep` (Search for text patterns in files or streams):**
```bash
grep ">" genome.fasta          # Print all lines matching ">" (e.g. FASTA headers)
grep -c ">" genome.fasta       # Count how many lines match ">"
grep -v "^#" annotation.gff3   # Invert match: exclude lines starting with "#"
grep -i "pattern" file.txt     # Case-insensitive search
```

**`awk` (Column extraction and processing):**
```bash
awk '{print $1}' table.tsv     # Print only the first column of a table
```

**`sed` (Search and replace text):**
```bash
sed 's/old/new/g' file.txt     # Replace every occurrence of "old" with "new"
```

---

## 5. File Permissions & Running Scripts

Start Bash scripts (`.sh`) with a shebang (`#!/bin/bash`) and add comments (`#`) explaining what each step does:
```bash
#!/bin/bash
# Description: Example script for mapping reads
```

Make a script executable and run it:
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
