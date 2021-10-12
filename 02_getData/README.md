# 1. Create a new project folder
- Make sure you are in your m-folder on the cluster
- Create a new project folder that we will do all our work in.  Feel free to name in whatever you want.  Then, cd into it.
  ```
  mdkir pigs_single_cell
  cd pigs_single_cell
  ```
# 2. Create subfolders
- We are going to create some sub-folders within your project folder where we can store useful information like reference files, scripts, and raw data.
  ```
  mdkir refs scripts rawData counts aggr
  ```
# 3. Download (or copy and paste) reference genome and annotation file
- The reference genome is in fasta format.
- The annotation file is in gtf format.
- cd into your **refs** folder.  This is where we will download our references.
- We get these two files from Ensembl because we know they are compatible with cellranger.  We can use the wget command to download any file from the internet.  Run the command below.  Downloading the fasta may take a while.
  ```
  wget http://ftp.ensembl.org/pub/release-104/fasta/sus_scrofa/dna/Sus_scrofa.Sscrofa11.1.dna.toplevel.fa.gz
  wget http://ftp.ensembl.org/pub/release-104/gtf/sus_scrofa/Sus_scrofa.Sscrofa11.1.104.gtf.gz
  ``` 
- If the links no longer work you can get them from here https://useast.ensembl.org/Sus_scrofa/Info/Index
# 4. Download (or copy and paste) raw fastq files for samples
- We will only use one sample to save time (each sample takes about 5 hours when you run cellranger count).
- cd into your **rawData*** folder
- Use the command below to copy and paste an E. coli pig blood sample ().
  ```
  cp /where/its/stored/sample.fastq /where/you/want/it/sample.fastq
  ```
