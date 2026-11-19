# 01_ComputerEnv_ProjectWorkflow

This document explains the setup of our computing environment, SSH server access, GitHub workflow, Conda/Mamba software environments, and background sessions (`tmux`) for the *Laboratorio di Genomica Comparata* course.

First, connect to the server via [Guacamole](https://rlab.unibo.it/) using your university credentials, or open **Git Bash** on Windows for local command-line work.

---

## 1. Connecting to the Remote Server (SSH)

We access the university server using the SSH command line:

```bash
ssh studenti.marta.verant@137.204.142.242
```
*Note: Enter the password for your Unibo account when prompted (confirm `yes` on the first connection if asked about the host fingerprint).*

### Setting Up an SSH Key for GitHub
An SSH key lets you connect your server account to GitHub securely without typing your password every time.

1. **Create a new SSH key:**
   ```bash
   ssh-keygen -t ed25519 -C "marta.verant@studio.unibo.it"
   ```
   *Press `Enter` to accept the default location (`~/.ssh/id_ed25519`).*

2. **Add the key to GitHub:**  
   Display and copy the entire contents of `~/.ssh/id_ed25519.pub` (never share your private key `id_ed25519`):
   ```bash
   cat ~/.ssh/id_ed25519.pub
   ```
   Paste it into **GitHub > Settings > SSH and GPG keys > New SSH key**, then test the connection:
   ```bash
   ssh -T git@github.com
   ```

3. **Automatically load your SSH key when you log in:**  
   Add these two lines to the end of your `~/.bash_profile` (or `~/.bashrc`):
   ```bash
   eval "$(ssh-agent -s)"
   ssh-add ~/.ssh/id_ed25519
   ```

### Creating an SSH Shortcut (Alias)
Instead of typing the full IP address every time, create a shortcut command by adding this line to `~/.bash_profile`:

```bash
alias didattica='ssh studenti.marta.verant@137.204.142.242'
```
*Now you can simply type `didattica` in your terminal to log in. (Note: Do not use personal aliases inside project scripts so your code remains portable.)*

---

## 2. Git & GitHub Workflow

### First-Time Configuration
Set your name and email so Git tags your commits correctly:

```bash
git config --global user.name "Marta Verant"
git config --global user.email "marta.verant@studio.unibo.it"
```

### Download (Clone) the Repository
Download a copy of the course repository to your server account:

```bash
git clone git@github.com:martaverant/Lab_Gen_Comparata.git
```

### Essential Git Commands

| Command | Description |
| :--- | :--- |
| `git status` | Shows modified, deleted, or untracked files |
| `git add <file>` | Prepares modified or new files to be saved (stages changes) |
| `git commit -m "message"` | Saves staged changes locally with a descriptive note |
| `git pull` | Downloads and merges the latest changes from GitHub to your server |
| `git push` | Sends your saved local commits to GitHub (e.g., `git push origin main`) |
| `git log --oneline` | Displays a compact history of previous commits |

*Tip: Use a `.gitignore` file to prevent large sequencing or assembly files (e.g., `.fastq`, `.fasta`, `.bam`) from being uploaded to GitHub.*

---

## 3. Managing Software with Conda & Mamba

Bioinformatics tools require specific settings and Python/library versions. Conda (and `mamba`, a faster drop-in replacement where available) keeps these tools separated in isolated environments so they do not conflict with each other.

### Basic Conda Commands

| Command | Description |
| :--- | :--- |
| `conda env list` | Lists all installed software environments |
| `conda activate <env_name>` | Switches to a specific tool environment |
| `conda deactivate` | Exits the current environment back to `base` |

### Course Environments
- **base**: Default environment with basic libraries and R.
- **assembly**: Quality control, read cleaning, and assembly tools (`FastQC`, `Trimmomatic`, `wtdbg2`, `Samtools`, `BUSCO`):
  ```bash
  conda activate assembly
  ```
- **kat**: K-mer Analysis Toolkit (`KAT`) for inspecting raw DNA sequencing data:
  ```bash
  conda activate kat
  ```
- **sequence**: Downstream filtering and contamination check tools (`BlobTools`, `NCBI Datasets`):
  ```bash
  conda activate sequence
  ```
- **tree**: Evolutionary and phylogenomic analysis tools (`OrthoFinder`, `IQ-TREE`):
  ```bash
  conda activate tree
  ```

---

## 4. Keeping Tasks Running with `tmux`

Long-running bioinformatics processes can stop if the SSH connection breaks. `tmux` creates background sessions that keep your jobs running on the server even when you log off.

### Step-by-Step `tmux` Workflow

1. **Start a new background session:**
   ```bash
   tmux new -s general
   ```

2. **Detach from a session (leave it running in the background):**  
   Press <kbd>Ctrl</kbd> + <kbd>a</kbd>, then <kbd>d</kbd>.  
   *(Note: On standard `tmux` setups it is <kbd>Ctrl</kbd> + <kbd>b</kbd>, <kbd>d</kbd>, but our server uses <kbd>Ctrl</kbd> + <kbd>a</kbd>.)*

3. **See all active sessions:**
   ```bash
   tmux ls
   ```

4. **Reconnect to an existing session:**
   ```bash
   tmux a -t general
   ```

---

## 5. Repository Directory Layout

This project contains code and pipelines for analyzing the *Anopheles stephensi* mosquito genome. Directories are organized by step:

- `00_Bash/` : General utility shell scripts and command-line notes.
- `01_ComputerEnv_ProjectWorkflow/` : Guides for environment setup, Git, and server instructions.
- `02_KmerBased_GenomeSurvey/` : Read quality control (`FastQC`, `MultiQC`), adapter removal (`Trimmomatic`), and genome size estimation (`KAT`).

*(to be continued...)*# 01_ComputerEnv_ProjectWorkflow
