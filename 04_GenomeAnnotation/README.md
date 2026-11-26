# 04_GenomeAnnotation

> **Note on Repository Sync:** All annotation statistics (`AGAT` logs) and `BUSCO` proteome/transcriptome summaries are saved in their folders and uploaded to GitHub. See the [Git & GitHub Workflow Guide](../01_CompEnv_ProjectWorkflow/README.md) for the exact Git commands (`git pull`, `git add`, `git commit`, `git push`).

## Overview & Workflow
After assembling the 3 chromosomes (`Anoste_chr.fasta`), our genome is just a long string of DNA letters (`A, T, C, G`). **Genome annotation** finds where the genes, exons, and introns are located and saves their coordinates in a `.gff` file, along with the predicted protein and transcript sequences (`.fasta`).

Full genome annotation requires a lot of time. We didn't complete the full annotation ourselves; we only started the commands and ended them earlier, and then Mirko gave us the final results.

1. **Repeat Masking (`RepeatMasker` inside MAKER):** Masks repetitive DNA and transposable elements using a custom repeat library (`Anoste_RepeatModeler_library.fa`) so MAKER does not mistake repeats for real genes.
2. **Round 1 — Evidence-Based Annotation (`MAKER`):** Aligns known proteins from closely related Diptera species (*Anopheles stephensi*, *Anopheles gambiae*, and *Drosophila melanogaster*) onto our chromosomes (`protein2genome=1`) to create a first set of gene models (`11,543` proteins).
3. **Round 2 & 3 — *Ab-initio* Gene Prediction (`SNAP` & `Augustus`):** Trains statistical gene-finding models (`SNAP` and `Augustus`) on the best Round 1 gene models to improve gene structures in the next MAKER rounds.
4. **Quality Control (`AGAT` & `BUSCO`):** Summarizes gene and repeat statistics (`AGAT`) and checks the completeness of the predicted proteins and transcripts (`BUSCO` in `-m prot` and `-m tran` modes).

---

## 00_round1: First Annotation Round (Homology-Based with MAKER)

### Setup and Symbolic Links
Create the folders for the iterative annotation rounds and link our chromosome-level assembly, reference proteomes, and custom repeat library:

```bash
mkdir 04_GenomeAnnotation
cd 04_GenomeAnnotation/
mkdir 00_round1
mkdir 01_round2
mkdir 02_round3
cd 00_round1/

# Link chromosome-level genome assembly
ln -s ../../03_GenomeAssembly/03_scaffolding/Anoste_chr.fasta .

# Link homologous protein evidence (Anopheles stephensi, Anopheles gambiae, Drosophila melanogaster)
ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/00_data/02_Annotation/GCF_013141755.1_UCI_ANSTEP_V1.0_protein.faa Anoste.faa
ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/00_data/02_Annotation/GCF_943734735.2_idAnoGambNW_F1_1_protein.faa Anogam.faa
ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/00_data/02_Annotation/GCF_000001215.4_Release_6_plus_ISO1_MT_protein.faa Dromel.faa

# Link custom repeat library (created by Mirko with RepeatModeler)
ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/00_data/02_Annotation/Anoste_RepeatModeler_library.fa .
```

### Generate and Configure MAKER Control Files
Activate the assembly environment, generate the default MAKER control files (maker_opts.ctl, maker_bopts.ctl, maker_exe.ctl), and edit maker_opts.ctl:
```bash
conda activate assembly
maker -CTL
nano maker_opts.ctl
```
### Key settings edited in maker_opts.ctl:
```ini
#-----Genome (these are always required)
genome=Anoste_chr.fasta #genome sequence (fasta file or fasta embeded in GFF3 file)
organism_type=eukaryotic #eukaryotic or prokaryotic. Default is eukaryotic

#-----Re-annotation Using MAKER Derived GFF3
maker_gff= #MAKER derived GFF3 file
est_pass=0 #use ESTs in maker_gff: 1 = yes, 0 = no
altest_pass=0 #use alternate organism ESTs in maker_gff: 1 = yes, 0 = no
protein_pass=0 #use protein alignments in maker_gff: 1 = yes, 0 = no
rm_pass=0 #use repeats in maker_gff: 1 = yes, 0 = no
model_pass=0 #use gene models in maker_gff: 1 = yes, 0 = no
pred_pass=0 #use ab-initio predictions in maker_gff: 1 = yes, 0 = no
other_pass=0 #passthrough anyything else in maker_gff: 1 = yes, 0 = no

#-----EST Evidence (for best results provide a file for at least one)
est= #set of ESTs or assembled mRNA-seq in fasta format
altest= #EST/cDNA sequence file in fasta format from an alternate organism
est_gff= #aligned ESTs or mRNA-seq from an external GFF3 file
altest_gff= #aligned ESTs from a closly relate species in GFF3 format

#-----Protein Homology Evidence (for best results provide a file for at least one)
protein=Anogam.faa,Anoste.faa,Dromel.faa #protein sequence file in fasta format (i.e. from mutiple organisms)
protein_gff=  #aligned protein homology evidence from an external GFF3 file

#-----Repeat Masking (leave values blank to skip repeat masking)
model_org= #select a model organism for DFam masking in RepeatMasker
rmlib=Anoste_RepeatModeler_library.fa #provide an organism specific repeat library in fasta format for RepeatMasker #Fatto library da Mirko
repeat_protein= #provide a fasta file of transposable element proteins for RepeatRunner
rm_gff= #pre-identified repeat elements from an external GFF3 file
prok_rm=0 #forces MAKER to repeatmask prokaryotes (no reason to change this), 1 = yes, 0 = no
softmask=1 #use soft-masking rather than hard-masking in BLAST (i.e. seg and dust filtering)

#-----Gene Prediction
snaphmm= #SNAP HMM file
gmhmm= #GeneMark HMM file
augustus_species= #Augustus gene prediction species model
fgenesh_par_file= #FGENESH parameter file
pred_gff= #ab-initio predictions from an external GFF3 file
model_gff= #annotated gene models from an external GFF3 file (annotation pass-through)
run_evm=0 #run EvidenceModeler, 1 = yes, 0 = no
est2genome=0 #infer gene predictions directly from ESTs, 1 = yes, 0 = no
protein2genome=1 #infer predictions from protein homology, 1 = yes, 0 = no
trna=0 #find tRNAs with tRNAscan, 1 = yes, 0 = no
snoscan_rrna= #rRNA file to have Snoscan find snoRNAs
snoscan_meth= #-O-methylation site fileto have Snoscan find snoRNAs
unmask=0 #also run ab-initio prediction programs on unmasked sequence, 1 = yes, 0 = no
allow_overlap= #allowed gene overlap fraction (value from 0 to 1, blank for default)

#-----Other Annotation Feature Types (features MAKER doesn't recognize)
other_gff= #extra features to pass-through to final MAKER generated GFF3 file

#-----External Application Behavior Options
alt_peptide=C #amino acid used to replace non-standard amino acids in BLAST databases
cpus=9 #max number of cpus to use in BLAST and RepeatMasker (not for MPI, leave 1 when using MPI)

#-----MAKER Behavior Options
max_dna_len=100000 #length for dividing up contigs into chunks (increases/decreases memory usage)
min_contig=1 #skip genome contigs below this length (under 10kb are often useless)

pred_flank=200 #flank for extending evidence clusters sent to gene predictors
pred_stats=1 #report AED and QI statistics for all predictions as well as models
AED_threshold=1 #Maximum Annotation Edit Distance allowed (bound by 0 and 1)
min_protein=50 #require at least this many amino acids in predicted proteins
alt_splice=1 #Take extra steps to try and find alternative splicing, 1 = yes, 0 = no
always_complete=0 #extra steps to force start and stop codons, 1 = yes, 0 = no
map_forward=0 #map names and attributes forward from old GFF3 genes, 1 = yes, 0 = no
keep_preds=0 #Concordance threshold to add unsupported gene prediction (bound by 0 and 1)

split_hit=10000 #length for the splitting of hits (expected max intron size for evidence alignments)
min_intron=20 #minimum intron length (used for alignment polishing)
single_exon=0 #consider single exon EST evidence when generating annotations, 1 = yes, 0 = no
single_length=250 #min length required for single exon ESTs if 'single_exon is enabled'
correct_est_fusion=0 #limits use of ESTs in annotation to avoid fusion genes

tries=2 #number of times to try a contig if there is a failure for some reason
clean_try=0 #remove all data from previous run before retrying, 1 = yes, 0 = no
clean_up=0 #removes theVoid directory with individual analysis files, 1 = yes, 0 = no
TMP= #specify a directory other than the system default temporary directory for temporary files
```

### Extract Pre-Computed Round 1 Results and Merge Genome-Wide Files
MAKER processes each chromosome separately inside nested folders (Anoste_rnd1_datastore). After unpacking Mirko's pre-computed archive (maker1.tar.gz), we use fasta_merge and gff3_merge (reading Anoste_rnd1_master_datastore_index.log) to combine all outputs into single genome-wide files:

```bash
# Copy and unpack the pre-computed Round 1 archive (maker1):
cp /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/04_GenomeAnnotation/00_round1/Anoste_rnd1.maker.output/maker1.tar.gz .
tar -z -xf maker1.tar.gz

# Merge chromosome outputs into single genome-wide FASTA and GFF3 files
conda activate assembly
fasta_merge -d Anoste_rnd1_master_datastore_index.log
gff3_merge -d Anoste_rnd1_master_datastore_index.log

# Count predicted proteins (11,543 proteins)
grep -c ">" Anoste_rnd1.all.maker.proteins.fasta
```

#### Explanation of the Output Files
- Anoste_rnd1.all.gff: The full GFF3 annotation file containing the genomic coordinates (chromosome, start, end, strand) of all predicted genes, mRNAs, exons, repeats, and protein alignments, plus AED quality scores.
- Anoste_rnd1.all.maker.proteins.fasta: Amino acid sequences of all 11,543 proteins predicted in Round 1.
- Anoste_rnd1.all.maker.transcripts.fasta: Nucleotide sequences (mRNA) of all predicted transcripts.

### Round 1 Quality Control (AGAT & BUSCO)
We compute gene model and repeat statistics using AGAT (GAAS environment) and evaluate the completeness of the predicted proteome and transcriptome using BUSCO (sequence environment):

```bash
# Summary statistics of gene models and repetitive elements
conda activate GAAS
agat_sp_statistics.pl --gff Anoste_rnd1.all.gff -o Anoste_rnd1_model_stats.log
agat_sq_repeats_analyzer.pl -i Anoste_rnd1.all.gff -o Anoste_rnd1_repeats.log

# Check completeness of predicted proteins (-m prot) and transcripts (-m tran) (not -m geno!!)
conda activate sequence
export NUMEXPR_MAX_THREADS=80
busco -m prot -l $BUSCO/diptera_odb12 -c 8 -o Anoste_rnd1_prot_busco -i Anoste_rnd1.all.maker.proteins.fasta
busco -m tran -l $BUSCO/diptera_odb12 -c 8 -o Anoste_rnd1_tran_busco -i Anoste_rnd1.all.maker.transcripts.fasta
```

### Train Ab-initio Gene Predictor (SNAP) for Round 2 
To allow MAKER to predict species-specific genes ab-initio in Round 2, we train a SNAP Hidden Markov Model (Anoste_rnd1.hmm) using our highest-confidence Round 1 gene models (AED <= 0.1):

```bash
mkdir SNAP
cd SNAP
nano SNAP.sh
```
#### Content of SNAP.sh
```bash
#!/bin/bash

maker2zff -c 0 -e 0 -l 80 -x 0.1 -d ../Anoste_rnd1_master_datastore_index.log # Extract high-confidence gene models in .zff format
fathom genome.ann genome.dna -gene-stats > Anoste_rnd1_fathom.stats # Print summary statistics of the selected gene models
fathom genome.ann genome.dna -validate # Validate gene models and print summary statistics to stdout
fathom genome.ann genome.dna -categorize 1000 # Extract gene models together with 1000 bp at both ends for training
fathom uni.ann uni.dna -export 1000 -plus # Export and convert unique genes to plus strand

mkdir forge
cd forge
forge ../export.ann ../export.dna # Estimate HMM parameters inside a separate directory
cd ..
hmm-assembler.pl Anoste_rnd1 forge > Anoste_rnd1.hmm
```
##### Run the training script in the assembly environment:
```bash
conda activate assembly
bash SNAP.sh
```



~/Lab_Gen_Comparata/00_data$
 mkdir 00_data
cd 00_data
nano diptera_dataset.tsv
cat diptera_dataset.tsv
script:
ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/99_scripts/download_dataset.sh .
cat download_dataset.sh
#!/bin/bash

#This script downloads genomes and relative gff from NCBI datasets creating ready to use folders

AN2name=$1

mkdir 00_genome
mkdir 01_gff

while IFS=$'\t' read -r AN sname ID; do
        echo $AN
        #download specifying the name of the folder
        datasets download genome accession "$AN" --filename "$ID".zip --include genome,gff3
        #unzip specifying the name of the folder
        unzip "$ID".zip -d "$ID"
        #rename the two file of interest
        mv "$ID"/ncbi_dataset/data/"$AN"/*.fna 00_genome/"$ID".fna
        mv "$ID"/ncbi_dataset/data/"$AN"/*.gff 01_gff/"$ID".gff
        #delete the folder
        rm -r "$ID"/
done < "$AN2name"

conda activate sequence
 bash download_dataset.sh diptera_dataset.tsv



