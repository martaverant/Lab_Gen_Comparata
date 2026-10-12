# 01_ComputerEnv_ProjectWorkflow

This document explains the setup of our computing environment, SSH server access, GitHub workflow, and Conda software environments for the *Laboratorio di Genomica Comparata* course.

---

## 1. Remote Server Access (SSH)

We connect to the university teaching server using SSH (Secure Shell):

```bash
ssh username@ip_address
ssh studenti.marta.verant@137.204.142.242
```
Enter the passwort for Unibo account. 

### SSH Key Setup for GitHub
To connect securely to GitHub without typing passwords every time:

1. Generate SSH key pair:
   ```bash
   ssh-keygen -t ed25519 -C "your_email@example.com"
   ssh-keygen -t ed25519 -C "marta.verant@studenti.unibo.it"
   ```
2. Copy the public key (`~/.ssh/id_ed25519.pub`) and add it to your GitHub account under **Settings > SSH and GPG keys**.
3. Enable SSH agent on startup by adding this to `~/.bash_profile`:
   ```bash
   eval "$(ssh-agent -s)"
   ssh-add ~/.ssh/id_ed25519
   ```

---

## 2. Git & GitHub Workflow

### Initial Setup
Set your identity for Git:

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
git config --global user.name "Marta Verant"
git config --global user.email "marta.verant@studio.unibo.it"
```

### Cloning Repository
Clone the online course repository to the server:

```bash
git clone git@github.com:your-username/Lab_Gen_Comparata.git
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
| `conda activate <env_name>` | Activate a specific environment (e.g. `assembly`, `kat`, `sequence`) |
| `conda deactivate` | Exit the current environment |

`conda activate assembly`  # For FastQC, Trimmomatic, wtdbg2, Samtools  

`conda activate kat`       # For K-mer Analysis Toolkit  

`conda activate sequence`  # For BUSCO, NCBI Datasets, BlobTool  


### Course Environments
- **base**: Default environment (R).
- **assembly**: Tools for QC, trimming, and assembly (FastQC, Trimmomatic, wtdbg2, Samtools, BUSCO).
- **kat**: K-mer Analysis Toolkit (KAT).
- **sequence**: Downstream processing and annotation tools (BlobTools, NCBI Datasets).
- **tree**: Phylogenomics and tree inference tools (OrthoFinder, IQ-TREE).

---

## 4. Project Directory Structure

Our project focuses on the comparative genomics of *Anopheles stephensi* (mosquito vector). The repository is organized into the following modules:

- `00_Bash/` : Shell commands and basic scripts.
- `01_ComputerEnv_ProjectWorkflow/` : Computing environment, SSH, Git, Conda.
- `02_KmerBased_GenomeSurvey/` : Raw read QC (FastQC, MultiQC), trimming (Trimmomatic), and K-mer survey (KAT).
- `03_GenomeAssembly/` : Assembly (wtdbg2), polishing (Hypo), contamination check (BlobTools), and scaffolding (RagTag).
- `04_GenomeAnnotation/` : Gene prediction and structural annotation (MAKER).

to be continued...
