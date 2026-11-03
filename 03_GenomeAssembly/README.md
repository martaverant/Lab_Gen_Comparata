# Assembly 

mi manca la parte del 19/10 -> recuperare
oggi 02/11
(base) STUDENTI^marta.verant@SGBGA-D142242S:~$ ls
Lab_Gen_Comparata  prova  snap
(base) STUDENTI^marta.verant@SGBGA-D142242S:~$ cd Lab_Gen_Comparata/
(base) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata$ ls
00_Bash                         02_KmerBased_GenomeSurvery  04_GenomeAnnotation     README.md
01_ComputerEnv_ProjectWorkflow  03_GenomeAssembly           markdown_cheatsheet.md
(base) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata$ cd 03_GenomeAssembly/
(base) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/03_GenomeAssembly$ ls
README.md
(base) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/03_GenomeAssembly$ mkdir 00_assembly_raw
(base) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/03_GenomeAssembly$ mv * 00_assembly_raw/
mv: cannot move '00_assembly_raw' to a subdirectory of itself, '00_assembly_raw/00_assembly_raw'
(base) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/03_GenomeAssembly$ ls
00_assembly_raw
(base) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/03_GenomeAssembly$ cd 00_assembly_raw/
(base) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/03_GenomeAssembly/00_assembly_raw$ ls
README.md
(base) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/03_GenomeAssembly/00_assembly_raw$ ^C
(base) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/03_GenomeAssembly/00_assembly_raw$

less Anoste_raw.fasta -> leggere il file
mv Anoste_raw Anoste_raw.fasta -> move

rubare un file:
cd
cd ../../STUDENTI/
home/STUDENTI$ cd virginia.piccinotti/
cp Anoste_raw.fasta ~/Lab_Gen_Comparata/03_GenomeAssembly/00_assembly_raw -> copy del file 

export NUMEXPR_MAX_THREADS=80
busco -m <MODE> -l <LINEAGE> -c 8 -o Anoste_raw -i Anoste_raw.fasta
se nn sai come lanciare un commando: dallo standatd output: busco --help | less  -> sempre verificare se siamo nella giusta environment vedi github (MirkMartLab_CompGeno/2026/01_ComputerEnv_ProjectWorkflow

 conda activate sequence
 2 databases diptera e culicidae, diptera è quello piu ampio 
 $BUSCO = posizione del computer (variabile) placeholder
 echo variabile (computer espandera) 
 busco -m geno -l $BUSCO/diptera_odb12 -c 8 -o Anoste_raw -i Anoste_raw.fasta

nuova screen: tmux new -s mapping (mapping è solo il nome della screen) 
mkdir -> creare la cartella  mkdir 01_polishing
cd 01_polishing/ entrare nella cartella 
skript = miniprogramma, file di testo che veranno lanciati una dopo l'altro 

Pre-requisite: Mapping short and long reads (sr ~50 mins with 30 threads + pac ~49 mins with 30 threads)
minimap2 -ax <PRESET_Options> --MD -t <cores_number> <target.fa> <fastq1> <fastq2> > <SAM_FILE>
samtools view -b <SAM FILE> > <BAM FILE>
rm <SAM FILE>
samtools sort -@<NUM_THREADS> -o <OUT_FILE> <INFILE>
samtools index <INFILE>
hypo programma per polishing ma bisogna avere il mapping 
mapping= ?? corrispondenza di read e ? 
sem = bam ma versione binaria 
possiamo inpacchetare tutti questi 5 commandi in un skript cosi diventa automatico, lancabile automaticamente dal computer

creaiamo un file: nano mapping.sh   sh= skript bash 
poi incollare tutti i 5 commandi
shibang= parte dello skrip di linguaggio di programmazione 
#commento -> non verà lanciata! #!/bin/bash -> lo skript è in linguaggio bash 
poi scriviamo cosa fa questo skrip che tutto lo capiscono 
 Mapping short and long reads on assembly 
# Mapping short and long reads on assembly

output di un file dentro un commando 
-o ridirezionare output di un file dentro in un file ??(non so se sia giusto) 

> riposizionato in un file, e crea anche il file nuovo con dentro cio che abbiamo detto di mettere, e il contentuto verrà sovrascritto con due >> (maggiori) appendo senconda riga, lascio quello che era già nel file, e aggiungo altre righe di commando


cat, less, nano (text editor) -> importanti per leggere e modificare un file 

Commando finale: 

#!/bin/bash

# Mapping short and long reads on assembly

# Mapping short and long reads on assembly
minimap2 -ax <PRESET_Options> --MD -t <cores_number> <target.fa> <fastq1> <fastq2> > <SAM_FILE>

# conversion SAM into BAM
samtools view -b <SAM FILE> > <BAM FILE>
rm <SAM FILE>

# sorting and indexing of BAM file
samtools sort -@<NUM_THREADS> -o <OUT_FILE> <INFILE>
samtools index <INFILE>

skript dovrebbe essere piu generale possibile 
non uttilizzare gli imput, non puttiamo i file nostri 
variabile = placeholder 
variabile =$ 
variabile importante che siano chiare sempre in """

minimap2 -ax sr = short reads 
#!/bin/bash

# Mapping short and long reads on assembly

"$CPU"=$1
"$INPUT"=$2
"$SR_1"=$3
"$SR_2"=$4
"$PREFIX"=$5




# Mapping short and long reads on assembly
minimap2 -ax sr --MD -t "$CPU" "$INPUT" "$SR_1" "$SR_2" > "$PREFIX".sam

# conversion SAM into BAM
samtools view -b "$PREFIX".sam "$PREFIX".bam
rm "$PREFIX".sam

# sorting and indexing of BAM file
samtools sort -@"$CPU" -o "$PREFIX" sorted.bam "$PREFIX".bam
samtools index "$PREFIX"_sorted.bam
cotrol s speichern 
control x esci 

cat mapping.sh -> fa vedere il file 

ln -s ../00_assembly_raw/Anoste_raw.fasta . -> crea Anoste_raw.fasta in cyano (?) 
ln -s ../../02_KmerBased_GenomeSurvery/SRR11672503_1_paired.fastq .
ln -s ../../02_KmerBased_GenomeSurvery/SRR11672503_2_paired.fastq .
ln -s crea il link per poi utilizzare i file nel skript 



conda activate assembly

come si lancia lo skript? 
bash mapping.sh 8 Anoste_raw.fasta SRR11672503_1_paired.fastq SRR11672503_2_paired.fastq Anoste_pol
skript finito, ma qualcosa non va vediamo domani


03/11/2026
entrare nella screen tmux a -t mapping
nano mapping.sh

# aggiungiamo anche per LONG reads e agiustiamo il commando (SERNZA $ davanti alle variabili): 
map-pb invece di sr
long reads soltanto in una direzione 
#!/bin/bash

# Mapping short and long reads on assembly

CPU=$1
INPUT=$2
SR_1=$3
SR_2=$4
PREFIX=$5
LR=$6


# Mapping SHORT reads
minimap2 -ax sr --MD -t "$CPU" "$INPUT" "$SR_1" "$SR_2" > "$PREFIX"_sr.sam

# conversion SAM into BAM
samtools view -b "$PREFIX"_sr.sam "$PREFIX"_sr.bam
rm "$PREFIX"_sr.sam

# sorting and indexing of BAM file
samtools sort -@"$CPU" -o "$PREFIX"_sr_sorted.bam "$PREFIX"_sr.bam
samtools index "$PREFIX"_sr_sorted.bam



# Mapping LONG reads
minimap2 -ax map-pb --MD -t "$CPU" "$INPUT" "$LR" > "$PREFIX"_lr.sam

# conversion SAM into BAM
samtools view -b "$PREFIX"_lr.sam "$PREFIX"_lr.bam
rm "$PREFIX"_lr.sam

# sorting and indexing of BAM file
samtools sort -@"$CPU" -o "$PREFIX"_lr_sorted.bam "$PREFIX"_lr.bam
samtools index "$PREFIX"_lr_sorted.bam
control S -> salvare 
control x -> uscire 

../ -> per path relativi 
senza puntini -> path assoluto (ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/00_data/00_reads/SRR11672506.fastq.gz .)
SRR11672506.fastq.gz -> abbiamo aggiunto un link (cyano), grigio (file), rosso (rotto o ?), rosa (immagine) 

less mapping.sh
cat mapping.sh
head mapping.sh (prime 10 righe del file) 

bash mapping.sh 8 Anoste_raw.fasta SRR11672503_1_paired.fastq SRR11672503_2_paired.fastq Anoste_pol SRR11672506.fastq.gz
control + a poi C -> fare una nuova screen 
control + a p -> previous
control + a n -> next

salvare il file di busco: 
git add short_summary.specific.diptera_odb12.Anoste_raw.txt
git pull
git commit -m "QC BUSCO on raw assembly" (-m messaggio)
git push


vedi su 05 tutto scritto: 
risultato dobbiamo fare un GENOME REPORT
per esame 4/5 genomi di ditteri
5/6 specie, immaginarvi ricerca biologica (trasmissione malattie, resistenza ambientale/patogeni ecc.) 
1-2 paper leggere su quale specie vogliamo lavorare
cercare e scaricarle le informazioni 
-> database 
NCBI Datasets  -> genome (richerca) -> diptera filtare i risultati -> genoma annotato (annotated genomes) -> qui possiamo scegliere tutto 
poi giocate cun po con i filtri 

dobbiamo crare una tabella con informazioni 
scarichiamo il proteoma = genoma annotato 
deve avere: prima colonna: separato da tabulazione -> session number 
seconda colonna nome completo con underscore il spazio non esiste (genbank o Refseq)
terza: codice a 6 lettere 3 lettere del genere 3 della specie e solo la prima lettere è maiuscola 

caratteristiche di assembly sono importante e sottolinieate, report comparativo 
comparare con altre specie 
grandezza genoma, contenuto ripetitivo 
COMPARARE 
file in quale siamo interessati .fna.gz .gff.gz (contiene la annotazione) + inofrmazioni delle proteine
