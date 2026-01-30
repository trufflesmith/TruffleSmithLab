# Identifying unique sequences using fur
## Required Files
## Required Programs
[```fur```](https://github.com/EvolBioInf/fur): Find Unique Regions  
[```SeqKit```](https://github.com/shenwei356/seqkit): A cross-platform and ultrafast toolkit for FASTA/Q file manipulation  
[```bioawk```](https://github.com/lh3/bioawk): BWK awk modified for biological data

### First we download the genomes from NCBI
In this paper we used...
```
```
### Then we move all of the out-group and in-group species to a seperate folders. 
### Next we make a ```fur``` database.
```
makeFurDb -t Taes -n TaesOUT -d Taes.db 
```
* -t requires a folder with your in-group sequence(s)
* -n requires a folder with your out-group sequence(s)
* -d is the output and can named anything you like
### Run ```fur```
```

```
### Filter sequences with Ns, soft masked regions, low GC content and those that are too long >2000 bp or too short <300 bp 
Remove sequences with Ns
```
bioawk -c fastx '{ if($seq !~ /[Nn]/) {print ">"$name; print $seq}}' Taes_unique.fasta  > Nfilt_Taes_unique.fasta
```
Remove sequences with soft masked regions
```
bioawk -c fastx '!match($seq, /[a-z]/) { print ">"$name"\n"$seq }' Nfilt_Taes_unique.fasta > soft_Nfilt_Taes_unique.fasta
```
Remove sequences with low GC content
```
seqkit fx2tab --name --only-id --gc input.fasta| awk -F "\t" '{if ($2 > 40) print $1}' | xargs -n 1 sh -c 'seqkit grep --pattern "$0" input.fasta' > output.fasta
```
Filter by size
```
seqkit seq -m 300 input.fasta > output.fasta
seqkit seq -M 2000 input.fasta > output.fasta
```
Count sequences left
```
seqkit fx2tab your_file.fasta | wc -l
```
