## 1. Download cellranger
- FIRST GO TO YOUR M-FOLDER ON THE CLUSTER
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
- Everytime we use a cellranger command we will need to put the path to where it's downloaded.  However, putting the entire path every time can be lengthy.  So, we will add the path to cellranger in our .bash_profile (think settings file).  Head back to your home directory using the command below.
  ```
  cd ~
  ```
- Use the vi text editor in your terminal to add the path to your .bash_profile.  You will edit this file and then add the two lines of code below to the end of the file.  Replace **<put_your_path_here>** (this means removes the < and > symbols) by whatever the output was from the pwd command.  I recommend doing this in a text editor (Notepad, BBedit, Atom) first, making changes, and then pasting it into your .bash_profile using the built in vi text editor.
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
- Make sure you have the annotation file and reference genome already downloaded and located in your **refs/** folder.  If you don't, go back to the 02_getData folder and follow the steps.
- We will be running the cellranger mkgtf and mkref command.  We will submit both of these commands as jobs to the cluster because we want to run everything quickly! It can be done from you terminal like normal but will take forever.
	- 10X Genomics explanation about what mkgtf and mkref do: https://support.10xgenomics.com/single-cell-gene-expression/software/pipelines/latest/advanced/references
	- Your fasta and gtf files must be compatible with STAR (Ensembl's are compatible). Cellranger uses STAR to index the genome.
	- mkgtf will filter the gtf file and mkref will index the genome.
- I have given the script/job below; you will just have to **edit some of the header and paths**.  Learn from this example because you will be creating your own scripts after this.
	- Remember you can create and edit a script in your local text editor.  Then you can create and copy/paste the file using the vi text editor built in to your terminal.
	- Store all your scripts in the **scripts/** folder. 
	- Can't remember what everything in the header is? Mayo job submission resource: https://mctools.sharepoint.com/teams/DCISSS/HPCServices/SitePages/Submit%20job.aspx 
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
  cellranger mkgtf Sus_scrofa.Sscrofa11.1.104.gtf Sscrofa11.1.104.filteredCellranger.gtf  \
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
  cellranger mkref --genome=cellranger_genomeDir --fasta=Sus_scrofa.Sscrofa11.1.dna.toplevel.fa --genes=Sscrofa11.1.104.filteredCellranger.gtf
  ```
- Since we ran a threaded job, you will get LOTS of output files.  A lot of these are empty and annoying.  Here is a quick command to delete all empty files in you current directory.  This way you don't have to go through and view each one to see if it contains anything.
  ```
  find . -size 0 -delete
  ```
  - the command is find, . means the current directory we are in, -size is a option/flag, 0 is the argument passed to the flag, -delete is another option/flag
## 4. Cellranger count
- Our fastq files are stored here
  ```
  /research/labs/neurology/fryer/m214960/practice_single_cell
  ```
- Below is the usage for cellranger count. 
	- Keep in mind we have two samples so you will have to create two separate scripts.  
	- You could submit it all on one script but each sample take 5 hours.  
	- So we will submit these two jobs in parallel to avoid taking 10 hours!
- Remember
	- create a header
	- source your .bash_profile (so the cluster knows the path to cellranger)
	- cd into the folder you want to be in or provide the absolute path to all folders/files
		- the output generated will be put in whatever folder you are in
		- if you want count output in your count folder, make sure to go there first
		- you will then have to provide the absolute path for other arguments
	- Do NOT include the carrots (< >) that are in the usage. You should replace this with your input.
- Cellranger count documentation: https://support.10xgenomics.com/single-cell-gene-expression/software/pipelines/latest/using/count
  ```
  # run cellranger count on a single sample
  cellranger count --id=<unique_id> --sample=<fastq_prefix> --fastqs=</path/to/fastq/folder> --transcriptome=</path/to/genomeDir> --localcores=16 --localmem=50
	
   # --id is a unique run id, we use how we have been naming samples (e.g. E19_BB)
   # --sample is the the unique prefix cellranger will look for in all the fastq files.  Remember we sequence over multiple lanes.  So they must know what files are related.
   # --fastqs is the path to the entire FOLDER containing scRNA fastq files.  The path to the folder you will be using is in the 02_getData step. 
   # --transcriptome is the path to the pig reference genome. This was created in the prior step.  We named it cellranger_genomeDir.
   # --localcores will restrict cellranger to 16 cores    
   # --localmem will restrict cellranger to 50G memory which is need in order to run, else you will receive the error of limited mem issue
  ```
## 5. Cellranger aggr
- Documentation: https://support.10xgenomics.com/single-cell-gene-expression/software/pipelines/latest/using/aggregate
- The cellranger aggr command is used to pool/aggregate cellranger count runs.
	- Like pooling our two blood samples together.
	- This way we only need one data file. 
- aggr does some processing and normalization so you can view data quickly in the Loupe Browser.
- When multiple GEM wells are combined a GEM well suffix is appended to the barcode sequence
- GEM wells are subsampled and have the same effective sequencing depth
- The command requires you to create a libraries.csv file
  - Two required columns are sample_id and molecule_h5.  
  	- The molecule_h5 column is the path to the molecule_info.h5 file output by cellranger counts.
  - Optional metadata columns can be added and later viewed in the Loupe browser (e.g. treatment, timepoint, sex)
- The cellranger aggr command inputs the CSV file (containing paths to molecule_info.h5 files) and generates a variety of output including our .cloupe file so we can view clusters in the Loupe Browser
- Keep in mind this is like a quick tool to view your data.  We are still going to view a more raw version of our counts data in R.
## 6. Loupe Browser
- Your cellranger aggr output will contain .cloupe files that you can view in the Loupe Browser.
- Download the Loupe Browser on your local computer.
	- https://support.10xgenomics.com/single-cell-gene-expression/software/downloads/latest
- Transfer your .cloupe file from the cluster to your local computer.
	- We will use the scp command to do this which stands fore secure copy.
	- There are two arguments passed to this command.
		- First, where your file is currently located.
		- Second, where you want your file to go.
	- **IMPORTANT**: You have to run this command on your local computer.  So, pull up a separate tab or window for your terminal when executing this command.
		- The command will work if you do this on the cluster.
	- If we have to run this command on our local computer, how will the command know your file is actually located on a cluster? 
		- You might notice that when you login to the cluster you terminal displays something like this: m214960@mforgehn1.
		- m214960@mforgehn1 is actually specifying user@host. Our username is our m-number and the host is mforgehn1.
		- We specify file location on a cluster like so
			- user@host:/path/to/file
	- After we run our command you will be prompted to enter you RSA passcode.  Similar to when you first login to the cluster
- Usage
  ```
  scp <currentl/location/of/file> <where/you/want/to/move/file>
  ```
- Example
  ```
  scp m214960@mforgehn1:/research/labs/neurology/fryer/m214960/aggr/output/cloupe.cloupe /Users/m214960/Downloads/sample.cloupe
  ```
- Windows users will have something like this - EDITING CURRENTLY -
  ```
  scp m214960@mforgehn1:/research/labs/neurology/fryer/m214960/aggr/output/cloupe.cloupe /Users/m214960/Downloads/sample.cloupe
  ```
  - Note: When providing the path of where you want to move the file, you have an opportunity to rename it.  I could have specified something like /Users/m214960/Downloads/E19_BB.cloupe




