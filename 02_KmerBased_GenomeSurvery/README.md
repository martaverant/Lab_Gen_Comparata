# 02_KmerBased_GenomeSurvey

This module covers short-read data setup, quality control of raw Illumina paired-end sequencing reads (*Anopheles stephensi*), read trimming with Trimmomatic, and k-mer distribution analysis using KAT and GenomeScope 2.0.

---

## 1. Data Access & Symbolic Links (`ln -s`)

Instead of downloading the raw sequencing reads from the internet, we used the pre-downloaded files provided on the university server.

Raw FASTQ files are large (several gigabytes). To save disk space, we created shortcuts (symbolic links) to the shared data directory instead of copying the actual files:

```bash
# Create symbolic links to raw paired-end reads
ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/00_data/00_reads/SRR11672503_1.fastq.gz .
ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/00_data/00_reads/SRR11672503_2.fastq.gz .
```

---

## 2. Quality Control on Raw Reads (FastQC & MultiQC)

Before performing downstream analyses, we evaluate base quality, adapter contamination, and duplication levels across raw sequencing reads.

### FastQC Reports
We retrieved and evaluated the initial FastQC HTML reports for raw reads:

```bash
# Copy pre-computed FastQC outputs from the shared directory
cp /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/02_KmerBased_GenomeSurvey/SRR11672503_1_fastqc.html .
cp /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/02_KmerBased_GenomeSurvey/SRR11672503_1_fastqc.zip .
cp /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/02_KmerBased_GenomeSurvey/SRR11672503_2_fastqc.html .
cp /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/02_KmerBased_GenomeSurvey/SRR11672503_2_fastqc.zip .

# Track and commit FastQC HTML reports in Git
git add SRR11672503_1_fastqc.html SRR11672503_2_fastqc.html
git commit -m "QC on raw reads"
git push origin main
```

---

## 3. Read Trimming (Trimmomatic)

We removed low-quality bases and Illumina adapter sequences using a sliding-window approach in Trimmomatic.

```bash
# Activated Conda environment: assembly
conda activate assembly

# Execute Trimmomatic in Paired-End mode (using 8 threads)
trimmomatic PE -threads 8 -phred33 \
  SRR11672503_1.fastq.gz SRR11672503_2.fastq.gz \
  SRR11672503_1_paired.fastq SRR11672503_1_unpaired.fastq \
  SRR11672503_2_paired.fastq SRR11672503_2_unpaired.fastq \
  ILLUMINACLIP:/opt/miniforge3/envs/assembly/share/trimmomatic-0.40-0/adapters/TruSeq3-PE.fa:2:30:10 \
  LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36 2> stats_trimmomatic.log
```

### Key Parameter Breakdown
- `PE`: Paired-end mode.
- `-threads 8 -phred33`: Uses 8 CPU threads and Phred-33 quality encoding.
- **Outputs**: Generates 4 files (`SRR11672503_1_paired.fastq`, `SRR11672503_1_unpaired.fastq`, `SRR11672503_2_paired.fastq`, `SRR11672503_2_unpaired.fastq`).
- `ILLUMINACLIP`: Removes TruSeq3 paired-end adapters.
- `SLIDINGWINDOW:4:15`: Trims when average Phred quality drops below 15 across a 4-bp window.
- `MINLEN:36`: Discards reads shorter than 36 bp after trimming.

---

## 4. Post-Trimming Quality Control

After trimming, we run FastQC again on the cleaned paired reads to confirm that low-quality bases and adapters were successfully removed:

```bash
fastqc SRR11672503_1_paired.fastq SRR11672503_2_paired.fastq
```

---

## 5. K-mer Analysis (KAT & GenomeScope 2.0)

K-mer counting decomposes reads into substrings of length $k$ to estimate genome size, heterozygosity, and repeat content prior to assembly.

```bash
# Switch to KAT Conda environment
conda activate kat

# Calculate k-mer frequency histogram (k=27) on trimmed paired reads
kat hist -t 8 -m 27 -o anoste_27 SRR11672503_1_paired.fastq SRR11672503_2_paired.fastq
```

### GenomeScope 2.0 Profiling Steps
1. Open the resulting `anoste_27` histogram file and remove any comment lines at the beginning.
2. Upload the cleaned histogram file to [GenomeScope 2.0](http://qb.cshl.edu/genomescope/genomescope2.0/).
3. Extract the estimated genome size parameter (`len`), required for draft genome assembly with `wtdbg2` in Module 03.

---

## 6. Summary of Workflow Steps
1. **Data Access**: Created symbolic links to raw paired-end reads (`SRR11672503_1.fastq.gz`, `SRR11672503_2.fastq.gz`) from the shared directory.
2. **Quality Control**: Analyzed raw read quality using FastQC reports and tracked HTML outputs in GitHub.
3. **Trimming**: Filtered adapters and low-quality bases via `trimmomatic PE`, yielding high-quality paired reads.
4. **Post-Trimming Inspection**: Verified read quality improvement with FastQC on trimmed reads.
5. **K-mer Histogram**: Built the $k=27$ k-mer frequency spectrum with `kat hist` for downstream GenomeScope 2.0 genome size estimation.
