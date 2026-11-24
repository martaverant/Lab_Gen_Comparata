# 04_GenomeAnnotation 
1. Identificare il contenuto ripetitivo del nostro genoma ("masking")
2. maker -> alligniamento di alta qualita
3. caratteristiche di altri genomi
4. proteomi di altri organismi simili -> ci aspettiamo che hanno il core proteomico simile
5. pipeline anche transcriptoma (va fatto atomaticamente)
valutazione finale con busco (versione proteoma 9 e 10 milla)

lunedi prossimo la annotazione
-> impiega tantissimo tempo 
genoma piccola e poi anche fatto male
secondo giro di annotazione
e alla fine Mirko ci dara i file finito perche li stoppiamo 

idea domanda biologica (variabilita biologica di zanzare) altre 4-5 specie nei dipteri 
che possono essere per Anoste (non deve essere il centro) puo essere anche il outgroup, ma deve essere dentro 
puo essere un po di tutto 
su NCBI 

recuperare 23/11/26

mkdir 04_GenomeAnnotation cd 04_GenomeAnnotation/ mkdir 00_round1 mkdir 01_round2 mkdir 02_round3 cd 00_round1/ ln -s ../../03_GenomeAssembly/03_scaffolding/Anoste_chr.fasta ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/00_data/02_Annotation/GCF_013141755.1_UCI_ANSTEP_V1.0_protein.faa Anoste.faa ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/00_data/02_Annotation/GCF_943734735.2_idAnoGambNW_F1_1_protein.faa Anogam.faa ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/00_data/02_Annotation/GCF_000001215.4_Release_6_plus_ISO1_MT_protein.faa Dromel.faa ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/00_data/02_Annotation/Anoste_RepeatModeler_library.fa conda activate assembly maker -CTL nano maker_opts.ctl # Dentro modificare:
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

(da Luca) 
24/11/2026

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


~/Lab_Gen_Comparata/04_GenomeAnnotation/00_round1$
  fasta_merge -d Anoste_rnd1_master_datastore_index.log
  gff3_merge -d Anoste_rnd1_master_datastore_index.log

  
  
