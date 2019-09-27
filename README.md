#UNIX Assignment

All files formatted as specified in the assignment description are in the directory called `final_files/`. Original data unaltered from when it were downloaded are in the directory `original_data`. All other files are some kind of intermediate file I used at some point.

##Data Inspection

###Attributes of `fang_et_al_genotypes`

```
# displays the 'fang_et_al_genotypes.txt' file without wrapping the lines. This makes it more intuitive to visualize.
less -S original_data/fang_et_al_genotypes.txt

# returns the number of lines in the 'fang_et_al_genotypes.txt' file
wc -l original_data/fang_et_al_genotypes.txt

# returns the number of columns in the 'fang_et_al_genotypes.txt' file
awk -F "\t" '{print NF; exit}' original_data/fang_et_al_genotypes.txt
```

By inspecting this file I learned that:

1. The first three columns do not contain any SNP IDs. Thus, after transposition, the first three rows would be headers.
2. There are 986 columns in the file. After transposition, this translates to 986 rows. Considering the first 3 rows are headers, that means there are 983 SNP IDs in the file.
3. Since there are 2783 lines in the file, there will be 2783 columns in the transposed file. The first line/column lists the SNP IDs, so there are 2782 data points per sample. This means there are 2,734,706 total SNP data points (not considering each data point represents 2 chromosomes since these are diploid organisms).

###Attributes of `snp_position.txt`

```
# displays the 'snp_position.txt' file without wrapping the lines. This makes it more intuitive to visualize.
less -S original_data/snp_position.txt

# returns the number of lines in the 'snp_position.txt' file
wc -l original_data/snp_position.txt

# returns the number of columns in the 'snp_position.txt' file
awk -F "\t" '{print NF; exit}' original_data/snp_position.txt
```

By inspecting this file I learned that:

1. The 1st row is a header, and the 1st column are the SNP IDs. Additionally, column 3 are the chromosomes and column 4 are the SNP positions.
2. There are 984 lines in this file. After subtracting the header, there are 983 SNP IDs in this file which is equal to the number in the `fang_et_al_genotypes.txt` file.
3. There are 15 columns in this file.

##Data Processing

###Maize Data

```
awk '$3 ~ /Group|ZMMIL|ZMMLR|ZMMMR/ {print $0}' fang_et_al_genotypes.txt > maize_genotypes.txt
```

Isolates the records of samples belonging to the groups ZMMIL, ZMMLR, and ZMMMR from the `fang_et_al_genotypes.txt` data.

```
awk -f transpose.awk maize_genotypes.txt > maize_genotypes_transposed.txt
```

Uses the script provided to us to transpose the columns and rows of the maize genotype data.

```
awk 'NR > 3 {print $0}' maize_genotypes_transposed.txt | sort -k1,1 c
echo $?
```

Checks if the genotype data is still properly sorted.

```
awk 'NR > 3 {print $0}' maize_genotypes_transposed.txt > maize_genotypes_transposed_NH.txt
```

Removes the headers from the genotype data file.

```
awk 'NR > 1 {print $0}' snp_position.txt > snp_position_NH.txt
```

Removes the headers from the SNP position data file.

```
cut -f 1,3,4 snp_position_NH.txt > snp_position_NH_c1c3c4.txt
```

Extracts columns 1 (SNP ID), 3 (chromosome number), and 4 (position) from the snp position data to a new file.

```
join -1 1 -2 1 snp_position_NH_c1c3c4.txt maize_genotypes_transposed_NH.txt > maize_genotypes_snps_NH.txt
```

Adds chromosome number and position columns to the maize genotype data.


###Teosinte Data

```
awk '$3 ~ /Group|ZMPBA|ZMPIL|ZMPJA/ {print $0}' fang_et_al_genotypes.txt > teosinte_genotypes.txt
```

Isolates the records of samples belonging to the groups ZMMIL, ZMMLR, and ZMMMR from the `fang_et_al_genotypes.txt` data.

The remaining commands were very similar to the ones I used for the maize data, except I replaced maize with teosinte.

###Maize and Teosinte Data

```
for i in maize teosinte; do sed 's+?/?+?+g' ${i}_geontypes_snps_NH.txt > ${i}_genotypes_snps_NH_QM.txt
```

Iterates through files following the pattern `${i}_genotypes_snps_NH.txt` except `${i}` is replaced with either maize or teosinte. So, for `maize_genotypes_snps_NH.txt` and `teosinte_genotypes_snps_NH.txt`, the above command replaces `?/?` with `?` and creates a file similar to the input, but with `QM` added to denote a file where missing data is represented with a question mark.

```
for i in maize teosinte; do sed 's+?/?+-+g' ${i}_geontypes_snps_NH.txt > ${i}_genotypes_snps_NH_D.txt
```

Similar to the previous command, but I replaced all instances of `?/?` with `-`. I do want to point out that my options for `sed` are separated by `+` since my search pattern included a `/`. This made it easier to type and understand.

```
for i in maize teosinte; do for j in QM D; do for k in `seq 10`; do awk -v k="$k" '$2 == k && $3 !~ /unknown|multiple/' ${i}_genotypes_snps_NH_${j}.txt > ${i}_genotypes_snps_NH_${j}_chr${k}.txt & done; done; done
```

This iterates through the maize and teosinte files that each have `?` and `-` representing missing data as well as the numbers 1 to 10 and uses awk to search through those maize and teosinte files. The search compares the second column `$2` (chromosome number) to the current values of k and also ensures the 3rd column `$3` (position) doesn't match "unknown" or "multiple." It then writes all records to matching that pattern in the current iteration to a file. This produces 40 files: 20 are maize data and 20 are teosinte data. Of the 20 in each group, 10 have missing data represented by `?` and 10 represent missing data with `-`.

```
for i in maize teosinte; do for k in `seq 10`; do sort -k3,3n ${i}_genotypes_snps_NH_QM_chr${k}.txt > final_files/${i}_genotypes_snps_NH_QM_chr${k}_sorted.txt & done; done
```

This iterates through the maize and teosinte files where missing data are represented by `?` and sorts the rows by increasing order based on the 3rd column which is treated as numerical data.
I repeated this command for the maize and teosinte data where missing data are represented by `-`, but I replaced `QM` with `D` and used the option `-k3,3nr` to sort by the 3rd column as numerical data in decreasing order.

```
for i in maize teosinte; do awk '$2 ~ /unknown/ || $3 ~ /unknown/' ${i}_genotypes_snps_NH.txt > final_files/${i}_genotypes_snps_NH_unknown.txt
```

This iterates through the maize and teosinted files where missing data are represented with `?/?` and finds all records where the chromosome or position are unknown and adds them to a file.
I also used this command to create the file with all SNP IDs with multiple locations. Instead of searching for "unknown," I searched for "multiple."
