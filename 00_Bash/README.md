# 00_Bash: Command Line Notes

This document contains basic Bash commands and notes for the *Laboratorio di Genomica Comparata* course.

---

## 1. Basic Navigation & File Operations

| Command | Description | Example |
| :--- | :--- | :--- |
| `pwd` | Show your current folder (working directory) | `pwd` |
| `ls -lh` | List files with easy-to-read file sizess | `ls -lh` |
| `cd <dir>` | Change directory | `cd ~/Lab_Gen_Comparata` |
| `mkdir -p <dir>` | Create directory (and subdirectories) | `mkdir -p project/{data,scripts}` |
| `cp <src> <dst>` | Copy a file or directory (`-r` for directories) | `cp file.txt backup/` |
| `mv <src> <dst>` | Move or rename a file | `mv old.txt new.txt` |
| `rm <file>` | Delete a file | `rm temp.txt` |
| `rm -r <dir>` | Delete a folder and everything inside it | `rm -r old_folder/` |

---

## 2. Reading & Inspecting Files

| Command | Description | Example |
| :--- | :--- | :--- |
| `cat <file>` | Display full file content | `cat file.txt` |
| `less <file>` | Open a file to scroll through (press `q` to exit) | `less sequences.fastq` |
| `head -n 10 <file>` | Show first 10 lines of a file | `head -n 10 genome.fasta` |
| `tail -n 10 <file>` | Show last 10 lines of a file | `tail -n 10 genome.fasta` |
| `wc -l <file>` | Count total lines in a file | `wc -l reads.fastq` |
| `nano <file>` | Open a simple text editor in the terminal | `nano script.sh` |

---

## 3. Redirection & Pipes

* `>`  : Save output to a file (overwrites existing text).
* `>>` : Add output to the end of an existing file without deleting its contents.
* `2>` : Save error messages to a file.
* `|` (Pipe) : Pass the output of one command as input into another command.

**Example: Count how many sequences are in a FASTA file and save the total to a text file::**
```bash
grep "^>" input.fasta | wc -l > sequence_count.txt
```

## 4. Text Processing Tools
* Grep (Search for text patterns):
 * grep "^>" genome.fasta        # Find all FASTA header lines
 * grep -c "^>" genome.fasta     # Count how many FASTA header lines exist.
* Awk (Column extraction and processing):
 * awk '{print \$1}' table.tsv    # Print only the first column of a table.
* Sed (Search and replace text):
 * sed 's/old/new/g' file.txt   # Replace every occurrence of "old" with "new".

---

## 5. File Permissions & Running Scripts
Change permissions to make a script executable and run it:

* chmod +x script.sh    # Make script executable
* ./script.sh           # Execute script

## 6. Essential Keyboard Shortcuts
`Tab` (Auto-completion):  
Type the first few letters of a command, file, or folder name and press Tab. Bash automatically completes the name. Press Tab twice to see all matching options.  

`Up / Down Arrows` (Command history):  
Press Up Arrow to scroll back through previously used commands. Press Down Arrow to go forward again.  

`Home` / `Ctrl + a`:  
Move cursor to the beginning of the current line.  

`End` / `Ctrl + e`:  
Move cursor to the end of the current line.  

`Ctrl + c` (Cancel):  
Stops and cancels a running command immediately.  

`Ctrl + l` or `clear` (Clear screen):  
Clears the terminal window to keep your workspace clean.
