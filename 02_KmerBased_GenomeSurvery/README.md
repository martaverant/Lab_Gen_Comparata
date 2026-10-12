google -> cheat sheat di .md

# titolo 
codice backtick `



# Kmer based survery


```bash
# commando per fare il QC delle raw reads appena sequenziate
fastqc /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/00_data/00_reads/SRR11672503_1.fastq.gz
```
(base) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata$ cd 02_KmerBased_GenomeSurvery/
(base) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$ ls
SRR11672503_1_fastqc.html   SRR11672503_1_unpaired.fastq  SRR11672503_2_paired.fastq
SRR11672503_1_fastqc.zip    SRR11672503_2_fastqc.html     SRR11672503_2_unpaired.fastq
SRR11672503_1.fastq.gz      SRR11672503_2_fastqc.zip      stats_trimmomatic.log
SRR11672503_1_paired.fastq  SRR11672503_2.fastq.gz
(base) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$ git add SRR11672503_1_fastqc.html  SRR11672503_2_fastqc.html
(base) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   SRR11672503_1_fastqc.html
        new file:   SRR11672503_2_fastqc.html

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        SRR11672503_1.fastq.gz
        SRR11672503_1_fastqc.zip
        SRR11672503_1_paired.fastq
        SRR11672503_1_unpaired.fastq
        SRR11672503_2.fastq.gz
        SRR11672503_2_fastqc.zip
        SRR11672503_2_paired.fastq
        SRR11672503_2_unpaired.fastq
        stats_trimmomatic.log
(base) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$ git commit -m "QC on raw reads"[main 88c628a] QC on raw reads
 2 files changed, 374 insertions(+)
 create mode 100644 02_KmerBased_GenomeSurvery/SRR11672503_1_fastqc.html
 create mode 100644 02_KmerBased_GenomeSurvery/SRR11672503_2_fastqc.html
(base) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$ git log
commit 88c628a9d226eca28ad4dcf0b19274357b8ee14c (HEAD -> main)
Author: martaverant <marta.verant@studio.unibo.it>
Date:   Mon Oct 12 12:31:50 2026 +0200

    QC on raw reads

commit 6e65b52d5a8d23588d994c0213da118b0e1e0c1e (origin/main, origin/HEAD)
Author: martaverant <martaverant@gmail.com>
Date:   Mon Oct 5 13:00:19 2026 +0200

    Update README.md

commit ab9677fd7425421696f26d0c00da39db41031ebb
Author: martaverant <marta.verant@studenti.unibo.it>
Date:   Mon Oct 5 12:53:41 2026 +0200

    update README.md

commit 9fb6f96a3ee18bdf06ea6cb831a21ec67ba8fa19
Author: martaverant <martaverant@gmail.com>
Date:   Mon Oct 5 12:21:46 2026 +0200

    Initial commit
(base) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$ git pull
Already up to date.
(base) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$ git push
Enumerating objects: 6, done.
Counting objects: 100% (6/6), done.
Delta compression using up to 80 threads
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 270.38 KiB | 786.00 KiB/s, done.
Total 5 (delta 1), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (1/1), done.
To github.com:martaverant/Lab_Gen_Comparata.git
   6e65b52..88c628a  main -> main
(base) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$


03_2_fastqc.zip (Permission denied)
        at java.base/java.io.FileOutputStream.open0(Native Method)
        at java.base/java.io.FileOutputStream.open(FileOutputStream.java:293)
        at java.base/java.io.FileOutputStream.<init>(FileOutputStream.java:235)
        at java.base/java.io.FileOutputStream.<init>(FileOutputStream.java:184)
        at uk.ac.babraham.FastQC.Report.HTMLReportArchive.<init>(HTMLReportArchive.java:80)
        at uk.ac.babraham.FastQC.Analysis.OfflineRunner.analysisComplete(OfflineRunner.java:189)
        at uk.ac.babraham.FastQC.Analysis.AnalysisRunner.run(AnalysisRunner.java:123)
        at java.base/java.lang.Thread.run(Thread.java:842)
(assembly) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$ ls               
(assembly) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$ cp /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/02_KmerBased_GenomeSurvey/SRR11672503_1_fastqc.html .
(assembly) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$ cp /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/02_KmerBased_GenomeSurvey/SRR11672503_1_fastqc.zip .
(assembly) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$ cp /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/02_KmerBased_GenomeSurvey/SRR11672503_2_fastqc.html .                  
(assembly) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$ cp /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/02_KmerBased_GenomeSurvey/SRR11672503_2_fastqc.zip .                   
(assembly) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$ ls
SRR11672503_1_fastqc.html  SRR11672503_1_fastqc.zip  SRR11672503_2_fastqc.html  SRR11672503_2_fastqc.zip
(assembly) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$ ll
total 1764
drwxr-xr-x 2 STUDENTI^marta.verant STUDENTI^domain users   4096 Oct 12 11:41 ./
drwxr-xr-x 4 STUDENTI^marta.verant STUDENTI^domain users   4096 Oct  6 12:27 ../
-rw-r--r-- 1 STUDENTI^marta.verant STUDENTI^domain users 570969 Oct 12 11:41 SRR11672503_1_fastqc.html
-rw-r--r-- 1 STUDENTI^marta.verant STUDENTI^domain users 320326 Oct 12 11:41 SRR11672503_1_fastqc.zip
-rw-r--r-- 1 STUDENTI^marta.verant STUDENTI^domain users 573040 Oct 12 11:41 SRR11672503_2_fastqc.html
-rw-r--r-- 1 STUDENTI^marta.verant STUDENTI^domain users 324567 Oct 12 11:41 SRR11672503_2_fastqc.zip
(assembly) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$ ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/00_data/00_reads/SRR11672503_1.fastq.gz
(assembly) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$ ll
total 1768
drwxr-xr-x 2 STUDENTI^marta.verant STUDENTI^domain users   4096 Oct 12 11:46 ./
drwxr-xr-x 4 STUDENTI^marta.verant STUDENTI^domain users   4096 Oct  6 12:27 ../
-rw-r--r-- 1 STUDENTI^marta.verant STUDENTI^domain users 570969 Oct 12 11:41 SRR11672503_1_fastqc.html
-rw-r--r-- 1 STUDENTI^marta.verant STUDENTI^domain users 320326 Oct 12 11:41 SRR11672503_1_fastqc.zip
lrwxrwxrwx 1 STUDENTI^marta.verant STUDENTI^domain users     95 Oct 12 11:46 SRR11672503_1.fastq.gz -> /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/00_data/00_reads/SRR11672503_1.fastq.gz
-rw-r--r-- 1 STUDENTI^marta.verant STUDENTI^domain users 573040 Oct 12 11:41 SRR11672503_2_fastqc.html
-rw-r--r-- 1 STUDENTI^marta.verant STUDENTI^domain users 324567 Oct 12 11:41 SRR11672503_2_fastqc.zip
(assembly) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$ ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/00_data/00_reads/SRR11672503_2.fastq.gz                             
(assembly) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$ ls
SRR11672503_1_fastqc.html  SRR11672503_1.fastq.gz     SRR11672503_2_fastqc.zip
SRR11672503_1_fastqc.zip   SRR11672503_2_fastqc.html  SRR11672503_2.fastq.gz
(assembly) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$ trimmomatic PE -threads 20 -phred33 SRR11672503_1.fastq.gz SRR11672503_2.fastq.gz SRR11672503_1_paired.fastq SRR11672503_1_unpaired.fastq SRR11672503_2_paired.fastq SRR1167250
(assembly) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$ trimmomatic PE -threads 8 -phred33 SRR11672503_1.fastq.gz SRR11672503_2.fastq.gz SRR11672503_1_paired.fastq SRR11672503_1_unpaired.fastq SRR11672503_2_paired.fastq SRR11672503_2_unpaired.fastq ILLUMINACLIP:/opt/miniforge3/envs/assembly/share/trimmomatic-0.40-0/adapters/TruSeq3-PE.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36 2> stats_trimmomatic.log
^[[4~
                                                                                                                                                                                                                                                                                                                                                   
(assembly) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$             (assembly) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$
(assembly) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$
(assembly) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$ cp /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/02_KmerBased_GenomeSurvey/SRR11672503_1_fastqc.html .

.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$ ls
SRR11672503_1_fastqc.html  SRR11672503_1.fastq.gz      SRR11672503_1_unpaired.fastq  SRR11672503_2_fastqc.zip  SRR11672503_2_paired.fastq    stats_trimmomatic.log
SRR11672503_1_fastqc.zip   SRR11672503_1_paired.fastq  SRR11672503_2_fastqc.html     SRR11672503_2.fastq.gz    SRR11672503_2_unpaired.fastq
(assembly) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$ ll
total 79424316
drwxr-xr-x 2 STUDENTI^marta.verant STUDENTI^domain users        4096 Oct 12 11:50 ./
drwxr-xr-x 4 STUDENTI^marta.verant STUDENTI^domain users        4096 Oct  6 12:27 ../
-rw-r--r-- 1 STUDENTI^marta.verant STUDENTI^domain users      570969 Oct 12 11:41 SRR11672503_1_fastqc.html
-rw-r--r-- 1 STUDENTI^marta.verant STUDENTI^domain users      320326 Oct 12 11:41 SRR11672503_1_fastqc.zip
lrwxrwxrwx 1 STUDENTI^marta.verant STUDENTI^domain users          95 Oct 12 11:46 SRR11672503_1.fastq.gz -> /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/00_data/00_reads/SRR11672503_1.fastq.gz
-rw-r--r-- 1 STUDENTI^marta.verant STUDENTI^domain users 40316211886 Oct 12 12:37 SRR11672503_1_paired.fastq
-rw-r--r-- 1 STUDENTI^marta.verant STUDENTI^domain users   701708094 Oct 12 12:37 SRR11672503_1_unpaired.fastq
-rw-r--r-- 1 STUDENTI^marta.verant STUDENTI^domain users      573040 Oct 12 11:41 SRR11672503_2_fastqc.html
-rw-r--r-- 1 STUDENTI^marta.verant STUDENTI^domain users      324567 Oct 12 11:41 SRR11672503_2_fastqc.zip
lrwxrwxrwx 1 STUDENTI^marta.verant STUDENTI^domain users          95 Oct 12 11:47 SRR11672503_2.fastq.gz -> /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/00_data/00_reads/SRR11672503_2.fastq.gz
-rw-r--r-- 1 STUDENTI^marta.verant STUDENTI^domain users 40190009686 Oct 12 12:37 SRR11672503_2_paired.fastq
-rw-r--r-- 1 STUDENTI^marta.verant STUDENTI^domain users   120647404 Oct 12 12:37 SRR11672503_2_unpaired.fastq
-rw-r--r-- 1 STUDENTI^marta.verant STUDENTI^domain users         929 Oct 12 12:37 stats_trimmomatic.log
(assembly) STUDENTI^marta.verant@SGBGA-D142242S:~/Lab_Gen_Comparata/02_KmerBased_GenomeSurvery$

