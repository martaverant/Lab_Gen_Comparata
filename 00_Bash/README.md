# 00_Bash: Command Line Notes

This document contains basic Bash commands and notes for the *Laboratorio di Genomica Comparata* course.

---

## 1. Basic Navigation & File Operations

| Command | Description | Example |
| :--- | :--- | :--- |
| `pwd` | Show current working directory | `pwd` |
| `ls -lh` | List files with human-readable file sizes | `ls -lh` |
| `cd <dir>` | Change directory | `cd ~/Lab_Gen_Comparata` |
| `mkdir -p <dir>` | Create directory (and subdirectories) | `mkdir -p project/{data,scripts}` |
| `cp <src> <dst>` | Copy a file or directory (`-r` for directories) | `cp file.txt backup/` |
| `mv <src> <dst>` | Move or rename a file | `mv old.txt new.txt` |
| `rm <file>` | Remove a file (use with caution) | `rm temp.txt` |
| `rm -r <dir>` | Remove a directory and its content | `rm -r old_folder/` |

---

## 2. Reading & Inspecting Files

| Command | Description | Example |
| :--- | :--- | :--- |
| `cat <file>` | Display full file content | `cat file.txt` |
| `less <file>` | Page through large files (`q` to quit) | `less sequences.fastq` |
| `head -n 10 <file>` | Show first 10 lines of a file | `head -n 10 genome.fasta` |
| `tail -n 10 <file>` | Show last 10 lines of a file | `tail -n 10 genome.fasta` |
| `wc -l <file>` | Count total lines in a file | `wc -l reads.fastq` |
| `nano <file>` | Open terminal text editor | `nano script.sh` |

---

## 3. Redirection & Pipes

* `>` : Redirect standard output to a file (overwrites existing content).
* `>>` : Append standard output to an existing file.
* `2>` : Redirect error messages (stderr) to a file.
* `|` (Pipe) : Send the output of one command as input to the next command.

**Example:**
```bash
grep "^>" input.fasta | wc -l > sequence_count.txt
```

## 4. Text Processing Tools
* Grep (Search for text patterns):
 * grep "^>" genome.fasta        # Find all FASTA header lines
 * grep -c "^>" genome.fasta     # Count matching lines
* Awk (Column extraction and processing):
 * awk '{print \$1}' table.tsv    # Print first column 
* Sed (Search and replace text):
 * sed 's/old/new/g' file.txt   # Replace 'old' with 'new'

---

## 5. File Permissions & Running Scripts
Change permissions to make a script executable and run it:

* chmod +x script.sh    # Make script executable
* ./script.sh           # Execute script
