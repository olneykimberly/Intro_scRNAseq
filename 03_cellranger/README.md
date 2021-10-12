## 1. Download cellranger
- Download cellranger .tar.gz file using the wget command
  ```
  wget -O cellranger-6.1.1.tar.gz "https://cf.10xgenomics.com/releases/cell-exp/cellranger-6.1.1.tar.gz?Expires=1634099562&Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9jZi4xMHhnZW5vbWljcy5jb20vcmVsZWFzZXMvY2VsbC1leHAvY2VsbHJhbmdlci02LjEuMS50YXIuZ3oiLCJDb25kaXRpb24iOnsiRGF0ZUxlc3NUaGFuIjp7IkFXUzpFcG9jaFRpbWUiOjE2MzQwOTk1NjJ9fX1dfQ__&Signature=n7izd3HdqaY2YTtocmGZYJazfHPgb2vjUDPoljhhk5~WSdzNaQZff52LUzFSYwdznJFbl1A6RK8f1pQm0ZCHZ-0MdpkVXGbRyDmZYD3GrMNXbSP2HkTWEXTxTBVW1RyE8zQUxaiKhVSqEqejUr2HZsOiqd0rxkLHEO1ROek9NXkQjXU0gCgQKPrBWwzcXmCAAVuxGpmbvdDhVG0DLzAweYvqiqmcEDl3oEJm~zcKbKPeYkD76V3Rw2FCOw5aAg--ERihUY6Pn9MA~6SUHPia38NzD4kvUtqh-d9nqcMeBoCTWHt-xPANYUCFYZYFtjJiOqxcC0iNcfRTSn636f9TMQ__&Key-Pair-Id=APKAI7S6A5RYOXBWRPDA"
  ```
- If the link above no longer works, it can also be found at https://support.10xgenomics.com/single-cell-gene-expression/software/downloads/latest
- Unpack the file
  ```
  tar -xzvf cellranger-6.1.1.tar.gz
  ```
- Go into the cellranger-6.1.1 directory and get the path.  We will use this path later.
  ```
  cd cellranger-6.1.1
  pwd
  ```
- Everytime we use a cellranger command we will need to put the path to where it downloaded.  However, putting the entire path every time can be lengthy.  So, we will add the path to cellranger in our .bash_profile (think settings file).  Head back to your home directory using the command below.
  ```
  cd ~
  ```
- Use the vi text editor in your terminal to add the path to your .bash_profile.  You will edit this file and then add the two lines of code below to the end of the file.  Replace **<put_your_path_here>** by whatever the output was from the pwd command.  I recommend doing this in a text editor (Notepad, BBedit, Atom) first, making changes, and then pasting it into your .bash_profile using the built in vi text editor.
  ```
  # path for cellranger
  export PATH=<put_your_path_here>:$PATH
  ```
- Now we want the changes we made to be registered.  To this we source our file
  ```
  source .bash_profile
  ```
- Let's see if everything worked. Run the command below. Your output should be the path to where cellranger is downloaded on your computer.
  ```
  which cellranger
  ```
## 2. Sitecheck (you can skip if you know it already works for someone else)
- Make sure you meet all conditions to run the program. Execute these commands for both the host and cluster node to see if you meet the requirements.
  ```
  cellranger sitecheck > sitecheck.txt
  cellranger upload your@email.edu sitecheck.txt
  ```
- 10X Genomics will email you and ask if you want a sitecheck review.
## 3. Filter gtf and index genome
- Make sure you have the annotation file and reference genome already downloaded.  If you don't, go back to the 02_getData folder.
- Your fasta and gtf files must be compatible with STAR (Ensembl's are compatible). Cellranger uses STAR to index the genome.
- Filter the gtf file and index the genome.
- I have given the script below you will just have to edit the header and paths.  Learn from this example because you will be creating your own scripts after this.
  ```
  #!/bin/sh
  #$ -cwd
  #$ -N cellranger_build_ref
  #$ -m abe 
  #$ -M email@mayo.edu
  #$ -pe threaded 16
  #$ -l h_vmem=16G
  #$ -q 1-day,4-day,lg-mem
  #$ -V

  # source your setting
  source $HOME/.bash_profile

  # change directory to where files are located
  cd /path/to/refs

  # step 1: 
  # filter the gene annotation file to exclude the following gene_biotypes
  # this is per the recommendation of cellranger to not include these biotypes: https://support.10xgenomics.com/single-cell-gene-expression/software/pipelines/latest/advanced/references
  cellranger mkgtf Sus_scrofa.Sscrofa11.1.103.gtf Sus_scrofa.Sscrofa11.1.103.filteredCellranger.gtf  \
                     --attribute=gene_biotype:protein_coding \
                     --attribute=gene_biotype:lincRNA \
                     --attribute=gene_biotype:antisense \
                     --attribute=gene_biotype:IG_LV_gene \
                     --attribute=gene_biotype:IG_V_gene \
                     --attribute=gene_biotype:IG_V_pseudogene \
                     --attribute=gene_biotype:IG_D_gene \
                     --attribute=gene_biotype:IG_J_gene \
                     --attribute=gene_biotype:IG_J_pseudogene \
                     --attribute=gene_biotype:IG_C_gene \
                     --attribute=gene_biotype:IG_C_pseudogene \
                     --attribute=gene_biotype:TR_V_gene \
                     --attribute=gene_biotype:TR_V_pseudogene \
                     --attribute=gene_biotype:TR_D_gene \
                     --attribute=gene_biotype:TR_J_gene \
                     --attribute=gene_biotype:TR_J_pseudogene \
                     --attribute=gene_biotype:TR_C_gene

  # For the GTF file, genes must be annotated with feature type 'exon' (column 3). 
  # this step is - Prior to mkref below as the GTF annotation files from Ensembl and NCBI are typically filtered with mkgtf to include only a subset of the annotated gene biotypes.

  # step 2: 
  # now that you have created a filtered gene annotation file, you can build the reference index
  cellranger mkref --genome=cellranger_genomeDir --fasta=Sscrofa11.1.chrYHardMasked.fa  --genes=Sus_scrofa.Sscrofa11.1.103.filteredCellranger.gtf
  ```
## 4. Cellranger count

## 5. Cellranger aggr
- The cellranger aggr command is used to pool/aggregate cellranger count runs
  - For example, we can pool all blood samples together
- Create a libraries.csv file
  - Two required columns are sample_id and molecule_h5.  The molecule_h5 column is the path to the molecule_info.h5 file output by cellranger counts.
  - Optional metadata columns can be added and later viewed in the Loupe browser (e.g. treatment, timepoint)
- The cellranger aggr command inputs a CSV file (containing paths to molecule_info.h5 files) and outputs a single feature-barcode matrix containing all the data
- When multiple GEM wells are combined a GEM well suffix is appended to the barcode sequence
- GEM wells are subsampled and have the same effective sequencing depth


## 6. Loupe Browser
- Read in individual .cloupe files from cellranger count or .cloupe files from cellranger aggr (like pooled E. coli final blood)


