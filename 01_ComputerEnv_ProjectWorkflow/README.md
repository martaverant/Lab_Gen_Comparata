# 01_ComputerEnv_ProjectWorkflow

This document explains the setup of our computing environment, SSH server access, GitHub workflow, and Conda software environments for the *Laboratorio di Genomica Comparata* course.

---

## 1. Remote Server Access (SSH)

We connect to the university teaching server using SSH (Secure Shell):

```bash
ssh studenti.marta.verant@137.204.142.242
```
*Note: Enter the password for your Unibo account when prompted.*

### SSH Key Setup for GitHub
To connect securely to GitHub without typing passwords every time:
aber eigentlich muss ich jedes mal das passwort eingeben nachdem ich didattica eigegebn habe??? deshalb glaube ich haben wir das nicht gemacht?  
1. Generate SSH key pair:
   ```bash
   ssh-keygen -t ed25519 -C "marta.verant@studio.unibo.it"
   ```
2. Copy the public key (`~/.ssh/id_ed25519.pub`) and add it to your GitHub account under **Settings > SSH and GPG keys**.
3. Enable SSH agent on startup by adding this to `~/.bash_profile`:
   ```bash
   eval "$(ssh-agent -s)"
   ssh-add ~/.ssh/id_ed25519
   ```

#### SSH Shortcut (Alias)
To quickly connect to the course server without typing the full SSH command every time, set up a Bash alias in `~/.bash_profile`:

```bash
alias didattica='ssh studenti.marta.verant@137.204.142.242'
```

---

## 2. Git & GitHub Workflow

### Initial Setup
Set your identity for Git:

```bash
git config --global user.name "Marta Verant"
git config --global user.email "marta.verant@studio.unibo.it"
```

### Cloning Repository
Clone the online course repository to the server:

```bash
git clone git@github.com:martaverant/Lab_Gen_Comparata.git
```

### Daily Git Commands

| Command | Description |
| :--- | :--- |
| `git status` | Check which files are modified or untracked |
| `git add <file>` | Add changes to the staging area |
| `git commit -m "message"` | Save a snapshot of staged changes with a note |
| `git push origin main` | Upload local commits to GitHub |
| `git pull` | Fetch and merge latest changes from GitHub |

---

## 3. Conda & Mamba Software Environments

Bioinformatics tools often require different software versions and dependencies. We use Conda/Mamba environments to keep tools isolated and prevent conflicts.

### Essential Conda Commands

| Command | Description |
| :--- | :--- |
| `conda env list` | View all available environments on the server |
| `conda activate <env_name>` | Activate a specific environment |
| `conda deactivate` | Exit the current environment |

### Course Environments
- **base**: Default environment (R).
- **assembly**: Tools for QC, trimming, and assembly (FastQC, Trimmomatic, wtdbg2, Samtools, BUSCO).
  ```bash
  conda activate assembly
  ```
- **kat**: K-mer Analysis Toolkit (KAT).
  ```bash
  conda activate kat
  ```
- **sequence**: Downstream processing and annotation tools (BlobTools, NCBI Datasets).
  ```bash
  conda activate sequence
  ```
- **tree**: Phylogenomics and tree inference tools (OrthoFinder, IQ-TREE).
  ```bash
  conda activate tree
  ```

---

## 4. Keeping Jobs Running with tmux

Long-running bioinformatics processes can stop if the SSH connection breaks. We use `tmux` to keep processes running in the background on the server.

### Basic tmux Workflow

1. **Start a new session:**
   ```bash
   tmux new -s general
   ```

2. **Detach from a session (leave it running in background):**
   Press <kbd>Ctrl</kbd> + <kbd>a</kbd>, then <kbd>d</kbd>.  
   *(Note: On standard tmux setups it is <kbd>Ctrl</kbd> + <kbd>b</kbd>, <kbd>d</kbd>, but our server uses <kbd>Ctrl</kbd> + <kbd>a</kbd>).*

3. **Re-attach to an existing session:**
   ```bash
   tmux a -t general
   ```

4. **List running tmux sessions:**
   ```bash
   tmux ls
   ```

---

## 5. Project Directory Structure

Our project focuses on the comparative genomics of *Anopheles stephensi* (mosquito vector). The repository is organized into the following modules:

- `00_Bash/` : Shell commands and basic scripts.
- `01_ComputerEnv_ProjectWorkflow/` : Computing environment, SSH, Git, Conda.
- `02_KmerBased_GenomeSurvey/` : Raw read QC (FastQC, MultiQC), trimming (Trimmomatic), and K-mer survey (KAT).
- `03_GenomeAssembly/` : Assembly (wtdbg2), polishing (Hypo), contamination check (BlobTools), and scaffolding (RagTag).
- `04_GenomeAnnotation/` : Gene prediction and structural annotation (MAKER).

*(to be continued...)*
