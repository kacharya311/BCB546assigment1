﻿﻿﻿﻿
# Unix Assignment
## -Kavi Raj Acharya

## Data Inspection
Inspection of data to figure out the header and number of rows

```
`head -n 5 snp_position.txt`

`tail -n 5 snp_position.txt`

`head -n 5 fang_et_al_genotypes.txt`

`tail -n 5 fang_et_al_genotypes.txt`

`wc fang_et_al_genotypes.txt`

`wc snp_position.txt` 
```
## Sorting before joining them
#### To prevent the header from sorting with the data, I would create a file with header and then add sorted data to the file. 
```
`head -n 1 snp_position.txt > sorted_snp1.txt`

`tail -n+2 snp_position.txt | sort  -k1,1 >>sorted_snp1.txt`
```
## Rearrangement of the columns in the file according to final output
```
`awk 'BEGIN {FS="\t"; OFS="\t"} {print $1, $3, $4, $2, $5,$6,$7,$8,$9,$10,$11,$12,$13,$14,$15}' sorted_snp1.txt >sorted_snp1_column.txt`
```
# Zea mays (Maize) datasets

## Separating maize data from genotype dataset
 ```
`head -n 1 fang_et_al_genotypes.txt > geno_maize.txt`

`grep -E "(ZMMIL|ZMMLR|ZMMMR)" fang_et_al_genotypes.txt >>geno_maize.txt`
```
## Transposing the the separated maize datasets
```
`awk -f transpose.awk geno_maize.txt > transposed_geno_maize.txt`
```
## Sorting the data before joining 
#### As the second and third row do not contain genotype data,  the second command is used to skip them while keeping the header
```
`head -n 1 transposed_geno_maize.txt > maize_sorted1.txt`

`tail -n+4 transposed_geno_maize.txt | sort  -k1,1 >>maize_sorted1.txt`
```
## Joining the sorted files
```
`join -1 1 -2 1 --header  sorted_snp1_column.txt maize_sorted1.txt > maize_joined.txt`

sed -i "s/ /\t/ g maize_joined.txt
```

## Creating 10 files (1 for each chromosome) with SNPs ordered based on increasing position values and with missing data encoded by '?'
### Creating maize directory  and creating Chromo\_(Chromosome number)\_ascending.txt for each chromosome with just the header row this symbol: ?
```
 `mkdir maize
 
 head -n 1 maize_joined.txt | awk 'BEGIN {FS="\t"; OFS="\t"}{ for (i=1; i <= 10; i++) print $0 >"maize/Chromo_"i"_ascending.txt"}'`
 ```
 #### - Sorting  the data from the joined file,  without header,  based on Chromosome number which then is positioned in ascending order. Then the data is piped into awk which saves data for each chromosome number into respective files.
```
`tail -n +2 maize_joined.txt | sort -k2,2 -k3,3n | awk 'BEGIN {FS="\t"; OFS="\t"}{ if($2 >= 1 && $2 <= 10) {  print  >>"maize/Chromo_"$2"_ascending.txt"}}'`
```


## Creating 10 files (1 for each chromosome) with SNPs ordered based on decreasing position values and with missing data encoded by this symbol: -
#### - Creating files with name Chromo\_(Chromosome number)\_descending.txt for each chromosome with just the header row
```
 `head -n 1 maize_joined.txt | awk 'BEGIN {FS="\t"; OFS="\t"}{ for (i=1; i <= 10; i++) print $0 >"maize/Chromo_"i"_descending.txt"}'`
 ```
#### - Sorting the data from the joined file (excluding header) on descending order of Chromosome number, then sed replaces ? with -. The data  then is piped into awk which saves data for each chromosome number into respective files.
```
`tail -n +2 maize_joined.txt | sort -k2,2 -k3,3rn | sed 's/?/-/g' | awk 'BEGIN {FS="\t"; OFS="\t"}{ if($2 >= 1 && $2 <= 10) {  print  >>"maize/Chromo_"$2"_descending.txt"}}'`

```
## Creating  file for unknown position
#### - Creating files with name Chromo\_unknown.txt 
```
 `head -n 1 maize_joined.txt >maize/Chromo_unknown.txt`
 ```
#### - Saves all records with unknown position into the file
```
`tail -n +2 maize_joined.txt |  awk 'BEGIN {FS="\t"; OFS="\t"}{ if($2 == "unknown") {  print  >>"maize/Chromo_unknown.txt"}}'`
```

## Creating  file for multiple positions
#### - Creating  files with name Chromo\_multiple.txt 
```
 `head -n 1 maize_joined.txt >maize/Chromo_multiple.txt`
 ```
#### - Saving all records with multiple position into the file
```
`tail -n +2 maize_joined.txt |  awk 'BEGIN {FS="\t"; OFS="\t"}{ if($2 == "multiple") {  print  >>"maize/Chromo_multiple.txt"}}'`
```


# Teosinte datasets

## Filtering Teosinte data from genotype dataset
```
 
`head -n 1 fang_et_al_genotypes.txt > geno_teosinte.txt`

`grep -E "(ZMPBA|ZMPIL|ZMPJA)" fang_et_al_genotypes.txt >>geno_teosinte.txt`
```
## Transposing the filtered data
```
`awk -f transpose.awk geno_teosinte.txt > transposed_geno_teosinte.txt`
```
## Sorting the data before join 
#### The second and third row do not contain genotype data, so the second command skips them while keeping the header
```
`head -n 1 transposed_teosinte_genotypes.txt > teosinte_sorted.txt`

`tail -n+4 transposed_teosinte_genotypes.txt | sort  -k1,1 >>teosinte_sorted.txt`
```
## Joining the two files
```
join -1 1 -2 1 --header  sorted_snp1_column.txt teosinte_sorted.txt > teosinte_joined.txt`

sed -i "s/ /\t/g" teosinte_joined.txt
```

## Creating 10 files (1 for each chromosome) with SNPs ordered based on increasing position values and with missing data encoded by this symbol: ?

#### - Creating teosinte directory and  files with name Chromo\_(Chromosome number)\_ascending.txt for each chromosome with just the header row
```
mkdir teosinte
`head -n 1 teosinte_joined.txt | awk 'BEGIN {FS="\t"; OFS="\t"}{ for (i=1; i <= 10; i++) print $0 >"teosinte/Chromo_"i"_ascending.txt"}'`
 ```
#### - Sorting the data from the joined file (excluding header)  in descending order based on Chromosome number, then piping it into awk which saves data for each chromosome number into respective files.
```
`tail -n +2 teosinte_joined.txt | sort -k2,2 -k3,3n | awk 'BEGIN {FS="\t"; OFS="\t"}{ if($2 >= 1 && $2 <= 10) {  print  >>"teosinte/Chromo_"$2"_ascending.txt"}}'`
```


## Creating 10 files (1 for each chromosome) with SNPs ordered based on decreasing position values and with missing data encoded by this symbol: -
#### - Creating files with name Chromo\_(Chromosome number)\_descending.txt for each chromosome with just the header row
```
 `head -n 1 teosinte_joined.txt | awk 'BEGIN {FS="\t"; OFS="\t"}{ for (i=1; i <= 10; i++) print $0 >"teosinte/Chr_"i"_descending.txt"}'`
 ```
#### - Sorting the data from the joined file (excluding header) in descending order of position based on Chromosome number. Then,  sed replaces ? with -, and  finally we pipe the data into awk which saves data for each chromosome number into respective files.
```
`tail -n +2 teosinte_joined.txt | sort -k2,2 -k3,3rn | sed 's/?/-/g' | awk 'BEGIN {FS="\t"; OFS="\t"}{ if($2 >= 1 && $2 <= 10) {  print  >>"Chromo_"$2"_descending.txt"}}'`
```


## Creating  file for unknown position
#### - Creating files with name Chromo\_unknown.txt 
```
 `head -n 1 teosinte_joined.txt > teosinte/Chromo_unknown.txt`
 ```
#### - Saving all data with unknown position into the file
```
`tail -n +2 teosinte_joined.txt |  awk 'BEGIN {FS="\t"; OFS="\t"}{ if($2 == "unknown") {  print  >>"teosinte/Chromo_unknown.txt"}}'`
```


## Creating  file for multiple positions
#### - Creating files with name Chromo\_multiple.txt 
```
 `head -n 1 teosinte_joined.txt >teosinte/Chromo_multiple.txt`
 ```
#### - Saving all data with multiple position into the file
```
`tail -n +2 teosinte_joined.txt |  awk 'BEGIN {FS="\t"; OFS="\t"}{ if($2 == "multiple") {  print  >>"teosinte/Chromo_multiple.txt"}}'`
```













