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

less mapping.sh (entra nel file) 
cat mapping.sh (scrive nella screen il testo del file)
head mapping.sh (prime 10 righe del file) 

bash mapping.sh 8 Anoste_raw.fasta SRR11672503_1_paired.fastq SRR11672503_2_paired.fastq Anoste_pol SRR11672506.fastq.gz
control + a poi C -> fare una nuova screen 
control + a p -> previous
control + a n -> next
control + a x -> cancellare

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

dobbiamo creare una tabella con informazioni 
scarichiamo il proteoma = genoma annotato 
deve avere: prima colonna: separato da tabulazione -> session number 
seconda colonna nome completo con underscore il spazio non esiste (genbank o Refseq)
terza: codice a 6 lettere 3 lettere del genere 3 della specie e solo la prima lettere è maiuscola 

caratteristiche di assembly sono importante e sottolinieate, report comparativo 
comparare con altre specie 
grandezza genoma, contenuto ripetitivo 
COMPARARE 
file in quale siamo interessati .fna.gz .gff.gz (contiene la annotazione) + inofrmazioni delle proteine


il commando non è andato a buon fine perché non c'era abbastanza spazio per salvare. 
BAM and BAI I mapping
abbiamo fatto i link dei file completi: 
ln -s con tutti questi path: 
/home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/03_GenomeAssembly/01_polishing/Anoste_raw_pb_sorted.bam     
/home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/03_GenomeAssembly/01_polishing/Anoste_raw_pb_sorted.bam.bai 
/home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/03_GenomeAssembly/01_polishing/Anoste_raw_sr_sorted.bam     
/home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/03_GenomeAssembly/01_polishing/Anoste_raw_sr_sorted.bam.bai
 . 

 e anche per questo: raw assembly Mirko
/home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/03_GenomeAssembly/00_assembly_raw/Anoste_raw.fasta .

dopo questo: 

hypo -d <DRAFT_Contigs> -r @<READS_PATH> -s <APPROXIMATE_GENOMESIZE> -c <SHORT_READSCOVERAGE> -b <SORTED_BAM_SR> -B <SORTED_BAM_PB> -t <NUMBER_THREADS>

ci manca però SHORT_READSCOVERAGE
che otterremmo con 
compito: capire come funziona questo commando: 
mosdepth -n --fast-mode --by 500 <...>
possiamo utilizzare IA e soprattutto guardare nel help o nel manuale

mosdepth -n --fast-mode --by 500 Anoste_PB Anoste_raw_pb_sorted.bam
mosdepth -n --fast-mode --by 500 Anoste_SR Anoste_raw_sr_sorted.bam

 -n: Non genera un file contenente la coverage di ogni singola base.
- --fast-mode: Rende il calcolo più veloce, semplificando il calcolo della coverage.
- --by 500: Divide il genoma in finestre di 500 bp e calcola la coverage media per ciascuna finestra.

la soluzione: mosdepth -n --fast-mode --by 500 Anoste_raw_[pb|sr] <sorted_bam>
zcat Anoste_raw_[pb|sr].regions.bed.gz | awk '{sum += $4;count++} END {print sum / count}' > <OUTFILE_coverage>



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








