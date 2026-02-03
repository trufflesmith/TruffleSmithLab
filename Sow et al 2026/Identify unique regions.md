# Identifying unique sequences using fur
## Required Files
## Required Programs
[```datasets```](https://www.ncbi.nlm.nih.gov/datasets/docs/v2/command-line-tools/download-and-install/): NCBI Datasets Command Line Tools  
[```fur```](https://github.com/EvolBioInf/fur): Find Unique Regions  
[```SeqKit```](https://github.com/shenwei356/seqkit): A cross-platform and ultrafast toolkit for FASTA/Q file manipulation  
[```bioawk```](https://github.com/lh3/bioawk): BWK awk modified for biological data

### First we download the genomes from NCBI
In this paper we used...
```console
datasets download genome accession GCA_008704415
datasets download genome accession GCA_900010015
datasets download genome accession GCA_003070745
datasets download genome accession GCA_014065205
datasets download genome accession GCA_014065215
datasets download genome accession GCA_006112555
datasets download genome accession GCA_003182015
datasets download genome accession GCA_000151645	
```
### Then we move all of the out-group and in-group species to a seperate folders. 
### Next we make a ```fur``` database.
```console
makeFurDb -t Taes -n TaesOUT -d Taes.db 
```
* -t requires a folder with your in-group sequence(s)
* -n requires a folder with your out-group sequence(s)
* -d is the output and can named anything you like
### Run ```fur```
```console
fur -d Taes.db/ > Taes_unique.fasta
```
### Filter sequences with Ns, soft masked regions, low GC content and those that are too long >2000 bp or too short <300 bp 
Remove sequences with Ns
```console
bioawk -c fastx '{ if($seq !~ /[Nn]/) {print ">"$name; print $seq}}' Taes_unique.fasta  > Nfilt_Taes_unique.fasta
```
Remove sequences with soft masked regions
```console
bioawk -c fastx '!match($seq, /[a-z]/) { print ">"$name"\n"$seq }' Nfilt_Taes_unique.fasta > soft_Nfilt_Taes_unique.fasta
```
Remove sequences with low GC content
```console
seqkit fx2tab --name --only-id --gc soft_Nfilt_Taes_unique.fasta| awk -F "\t" '{if ($2 > 40) print $1}' | xargs -n 1 sh -c 'seqkit grep --pattern "$0" soft_Nfilt_Taes_unique.fasta' > GC_soft_Nfilt_Taes_unique.fasta
```
Filter by size
```console
seqkit seq -m 300 GC_soft_Nfilt_Taes_unique.fasta > short_GC_soft_Nfilt_Taes_unique.fasta
seqkit seq -M 2000 short_GC_soft_Nfilt_Taes_unique.fasta > Taes_markers.fasta
```
Count sequences left
```console
seqkit fx2tab Taes_markers.fasta | wc -l
```
### Finally we use these sequences and the [NEB LAMP Primer Design Tool](https://lamp.neb.com/#!/) to design LAMP primers for testing. 
