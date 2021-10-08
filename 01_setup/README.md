# Setup
## 0. For PC users only
 - Mac OS X and Linux are both Unix-like operating systems.  Many of the Mac OS X and Linux commands are the same.  This is not the case for Windows.
 - You will need to download an interface in order to be able to access the command line.  
 - Ubuntu is an app available from the Microsoft Store that will let you use Linux on a Windows computer.
 - We will be using instructions from the Biostar Handbook in addition to some Mayo specific instructions.
 	- You may have limited privliges on your Mayo computer.  We will have to request access to install a new program.
 		- Go to your **Mayo dock > My Settings > computer privileges > My User Account > Local Administrator > I need to change.... > enter SSN > sign-out/restart computer**
 	- In your Windows search bar type *Turn Windows features on or off*
 		- Launch settings panel
 		- Click the box that says *Windows Subsystem for Linux*
 		- Apply settings and restart computer
 	- Head to the Microsoft Store (app or website) and download the app Ubuntu published by Canonical Group Unlimited.  Click *Get*.
    	- You might need to create a microsoft account.
    	- https://www.microsoft.com/en-us/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab
     - Open Ubuntu and choose a username
    	  - It's a good idea to use your m-number (e.g. m123456).	  
    	  - Choose a password and then re-enter it again
## 1. Before you start, you will need...
 - VPN access
 - mForge cluster access (might take a couple days)
    - https://mctools.sharepoint.com/teams/DCISSS/HPCServices/SitePages/Request%20an%20account%20-%20mForge.aspx
 - a text editor
    - THIS CANNOT BE MICROSOFT WORD.
    - Mac: I use BBEdit and there's also a built in one called TextEdit. 
    - Windows: There is a built in one called Notepad.
      - Other options https://techwiser.com/text-editors-windows/ 
    - Note: Everything is way easier on a Mac! Can be done on Windows but some steps may be different.
- R & RStudio
   - R is a programming language you will need to learn in order to find differentially expressed genes in RNA-seq data.
   - There are many websites to help you learn a brief introduction into R.  My favorite is datacamp because it's interactive and I'm a very visual learner.
     - Datacamp is free for students: https://www.datacamp.com/groups/classrooms
   - R is the very last step for this Intro_RNA_Seq tutorial.  So, start learning this on the side as you go through the guide.
     - Don't worry about memorizing every little detail.
     - Many things we will learn in R are package specific and will not be taught in the course.  Just know the basics (vectors, dataframes, matrices, subsetting). 
   - Download R and THEN RStudio
     - Ultimately we will be using RStudio but R must first be downloaded.
     - R: https://cran.microsoft.com/
     - RStudio Desktop: https://www.rstudio.com/products/rstudio/download/
## 2. What is a cluster?
 - Watch from 4:50 → 28:36 (feel free to increase the speed)
    - https://www.youtube.com/watch?v=mLdHvl-KSKo
 - We will obviously be using the Mayo cluster, not Yale’s cluster, but they function the same way and have a great explanation.
## 3. Accessing the cluster
 - Make sure you are connected to the Mayo VPN
 - Access your terminal
    - Mac: launchpad → terminal
    - Windows: Ubuntu
 - If you are new to using Unix based operating systems and navigating through directories using your terminal, use this link for a cheat sheet or watch a quick YouTube video!  We will also go over using the command line together.
    - YouTube: https://www.youtube.com/watch?v=MnY0K-3_Fjk
    - Command cheat sheet: https://phoenixnap.com/kb/linux-commands-cheat-sheet
 - When you open your terminal, a command line interpretor called shell is launched
    - Shell is also a language of it's own
    - Some of the top open source shells are bash, tcsh, ksh, zsh and fish.
    - Usually, the default shell is bash.  All the commands and scripts we right will be in bash.
 - For the manual on any command type 'man commandYouWant'.  For example, look at the manual for the ls command.
```
man ls
```
 - The most useful commands: pwd, ls, cd, scp, mv, rm, ssh
 - From your terminal, enter the command below.  This is how we connect to the server.
```
ssh mforgehn1.mayo.edu
```
 - Enter your one-time RSA SecurID passcode :calling:.  It will not display the characters as you type.
   - It will ask if you want to continue connecting; enter yes.
 - Your home screen will take you to your working directory, which is also referred to as ~. The word ‘folder’ and ‘directory’ are used interchangeably. Navigate to your Fryer lab folder.
```
cd /research/labs/neurology/fryer/your_m_number
```
- Settings
  - Setup mayobiotools by running the command below
  - NOTE: This will reset your .bash_profile and .bashrc file
   - Option 1
     ```
     /usr/local/biotools/bin/newdotfiles
     ```
   - Option 2
     ```
     /mforge/local/biotools/bin/newdotfiles
     ```
  - To make your life easier
    ```
    # create alias to fryer lab folder
    alias fryer="cd /research/labs/neurology/fryer/YOUR_M_NUMBER/"
    ```
## 4. Setup your conda environment
 - Conda environments allow you to store all your downloaded programs/packages into an ‘environment’.  You can share these environments with other people.  
 - So, if I wanted to analyze a dataset from a paper and reproduce the results, I would need to use the same programs (and the same versions of each program).  In this case I could just use the conda environment used in the paper.
 - We will setup our own conda environment as we need the programs. Make sure you are within your fryer lab m-folder (e.g. /research/labs/neurology/fryer/m214960).
 - Most things you cannot download to your home directory (/home/mayo/your_m_number).  It only has 2 GB of space. The files we will be downloading are 100+ GB which is why we must go to our fryer lab folder (research/labs/neurology/fryer/your_m_number).
 - Installing miniconda can be tedious. Download this bash script to start installing miniconda.
```
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
```
 - Run the bash file.
```
sh Miniconda3-latest-Linux-x86_64.sh
```
 - Accept the license (keep pressing enter or scrolling).
 - :exclamation:**IMPORTANT: change the location to /research/labs/neurology/fryer/your_m_number/miniconda3**:exclamation:
    - If you don’t do this, the installation will use up all your space in your home directory (~) before it’s finished installing.
 - Initialize with init
 - Close and re-open your shell.  You can type 'logout' and then use ssh to log back in.
 - Navigate back to your fryer lab folder and test if conda is working properly.
```
conda
```
 - We are going to add some channels that conda can install packages from
```
conda config --add channels bioconda
conda config --add channels conda-forge
```
 - If you ever need to update conda in the future run the command below.
 ```
 conda update -n base conda
 ```
 - If you ever need to initialize the conda env
  ```
 miniconda3/condabin/conda init
  ```
## 5. SoGE
 - SoGE (Son of Grid Engine) is used for scheduling jobs
 - A “job” is a program/script that contains the work that needs to be done
 - Jobs are submitted to the scheduler via queues
 - 5 queues are available for general use on the NCSA cluster
    - 1 hour, 1 day, 4 days, 7 days, 30 days
    - If a job does not complete before the CPU time limit it will be terminated by the scheduler
 - A cluster/job script has three main parts
    - Header – A set of SoGE commands to control the grid job.  Using a header is a great way to document your run.
    - Trap command – cleans up work space after job runs
    - Body – the program you want to run
 - We will only focus on the header and the body.
## 6. Creating a script for a job
 - Use the vi text editor within Linux to create/modify/copy&paste scripts.
 - vi is a bit tricky to work with.  I recommend watching a quick YouTube video.
   - vi command cheat sheet: https://cheatography.com/ericg/cheat-sheets/vi-editor/
   ```
   vi example.sh
   ```
 - To practice submitting a job visit the link
   - https://mctools.sharepoint.com/teams/DCISSS/HPCServices/SitePages/Submit%20job.aspx
 - Use the example code provided to copy and paste into your file
 - Here are some more advanced options and useful information you can come back to later
   - https://mctools.sharepoint.com/teams/DCISSS/HPCServices/SitePages/Working%20with%20jobs.aspx
## 7 . Submitting a job
 - Use the qsub Linux command to send a job to OGS:
   ```
   qsub example.sh
   ```
 - Tips
    - Default memory is 2 GB, if unspecified in the script header
    - If you must load a module in a job you submit, add this to your header
      #$ -V
 - Checking on submitted jobs
    - Use the qstat Linux command to see your jobs
    ```
    qstat
    ```
 - Use qdel command to kill job
   ```
   qdel <job number>
   ```
 - You get lots of empty files from running jobs.  The command below will delete any empty files in your current directory.
   ```
   find . -size 0 -delete
   ```
- Another useful command to extract all files out of subfolders
  ```
  find . -mindepth 2 -type f -print -exec mv {} . \;
  ```
## 8. Optional terminal formatting
 - The Biostar Handbook provides some fun terminal formatting.  If interested, run the commands below **ONLY ONCE**:exclamation:. People using Ubuntu will also have to go to **properties > colors** to change background and text colors.  I recommend making your background white and text black.
   ```
   curl http://data.biostarhandbook.com/install/bash_profile.txt >> ~/.bash_profile
   curl http://data.biostarhandbook.com/install/bashrc.txt >> ~/.bashrc
   ```
 - For the changes to take effect run the command below.
   ```
   source ~/.bash_profile
   ```
 - Ubuntu users do not have the tab completion feature.  If you would like to add this feature you can run the commands below.
   ```
   sudo apt update
   sudo apt install bash-completion
   ```
