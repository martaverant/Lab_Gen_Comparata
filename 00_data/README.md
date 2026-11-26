# 00_data: Comparative Genomics Dataset Collection
To perform comparative genomics analyses with our annotated *Anopheles stephensi* genome, we selected 4–5 already annotated Diptera species from **NCBI Datasets** and recorded their accession numbers, species names, and 6-letter IDs in a tab-separated file (`diptera_dataset.tsv`).

> **Note:** Only `README.md` and `diptera_dataset.tsv` are stored on GitHub. The downloaded genome FASTA files (`00_genome/*.fna`), annotation files (`01_gff/*.gff`), and `.zip` archives remain on the university server due to file size limits.


## Setup and Download Workflow

Create the `00_data` directory at the root of the repository, create `diptera_dataset.tsv` (columns separated by **tabs**: `Accession_Number`, `Species_Name`, `ID`), and run Mirko's `download_dataset.sh` script inside the `sequence` environment:

```bash
mkdir -p ~/Lab_Gen_Comparata/00_data
cd ~/Lab_Gen_Comparata/00_data

# Create the tab-separated species table and verify correct structure
nano diptera_dataset.tsv
cat diptera_dataset.tsv

# Link the download script and download genomes + GFF3 annotations
ln -s /home/PERSONALE/mirko.martini3/Lab_CompGeno/00_practice/99_scripts/download_dataset.sh .
conda activate sequence
bash download_dataset.sh diptera_dataset.tsv
```

### Script download_dataset.sh
```bash
#!/bin/bash

# This script downloads genomes and relative GFF3 files from NCBI Datasets, creating ready-to-use folders

AN2name=$1

mkdir -p 00_genome
mkdir -p 01_gff

while IFS=$'\t' read -r AN sname ID; do
        echo $AN
        # Download genome (.fna) and annotation (.gff) using the accession number
        datasets download genome accession "$AN" --filename "$ID".zip --include genome,gff3
        # Unzip into a temporary folder named after the 6-letter ID
        unzip "$ID".zip -d "$ID"
        # Move and rename the files of interest into 00_genome/ and 01_gff/
        mv "$ID"/ncbi_dataset/data/"$AN"/*.fna 00_genome/"$ID".fna
        mv "$ID"/ncbi_dataset/data/"$AN"/*.gff 01_gff/"$ID".gff
        # Remove the temporary unzipped folder
        rm -r "$ID"/
done < "$AN2name"
```

#### How it works

- Creates two clean output directories: 
- 00_genome/ (containing $ID.fna) and 01_gff/ (containing $ID.gff).
- Reads diptera_dataset.tsv line by line (while IFS=$'\t' read -r AN sname ID).
- Uses ncbi-datasets (datasets download genome accession "$AN" --include genome,gff3) to download only the genome assembly and GFF3 annotation, unzips them, renames both files with the 6-letter species ID, and deletes the temporary unzipped directory.


