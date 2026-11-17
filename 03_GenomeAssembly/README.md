# 03_GenomeAssembly 

## 00_assembly_raw: Get the raw assembly and do a first quality check (BUSCO).

### 1. De novo Assembly
The raw draft assembly (`Anoste_raw.fasta`) was generated from PacBio long reads (`SRR11672506.fastq.gz`) inside a `tmux` session using the `assembly` Conda environment (with Flye / wtdbg2):

```bash
mkdir -p ~/Lab_Gen_Comparata/03_GenomeAssembly/00_assembly_raw
cd ~/Lab_Gen_Comparata/03_GenomeAssembly/00_assembly_raw

# Link PacBio long reads
ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/00_data/00_reads/SRR11672506.fastq.gz .

# Activate assembly environment
conda activate assembly

# Step 1: Assemble long reads into contig layouts
wtdbg2 -x rs -g 227m -t 8 -i SRR11672506.fastq.gz -fo Anoste_raw

# Step 2: Generate the consensus FASTA sequence from the layout file
wtpoa-cns -t 8 -i Anoste_raw.ctg.lay.gz -fo Anoste_raw.fasta
```
Parameter Breakdown (wtdbg2 & wtpoa-cns)
-x rs: Sequencing technology preset (PacBio RSII / long reads).
-g 227m: Expected haploid genome size (~227 Mbp, estimated in the k-mer survey).
-t 8: Number of CPU threads.
-i: Input file (SRR11672506.fastq.gz for wtdbg2; the generated .ctg.lay.gz layout file for wtpoa-cns).
-fo: Output prefix / output FASTA file (Anoste_raw.fasta).

(Note: Since I missed the 19/10 lession, I copied the pre-assembled Anoste_raw.fasta file to continue with the downstream steps).

### Copy the Raw Assembly File
Copy the pre-assembled `Anoste_raw.fasta` file from a classmate's directory into the working folder:

```bash
cp /home/STUDENTI/virginia.piccinotti/Anoste_raw.fasta ~/Lab_Gen_Comparata/03_GenomeAssembly/00_assembly_raw/
```

### 2. Initial Quality Check (BUSCO)
We evaluate the completeness of the raw assembly by searching for conserved single-copy orthologs using **BUSCO**. We used two lineage databases in class (`diptera_odb12` and `culicidae_odb12`); I used `diptera_odb12` (which covers a broader taxonomic group across all flies/mosquitoes).

```bash
# Activate the Conda environment containing BUSCO
conda activate sequence

# Check command syntax if needed
# busco --help | less

# Set max threads and run BUSCO in genome mode (-m geno)
export NUMEXPR_MAX_THREADS=80
busco -m geno -l $BUSCO/diptera_odb12 -c 8 -o Anoste_raw -i Anoste_raw.fasta
```

Parameter Breakdown
* -m geno: Genome mode (searches for conserved genes directly in the assembly).
* -l $BUSCO/diptera_odb12: Lineage dataset ($BUSCO is a position of the computer (a variable) placeholder).
* -c 8: Uses 8 CPU threads.
* -o Anoste_raw: Output directory name.
* -i Anoste_raw.fasta: Input raw assembly FASTA file.

safe the file BUSCO: 
git add short_summary.specific.diptera_odb12.Anoste_raw.txt
git pull
git commit -m "QC BUSCO on raw assembly" (-m messaggio)
git push

 
## 01_polishing: Map short and long reads (minimap2 + samtools in mapping.sh), calculate coverage (mosdepth), polish the assembly (hypo) and check the quality again (BUSCO & KAT).
Long-read assemblies often contain small base-level errors (especially indels). We polish the raw assembly using **HyPo**, which combines accurate Illumina short reads with PacBio long reads.

### 1. Read Mapping & Creating a Bash Script (`mapping.sh`)

#### Key Concepts & Workflow
* **Read Mapping:** Aligning sequencing reads onto the draft assembly (`Anoste_raw.fasta`) to find where each read originates on the contigs. **HyPo** needs these alignments to detect and correct base errors.
* **SAM vs. BAM:** `minimap2` outputs alignments in **SAM** format (plain text, very large). Using `samtools`, we convert it into **BAM** format (compressed binary, saves disk space), delete the raw `.sam` file, and then **sort** and **index** (`.bai`) the BAM file for fast access.
* **Bash Scripting (`mapping.sh`):** A `.sh` script is a text file containing a list of commands that the computer executes automatically one after another. We create it with `nano` and inspect it with `cat` or `less` (see also `00_Bash`):
  * **Shebang (`#!/bin/bash`):** Placed at the very top of the script so the system knows to execute the commands using Bash. Lines starting with `#` are comments and are ignored.
  * **Variables (`$1`, `$2`, ...):** Placeholders for command-line arguments (`CPU=$1`, `INPUT=$2`, etc.) so the script stays reusable for any dataset.
  * **Output Redirection (`-o`, `>`, `>>`):** `-o` specifies an output file name; `>` writes output into a file (overwriting existing content), while `>>` appends output to the end of an existing file.
* **Symbolic Links (`ln -s`):** Creates shortcuts to large files without duplicating them (cyan = valid link, red = broken link, pink = compressed/image file). Paths can be **relative** (`../`) or **absolute** (`/home/...`).

#### Setup & Script Execution
Create the working directory, link the required input files, and write a reusable Bash script (`mapping.sh`) to align both short and long reads against `Anoste_raw.fasta` using `minimap2` and `samtools`:

```bash
mkdir -p ~/Lab_Gen_Comparata/03_GenomeAssembly/01_polishing
cd ~/Lab_Gen_Comparata/03_GenomeAssembly/01_polishing

# Link raw assembly, short reads (relative paths), and long reads (absolute path)
ln -s ../00_assembly_raw/Anoste_raw.fasta .
ln -s ../../02_KmerBased_GenomeSurvey/SRR11672503_1_paired.fastq .
ln -s ../../02_KmerBased_GenomeSurvey/SRR11672503_2_paired.fastq .
ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/00_data/00_reads/SRR11672506.fastq.gz .


Create the script using `nano mapping.sh` (save with `Ctrl + s`, exit with `Ctrl + x`) and verify its content with `cat mapping.sh`, `head mapping.sh`, or `less mapping.sh`:

```bash
#!/bin/bash

# Mapping short reads on assembly
CPU=$1
INPUT=$2
SR_1=$3
SR_2=$4
PREFIX=$5

# Mapping short reads with minimap2 (-ax sr: short reads)
minimap2 -ax sr --MD -t "$CPU" "$INPUT" "$SR_1" "$SR_2" > "$PREFIX".sam

# Conversion SAM into BAM
samtools view -b "$PREFIX".sam > "$PREFIX".bam
rm "$PREFIX".sam

# Sorting and indexing of BAM file
samtools sort -@"$CPU" -o "$PREFIX"_sorted.bam "$PREFIX".bam
samtools index "$PREFIX"_sorted.bam


# Mapping LONG reads (-ax map-pb: long read in one direction)
minimap2 -ax map-pb --MD -t "$CPU" "$INPUT" "$LR" > "$PREFIX"_lr.sam

# conversion SAM into BAM
samtools view -b "$PREFIX"_lr.sam "$PREFIX"_lr.bam
rm "$PREFIX"_lr.sam

# sorting and indexing of BAM file
samtools sort -@"$CPU" -o "$PREFIX"_lr_sorted.bam "$PREFIX"_lr.bam
samtools index "$PREFIX"_lr_sorted.bam
```

Activate the assembly environment and run the script inside the tmux session: 

```bash
conda activate assembly
bash mapping.sh 8 Anoste_raw.fasta SRR11672503_1_paired.fastq SRR11672503_2_paired.fastq Anoste_pol SRR11672506.fastq.gz
```


ok fino qua tutto fattto poi unvollständig... 



### 2. Linking Pre-computed BAM Files & Calculating Coverage (`mosdepth`)

#### Disk Space Issue & Symbolic Links
Because running the mapping script for all reads failed due to insufficient space on the server, we linked the pre-computed sorted `.bam` and `.bai` files (along with Mirko's raw assembly) directly from the practice folder to proceed with polishing:

```bash
# Link pre-computed sorted BAM and BAI files
ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/03_GenomeAssembly/01_polishing/Anoste_raw_pb_sorted.bam .
ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/03_GenomeAssembly/01_polishing/Anoste_raw_pb_sorted.bam.bai .
ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/03_GenomeAssembly/01_polishing/Anoste_raw_sr_sorted.bam .
ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/03_GenomeAssembly/01_polishing/Anoste_raw_sr_sorted.bam.bai .

# Link raw assembly
ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/03_GenomeAssembly/00_assembly_raw/Anoste_raw.fasta .
```
#### Calculating Short-Read Coverage with mosdepth
Before running hypo polishing, we need the average short-read coverage (-c). We compute coverage in 500 bp windows using mosdepth and calculate the mean using awk:

# Calculate coverage in 500 bp windows
```bash
mosdepth -n --fast-mode --by 500 Anoste_PB Anoste_raw_pb_sorted.bam
mosdepth -n --fast-mode --by 500 Anoste_SR Anoste_raw_sr_sorted.bam
```
# Parameter & Tool Breakdown (mosdepth)
*-n: Skips generating the coverage file for every single base (saves disk space and time).
*--fast-mode: Speeds up calculation by semplifing the calculation of coverage.
*--by 500: Divides the genome into windows of 500 bp and calculates the average coverage per window.

zcat + awk: Unzips the regions bed file (zcat), sums up the coverage values of column 4 (sum += $4), counts the lines (count++), and calculates the final average (sum / count).

# Compute the mean coverage across all windows
zcat Anoste_SR.regions.bed.gz | awk '{sum += $4; count++} END {print sum / count}' > Anoste_SR_coverage.txt
zcat Anoste_PB.regions.bed.gz | awk '{sum += $4; count++} END {print sum / count}' > Anoste_PB_coverage.txt

### 3. Polish the Assembly (`hypo`)

Now that we have the alignments (`.bam`), the calculated coverage, and the draft assembly, we can run **hypo** (Hybrid Polishing) to correct base-level errors and indels.

First, we create a text file (`reads.txt`) listing the paths to the paired short reads (using `echo -e` with `\n` for newlines):

```bash
R1="/home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/02_KmerBased_GenomeSurvey/SRR11672503_1_paired.fastq"
R2="/home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/02_KmerBased_GenomeSurvey/SRR11672503_2_paired.fastq"
echo -e "$R1\n$R2" > reads.txt

# Then we run HyPo and rename the output file:

hypo -d Anoste_raw.fasta -r @reads.txt -s 227054799 -c 136 -b Anoste_raw_sr_sorted.bam -B Anoste_raw_pb_sorted.bam -t 8
```
# Rename output file for clarity
mv hypo_Anoste_raw.fasta Anoste_pol.fasta

Parameter Breakdown (HyPo)
-d: Draft assembly FASTA file (Anoste_raw.fasta).
-r @reads.txt: Path file listing the paired short-read FASTQ files (preceded by @).
-s 227054799: Approximate haploid genome size (~227.05 Mbp from the GenomeScope survey).
-c 136: Mean short-read coverage calculated via mosdepth (136.412).
-b: Sorted BAM file for short reads.
-B: Sorted BAM file for PacBio long reads.
-t 8: Number of CPU threads.


(assembly) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/03_GenomeAssembly/01_polishing$ zcat Anoste_SR.regions.bed.gz | awk '{sum += $4;count++} END {print sum / count}' > Anoste_SR_coverage.txt
(assembly) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/03_GenomeAssembly/01_polishing$ cat Anoste_SR_coverage.txt
soluzione: 136.412
(assembly) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/03_GenomeAssembly/01_polishing$ zcat Anoste_PB.regions.bed.gz | awk '{sum += $4;count++} END {print sum / count}' > Anoste_PB_coverage.txt
(assembly) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/03_GenomeAssembly/01_polishing$ cat Anoste_PB_coverage.txt
soluzione: 102.621
(assembly) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/03_GenomeAssembly/01_polishing$

per lanciare il prossimo script: 
nano polishing.sh 
per aprire una finestra con 
control altro slash o barretta dirtta
control +a o -> muovere tra le due finestre


hypo -d <DRAFT_Contigs> -r @<READS_PATH> -s <APPROXIMATE_GENOMESIZE> -c <SHORT_READSCOVERAGE> -b <SORTED_BAM_SR> -B <SORTED_BAM_PB> -t <NUMBER_THREADS>

hypo -d Anoste_raw.fasta -r @<READS_PATH> -s 227054799 -c 136 -b Anoste_raw_sr_sorted.bam -B Anoste_raw_pb_sorted.bam -t 8  

echo -> stampare il standard output
creare la variabile R1 e R2 nel file delle reads
 R1="/home/PERSONALE/mirko.martini3/Lab_
                                                                                                                                       │CompGeno/00_practice/02_KmerBased_GenomeSurvey/SRR11672503_1_paired.fastq"
                                                                                                                                       │(base) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/03_GenomeAssembly/01_polishing$ R2="/home/PERSONALE/mirko.martini3/Lab_
                                                                                                                                       │CompGeno/00_practice/02_KmerBased_GenomeSurvey/SRR11672503_2_paired.fastq"
                                                                                                                                       │(base) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/03_GenomeAssembly/01_polishing$ echo $R1
                                                                                                                                       │/home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/02_KmerBased_GenomeSurvey/SRR11672503_1_paired.fastq
                                                                                                                                       │(base) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/03_GenomeAssembly/01_polishing$

echo -e "$R1\n$R2" > reads.txt
-e (serve a scrivere il \n) \n = a capo/new line

hypo -d Anoste_raw.fasta -r @reads.txt -s 227054799 -c 136 -b Anoste_raw_sr_sorted.bam -B Anoste_raw_pb_sorted.bam -t 8

tirato fuori dallo script e poi lanciarlo 

lo script dobbiamo scrivere che una persona che non ha fatto il corso potrebbe fare la stessa cosa come io 
-> mettere dentro tutto quello che pensiamo sia necessario e utile, è un script personale che dobbiamo fare anche nel futuro, scrivere tutto quello che serve

10/11/2026
tuttoo andato per sbaglio ho chiuso la finestra 
tutta la parte manca di quantitativo e qualitativo 
busco miglioramento qualitativo perché aumenta i numeri di geni -> cambia le basi 
poi bo non mi ricordo recuperarlo dalla registrazione audio 


tumx ls
tmux a -t mapping (ricordatelo!!!)
sarebbe anche utile scrivere un foglie con tutti i comandi che sono neccessario per muovere dagli screen ecc., per chiudere i screen (contol a + x penso non sono sicura) per i commandi che sono importanti, prima o poi li dobbiamo sapere!!  

rinomina:  mv hypo_Anoste_raw.fasta Anoste_pol.fasta
conda activate sequence (perché busco lavora con sequence) 
export NUMEXPR_MAX_THREADS=<CPU_NUMBER>
export NUMEXPR_MAX_THREADS=80
busco -m <MODE> -l <LINEAGE> -c <CPU_NUMBER> -o <OUTPUT_NAME> -i <INPUT>
busco -m geno -l $BUSCO/diptera_odb12 -c 9 -o Anoste_pol -i Anoste_pol.fasta (busco crea una cartella nel output) 
non è andato perché non è andato 
diversi programmi non infastidito come viene formatato un file fasta
one line (header fasta, header fasta) 
multi line fasta (forzatamente il programma va da capo) più leggibile
se la riga è troppo lunga non funziona 
raw.fasta va a capo e il nuovo file non va da capo 

come lo spezzettiamo? 
fold -w <n_characters> <input.fasta> > <output.fasta>
fold -w 80 Anoste_pol.fasta > Anoste_pol_multi.fasta
less -S Anoste_pol_multi.fasta per controllare 
less -S Anoste_pol.fasta (q per uscire)
rm Anoste_pol.fasta
mv Anoste_pol_multi.fasta Anoste_pol.fasta
rm -r Anoste_pol (rimuovere la cartella)
busco -m geno -l $BUSCO/diptera_odb12 -c 9 -o Anoste_pol -i Anoste_pol.fasta

kat (control qualitativo) 
conda activate kat
per creare i paths:
ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/02_KmerBased_GenomeSurvey/SRR11672503_1_paired.fastq .
ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/02_KmerBased_GenomeSurvey/SRR11672503_2_paired.fastq .
 
kat comp -t <NUM_THREADS> -o <OUTPUT_PREFIX> '<FASTQ>...' <ASSEMBLY>
kat comp -t 9 -o Anoste_pol_kat 'SRR11672503_1_paired.fastq SRR11672503_2_paired.fastq' Anoste_pol.fasta
 
Anoste_pol_kat-main.mx.spectra-cn.png (inserire l'immagine kat) 

## 02_contaminants: Find contaminants (BlobTools) and remove non-Arthropoda contigs using grep and awk.

genome assembly 02
identificare le contaminazioni e eliminarle
re mapping -> vedi script del mapping
nuovo segno la barretta verticale | -> concatenazione dei comandi 
usare l'output per l'input (?giusto?) 
mapping 2 passaggio sam in bam, rimosso il sam 
possiamo farlo in un unico passaggio del file contemporaneo
non crea un file intermedio = appiccicare vari comandi insieme, uno al altro 
il mapping non facciamo (abbiamo gia fatto) 

annotazione tassonomica (specie, classe di geni, ecc) 
posso creare la database structure, come e quale rimuovere?, questi commandi vanno anche messi nel quaderno di laboratorio!!!, una persona deve essere informata, noi non stiamo lanciando, ma questi commandi sono stati lanciati da Mirko -> li prendiamo da lui, sempre deve essere cosi tutto deve essere riconoscibile, provare a completarli!!! 
minimap2, de essere completato da re-mapping non con quelli originali, è simile allo script ma non è uguali, chiedere a Mikro se abbiamo dubbio anche per mail


minimap2 --secondary=no --MD -ax sr -t <NUMBER_CORE> <ASSEMBLY> <FASTQ_R1> <FASTQ_R2> | samtools view -Sb - > <OUT_BAMFILE>.bam
samtools sort -@<n_cores> -o <OUT_SORTED_BAMFILE>_sorted.bam Anoste_pol.bam
rm Anoste_pol.bam
samtools index Anoste_pol_sorted.bam

blastn
blastn -query <ASSEMBLY> -db <PATH/TO/nt/> -outfmt '6 qseqid staxids bitscore std sscinames sskingdoms stitle' -max_target_seqs 25 -max_hsps 1 -num_threads 25 -evalue 1e-25 -out <OUTFILE>

non sappiamo questo: -db <PATH/TO/nt/> -> ricordarlo prossima settimana di dirci!!! 

per Blobtools: 
nuova caretlla: 
mkdir 02_contaminants
cd 02_contaminants/

blobtools create -i <ASSEMBLY> -b <MAPPING_FILE> -t <BLASTN_FILE> -o <OUTPUT_PREFIX>
blobtools view -i <JSON_FILE> -o <OUTPUT_PREFIX>
blobtools plot -i <JSON_FILE> -o <OUTPUT_PREFIX>

file molto grandi -> fare il link dei input (non abbiamo Mapping, blastn) 
ln -s ../01_polishing/Anoste_pol.fasta (link del assembly)
ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/03_GenomeAssembly/02_contaminants/Anoste_pol_sorted.bam .
ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/03_GenomeAssembly/02_contaminants/Anoste_pol_sorted.bam.bai .
ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/03_GenomeAssembly/02_contaminants/Anoste_blast.tsv .

blob -> sequence (importqante sempre verificare che sei nella envirnment giusta) 
blobtools create -i Anoste_pol.fasta -b Anoste_pol_sorted.bam -t Anoste_blast.tsv -o Anoste_blob

mentre guardiamo il fle busco: 
cd Lab_Gen_Comparata/03_GenomeAssembly/01_polishing
cd Anoste_pol
cat short_summary.specific.diptera_odb12.Anoste_pol.txt
ma accanto anche quello raw
~/Lab_Gen_Comparata/03_GenomeAssembly/00_assembly_raw$ cd Anoste_raw/
 cat short_summary.specific.diptera_odb12.Anoste_raw.txt
 
-> nel polished ho più geni completi (Complete and single-copy BUSCOs) -> il mio polish ha migliorato? si
miglioramento qualitativo 
Assembly statistics = sono le stesse 
ific.diptera_odb12.Anoste_raw.txt

possiamo fare una tabella non i migloramenti: 

Anoste_raw:
# BUSCO version is: 6.0.0
# The lineage dataset is: diptera_odb12 (Creation date: 2025-07-01, number of genomes: 76, number of BUSCOs: 5067)
# Summarized benchmarking in BUSCO notation for file /home/STUDENTI/marta.verant/Lab_Gen_Comparata/03_GenomeAssembly/00_assembly_raw/Anoste_raw.fasta
# BUSCO was run in mode: euk_genome_min
# Gene predictor used: miniprot

        ***** Results: *****

        C:96.0%[S:95.2%,D:0.7%],F:0.6%,M:3.5%,n:5067,E:7.0%
        4863    Complete BUSCOs (C)     (of which 339 contain internal stop codons)
        4825    Complete and single-copy BUSCOs (S)
        38      Complete and duplicated BUSCOs (D)
        29      Fragmented BUSCOs (F)
        175     Missing BUSCOs (M)
        5067    Total BUSCO groups searched

Assembly Statistics:
        850     Number of scaffolds
        850     Number of contigs
        224097419       Total length
        0.000%  Percent gaps
        20 Mbp  Scaffold N50
        20 Mbp  Contigs N50


Dependencies and versions:
        hmmsearch: 3.4
        bbtools: None
        miniprot_index: 0.18-r281
        miniprot_align: 0.18-r281

Anoste_pol: 
# BUSCO version is: 6.0.0
# The lineage dataset is: diptera_odb12 (Creation date: 2025-07-01, number of genomes: 76, number of BUSCOs: 5067)
# Summarized benchmarking in BUSCO notation for file /home/STUDENTI/marta.verant/Lab_Gen_Comparata/03_GenomeAssembly/01_polishing/Anoste_pol.fasta
# BUSCO was run in mode: euk_genome_min
# Gene predictor used: miniprot

        ***** Results: *****

        C:96.5%[S:95.8%,D:0.7%],F:0.6%,M:2.9%,n:5067,E:7.4%
        4891    Complete BUSCOs (C)     (of which 361 contain internal stop codons)
        4854    Complete and single-copy BUSCOs (S)
        37      Complete and duplicated BUSCOs (D)
        29      Fragmented BUSCOs (F)
        147     Missing BUSCOs (M)
        5067    Total BUSCO groups searched

Assembly Statistics:
        849     Number of scaffolds
        849     Number of contigs
        224753923       Total length
        0.000%  Percent gaps
        20 Mbp  Scaffold N50
        20 Mbp  Contigs N50


Dependencies and versions:
        hmmsearch: 3.4
        bbtools: None
        miniprot_index: 0.18-r281
        miniprot_align: 0.18-r281
-> creare una tabellina con miglioramenti 

## 03_scaffolding: Build chromosome-level scaffolds using a reference genome (RagTag).

comandi importanti, sapera la sintasse base
perché e anche deve fare le cose corretti. e controllare le cose se sono giuste

grep: è il control + f, cercare
esempio grep ">ctg" Anoste_pol.fasta -> "parole che voglio cercare" poi file dove lo voglio cercare
grep ">ctg" Anoste_pol.fasta | wc -l (wc=word count) e -l? (non mi ricorda)
grep --help |less = lettura interattiva del manuale (uscire con la q)
esempio -n (in quale riga lo trovo)
anche -c (la stessa cosa con la pipe (|wc -l)


awk
sed cerca e sostituisci

:~/Lab_Gen_Comparata/03_GenomeAssembly/02_contaminants$ ls
 Anoste_blob.blobDB.json (è uscito questo file) 

blobtools view -i Anoste_blob.blobDB.json -o Anoste
blobtools plot -i Anoste_blob.blobDB.json -o Anoste
(2 file .png e due file .txt)
 salvarli (git add, git pull, git commit, git push) 

 You can download and inspect .png files directly. A common way to observe the table file, instead, is:




awk '{ if ((NR>1)&&($0~/^>/)) { printf("\n%s", $0); } else if (NR==1) { printf("%s", $0); } else { printf("\t%s", $0); } }' in.fa | grep -w -v -Ff patterns.txt - | tr "\t" "\n" > out.fa
#The first part ensures the FASTA is in one-line form and reformats the file, obtaining">header\tsequence". This makes it easier to process the file with grep. Then it filters out everything in the patterns file, outputting a one-line FASTA file with only the desired contigs.

 trasforma un fasta la struttura header fasta viene persa. la struttura diventa una nuova tabella. ci è utile grep funziona a righe e non a colonne
 -> passaggio intermedio per utilizzare grep

 With:

-w #ensures it matches the exact name
-v #invert the recognised pattern
-Ff #compares directly to file
'-' #use stdin as input from the previous command (placeholder)
To use it, we need to create a file listing all contaminated contigs. To do so, we need to decide which parameters to use to filter our blobDB_table.

tr = trasformazione \t \n ->nuova line e ??? bohh

creaiamo il file pattern prima: 
uttilizzando grep 

grep -v '^#' Anoste_blobDB_table.txt | head
togliere header
trovare tutti i contings che non sono artropoda:
grep -v '^#' Anoste.Anoste_blob.blobDB.table.txt | awk '$6 != "Arthropoda"' | head
grep -v '^#' Anoste.Anoste_blob.blobDB.table.txt | awk '$6 != "Arthropoda" {print $1}' | head
grep -v '^#' Anoste.Anoste_blob.blobDB.table.txt | awk '$6 != "Arthropoda" {print $1}' > contaminants_contings.txt


adesso usiamo il awk: 
awk '{ if ((NR>1)&&($0~/^>/)) { printf("\n%s", $0); } else if (NR==1) { printf("%s", $0); } else { printf("\t%s", $0); } }' in.fa | grep -w -v -Ff patterns.txt - | tr "\t" "\n" > out.fa

awk '{ if ((NR>1)&&($0~/^>/)) { printf("\n%s", $0); } else if (NR==1) { printf("%s", $0); } else { printf("\t%s", $0); } }' Anoste_pol.fasta | grep -w -v -Ff contaminants_contings.txt - | tr "\t" "\n" > Anoste_pol_nocont.fa

abbiamo creato il nostro assembly senza contaminazioni

Reference-based scaffolding:
ultimo comando per completare il assembly: 
mkdir 03_scaffolding
ln -s ../02_contaminants/Anoste_pol_nocont.fa
ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/00_data/01_Anoste_reference/GCF_013141755.1_UCI_ANSTEP_V1.0_genomic.fna . 
-> da ncbi (con assession (??) number 013141755)

conda activate assembly
ragtag.py correct -t 10 <REFERENCE_GENOME> <DRAFT_GENOME> (t=quanti CPU possiamo uttilizzare)
ragtag.py correct -t 10 GCF_013141755.1_UCI_ANSTEP_V1.0_genomic.fna Anoste_pol_nocont.fa
ragtag.py scaffold -C -t 20 -o <OUTPUT_DIR> <REFERENCE_GENOME> <CORRECTED_DRAFTGENOME>
ragtag.py scaffold -C -t 10 -o Anoste_chr GCF_013141755.1_UCI_ANSTEP_V1.0_genomic.fna ragtag_output/ragtag.correct.fasta

we do a control of quality: 

grep ">" Anoste_chr/ragtag.scaffold.fasta | head
nano chromocomes.txt (file of pattern)
>NC_050201.1_RagTag
>NC_050202.1_RagTag
>NC_050203.1_RagTag

we use only that 3 because of interest (these are the true chromosomes)

grep -A1 -Ff Anoste_chr/chromocomes.txt Anoste_chr/ragtag.scaffold.fasta > Anoste_chr.fasta

è molto poco heterozigosi, 80% è homozigosi (vedi kat, anche quello è un controllo di qualità)

martedi si inizia a lavorare a conto suo ognun olavora in autonomia sui propri dati, fare la tabella!! come spiegato!!
formato e output ecc. non sono suggerimenti, sono obbligatori 
tabellina per settimana prossima per martedi !! IMPORTANTE

->>> dovete lanciare il busco su livello scaffold 
lanciate busco su Anoste chromosome level 
busco sicoramente su tutti i livelli 
anche sul decontaminato 
4 -> busco (raw, polish, decontaminato, scaffold) 


