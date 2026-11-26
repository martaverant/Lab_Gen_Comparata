# Laboratorio di Genomica Comparata — *Anopheles stephensi*

This repository documents the bioinformatics workflow and laboratory notebook for the **Comparative Genomics Laboratory** (*Laboratorio di Genomica Comparata*) at the University of Bologna. 

The project covers the complete genomics pipeline for the malaria vector ***Anopheles stephensi***, starting from raw sequencing quality control and *k*-mer genome profiling to chromosome-level *de novo* genome assembly, structural gene annotation, and comparative genomics across Diptera species.


## Repository Structure

* **[`00_Bash/`](./00_Bash/)**: Introduction to Unix/Bash commands, server navigation, file manipulation, and `tmux` session management.
* **[`00_data/`](./00_data/)**: Metadata (`diptera_dataset.tsv`) and download pipeline (`download_dataset.sh`) for reference genomes and GFF3 annotations of selected Diptera species from NCBI Datasets.
* **[`01_ComputerEnv_ProjectWorkflow/`](./01_ComputerEnv_ProjectWorkflow/)**: Setup of Conda environments, Git/GitHub version control workflow, symbolic links (`ln -s`), and reproducible research guidelines.
* **[`02_KmerBased_GenomeSurvey/`](./02_KmerBased_GenomeSurvey/)**: Quality control of Illumina sequencing reads (`FastQC`) and *k*-mer frequency analysis (`KAT`) to estimate genome size, heterozygosity, and repeat content.
* **[`03_GenomeAssembly/`](./03_GenomeAssembly/)**: Long-read *de novo* assembly (`Flye`), assembly polishing (`Medaka`), contaminant screening (`BlobTools`), and reference-guided scaffolding into 3 chromosomes (`RagTag`), evaluated with `QUAST`, `BUSCO`, and `KAT`.
* **[`04_GenomeAnnotation/`](./04_GenomeAnnotation/)**: Repeat masking and evidence-based structural genome annotation (`MAKER` Round 1), quality statistics (`AGAT`, `BUSCO`), and *ab-initio* gene predictor training (`SNAP`) for subsequent rounds.


## Computational Environment & Key Tools

Analyses are executed on the University of Bologna Linux server using isolated **Conda** environments:
* **`base`**: `R` (v4.5.1) and core system libraries
* **`assembly`**: `FastQC`, `Trimmomatic`, `wtdbg2`, `Flye`, `minimap2`, `Samtools`, `mosdepth`, `HyPo`, `RagTag`, `assembly-stats`, `QUAST`, `MAKER`, `SNAP`, `Augustus`, `BLAST`
* **`kat`**: `KAT` (*K*-mer Analysis Toolkit for *k*-mer spectra and assembly completeness plots)
* **`sequence`**: `BUSCO` (`diptera_odb12`), `BlobTools`, `RepeatMasker`, `ncbi-datasets-cli`, `SRA-tools`, `MAFFT`, `BMGE`
* **`GAAS`**: `AGAT` (`agat_sp_statistics.pl`, `agat_sq_repeats_analyzer.pl`) and `GAAS` for GFF3 gene model and repeat statistics
* **`orthofinder`**: `OrthoFinder` (v3.1.0, isolated due to Python version requirements)
* **`tree`**: `IQ-TREE`, `RAxML-NG`, `MAFFT`, `BMGE`, `gotree`, `ete3`, `treeswift`, `CAFE`, `PAML`, `HyPhy`
* **`snakemake`** & **`time`**: Workflow automation and benchmarking utilities

## Data Management Note

In accordance with GitHub file size limits and reproducible research practices, large raw sequencing files (`.fastq.gz`), genome assemblies (`.fasta`/`.fna`), full annotation tracks (`.gff`), and intermediate archives (`.tar.gz`, `.bam`) are stored locally on the university server. This repository tracks all scripts, configuration files (`.ctl`), species tables (`.tsv`), plots (`.png`), and final quality control summaries (`.txt`/`.log`).
