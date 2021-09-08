## Purpose: Identify, filter, and count CAZyme families and subfamilies from a protein fasta.

1. Find CAZymes with coverage > 0.45 and e-value <1e-17 from proteins.fasta file using HMMER - these parameters are suggested for fungi. This takes about 3-5 hours depending on CAZy content. I'm not sure multiple nodes/tasks if you're running a slurm really helps.

```
source ~/.hmmer
run_dbcan.py --out_pre yourspecies --hmm_cov 0.45 --hmm_eval 1e-17 --hmm_cpu 12 --db_dir /pl/active/fungi1/dbcan_db/ yourinputfile.proteins.fa protein
```

2. Remove header line
```
tail -n +2 hmmer.out > hmmer_step1.out
```
3. Remove all columns except cazy name and gene
```
awk '{ print $1, $3}' hmmer_step1.out > hmmer_step2.out
```
4. Remove any duplicate annotations on the same gene
```
awk '!x[$0]++' hmmer_step2.out > hmmer_step3.out
```
5. Generate counts of cazys
```
 awk '{print $1}' hmmer_step3.out | sort | uniq -c > hmmer_step4.out
 ```
 6. Swap columns
 ```
 awk ' { t = $1; $1 = $2; $2 = t; print; } ' hmmer_step4.out > hmmer_step5.out
 ```
 7. Add species name as column header (format as G.species no space).
```
 awk 'BEGIN{print "Species\tG.species"}1' hmmer_step5.out > yourspecies_cazy_counts.out
 ```
 8. Once you have all of your yourspecies_cazy_counts.out files, put them into a single folder. 

### Process all CAZyme annotations into one file.

1. Convert *.out files to tab-delimited
```
ls *.out | parallel 'csvtk space2tab {} > {.}.tsv'
```
2. Sort all of the files in the directory.
```
for file in *.tsv;
  do 
sort  < ${file} > ${file}_sorted; done
```
4. Make a new directory and move the old .tsv files to a different folder
```
mkdir ../presort_cazy
mv *.tsv ../presort_cazy
```
5. Get the names of the file in the directory
```
echo $(dir)
```
7. Get unique IDs from columns. Check if anything is weird. 
```
cut -f 1 *_sorted | sort | uniq | tee columnkey.txt
head columnkey.txt
```
The file should look like this
```
```
8. Join all files and replace blanks with 0s. Make sure column key is first in the order before all other file names. 
```
csvtk join -t -k columnkey.txt file1 file2 --na 0 > all_cazys_counts_final.tsv
```
9. Convert to csv is necessary
```
csvtk tab2csv all_cazys_counts_final.tsv > all_cazys_counts_final.csv
```
