This folder contains generic scripts used for common analyses (e.g., making phylogenetic trees). See below for [HiperGator](#HiperGator-Tips-and-Tricks) and [Github tips](#Github-Tips-and-Tricks) and tricks!

# Github Tips and Tricks

# HiperGator Tips and Tricks
## Job resources
When submitting a job, it may take a while to run becuase all of the resources have been partitioned for someone else.  
To check what resources are being used, simply run
```console
squeue -A plantpath
```
The department has additonal burst resources you can use when the queue is long. Try using ``plantpath-b`` instead of ``plantpath``
```console
#!/bin/bash
#SBATCH --account=plantpath
#SBATCH --qos=plantpath-b
```
## Using R
To use R on the Hipergator simply load R
```console
ml R
```
and open R
```console
R
```
Now you can use R just as you would on your computer! Make sure you check and/or set your working directory
```R
getwd()
```
Check the installed libraries and install any that you need
```R
install.packages("adegenet")
installed.packages()
```
Using R just on the command-line will be much faster than your computer. However, if commands are still taking a long time, you can create an R script and run it as a job. To do this you need two files, 1) an R script, and 2) a shell script.  
  
First you write your R script as you would on your computer. See `parallel_pca.R` below as an example.
```R
library(adegenet)
library(parallel)
setwd("/blue/matthewsmith/a.sow/Sebastian_Data") #always set your working directory
data <- readRDS("Fp_filtered_genlight.rds")
parallel_PCA <- glPca(data,parallel = TRUE,nf=10)
saveRDS(parallel_PCA,"parallel_Fp_PCA.rds") #make sure to save any outputs as an rds so you don't have to rerun your scripts 
```
Now, to submit this job, you need to write a shell script that loads R and runs the script.
```console
#!/bin/bash
#SBATCH --account=plantpath
#SBATCH --qos=plantpath
#SBATCH --job-name=parallel_pca
#SBATCH --mail-type=END,FAIL
#SBATCH --mail-user=a.sow@ufl.edu
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=8
#SBATCH --mem=16gb
#SBATCH --time=8:00:00
#SBATCH --output=parallel_PCA_%j.out
pwd; hostname; date

ml R
Rscript parallel_pca.R
```
