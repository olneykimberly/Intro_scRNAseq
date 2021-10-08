## 1. Download cellranger
- Download cellranger .tar.gz file using the wget command
  ```
  wget -O https://cf.10xgenomics.com/releases/cell-exp/cellranger-6.1.1.tar.gz?Expires=1629542558&Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9jZi4xMHhnZW5vbWljcy5jb20vcmVsZWFzZXMvY2VsbC1leHAvY2VsbHJhbmdlci02LjEuMS50YXIuZ3oiLCJDb25kaXRpb24iOnsiRGF0ZUxlc3NUaGFuIjp7IkFXUzpFcG9jaFRpbWUiOjE2Mjk1NDI1NTh9fX1dfQ__&Signature=Rq4xVqqTsiYx1pEpMMEeIwgTtzXhvzjU24CuARnXj~OxKBnVpjMb0E~Qcry-KeWZ~iKnmF2JLP1CE8J5e-i1lQYgIdtk7lwWJIeIvY5Os6hhen80fw-bogn8nkTY412s8oAmUsjyh-yZxMVI2Vj49EwVuI2F5a3NKepERpnIx~LxOWXsOIpOruiQ8IDTIFL9DliBxuG0w939Yi0ijk4gZrhNDPXpeDiUDHmwR~RcPWXFRlRT7fENK4janThNdnJXcHRNcE06QnzDvufXtFBxpkltgq3hZ7zuGjML2v1oonWr434YGyAlwpJaHtOZq8k7sb-dWo-zIePTKv47BwJGLg__&Key-Pair-Id=APKAI7S6A5RYOXBWRPDA
  ```
- If the link above no longer works, it can also be found at https://support.10xgenomics.com/single-cell-gene-expression/software/downloads/latest
- Unpack the file
  ```
  tar -xzvf cellranger-6.1.1.tar.gz
  ```
- Go into the cellranger-6.1.1 directory and get the path
  ```
  cd cellranger-6.1.1
  pwd
  ```
- Export the path to your new tool.  Replace **/your/path/to/cellranger-6.1.1** by whatever the output was from the pwd command.  You can also append this to your .bashrc file.
  ```
  export PATH=/your/path/to/cellranger-6.1.1:$PATH
  ```
- Run this command to make sure everything works. Your output should be the path to cellranger.
  ```
  which cellranger
  ```
## 2. Sitecheck
- Make sure you meet all conditions to run the program. Execute these commands for both the host and cluster node to see if you meet the requirements.
  ```
  cellranger sitecheck > sitecheck.txt
  cellranger upload your@email.edu sitecheck.txt
  ```
- 10X Genomics will email you and ask if you want a sitecheck review.
## 3. Filter gtf and index genome
- Your fasta and gtf files must be compatible with STAR (Ensembl's are compatible). Cellranger uses STAR to index the genome.
- Filter the gtf file and index the hard masked chrY reference genome.
- Index our hard masked chrY reference genome.
- Run the **cellranger_build_ref.sh** script
  ```
  ./cellranger_build_ref.sh
  ```
  - Notes are included in the script
  - You can also submit this to a cluster to run faster
## 4. Cellranger count
- A snakemake pipeline is challenging with cellranger count.  We will work on a snakemake pipeline later.
- For now, two scripts are used to submit cellranger count to the cluster
  - **cellranger_count.sh** - This bash script loops through all your sample IDs and submits a job to the cluster for each sample ID.  Technically, the script submits the **counts.ogs** script with the sample ID passed.
  - **counts.ogs** - This script actually calls cellranger count when given a sample ID.
- Both scripts are located in this folder of the repository. 
## 5. Cellranger aggr
- The cellranger aggr command is used to pool/aggregate cellranger count runs
  - For example, we can pool all blood samples together
- Create a libraries.csv file
  - Two required columns are sample_id and molecule_h5.  The molecule_h5 column is the path to the molecule_info.h5 file output by cellranger counts.  This is located in **sample_id/outs/molecule_info.h5**.
  - Optional metadata columns can be added and later viewed in the Loupe browser (e.g. treatment, timepoint)
- The cellranger aggr command inputs a CSV file (containing paths to molecule_info.h5 files) and outputs a single feature-barcode matrix containing all the data
- When multiple GEM wells are combined a GEM well suffix is appended to the barcode sequence
- GEM wells are subsampled and have the same effective sequencing depth
## 6. Loupe Browser
- Read in individual .cloupe files from cellranger count or .cloupe files from cellranger aggr (like pooled E. coli final blood)


