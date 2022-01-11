Quandt Lab only: Source the funtina environment to have access to Newick Utilities. Load Java module for ASTRAL.
```
source ~/.funrc
module load jdk
```
1. Concatenate your gene trees. If using OG RAxML, use the bipartitions files as input.
```
cat RAxML_bipartitions.newall86.tsv.OrthoGroup* > all_bipartitions.tre
```
2. Contract branches with low support from the input gene trees. The example below uses Newick Utilities and excludes support values below 30. This can improve the accuracy of the output. More information [here](https://github.com/maryamrabiee/Constrained-search/blob/master/astral-tutorial.md#running-with-unresolved-gene-trees)
```
nw_ed all_bipartitions.tre 'i & b<=30' o > genetrees_30.tre
```
3. Run ASTRAL with default settings.
```
java -jar ~/astral.5.7.7.jar -i genetrees_30.tre -o astral_genetrees_30.tre
```
