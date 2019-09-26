#UNIX Assignment

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

```
# transposes the columns and rows of the 'fang_et_al_genotypes.txt' file. This makes it easier (or possible) to invoke commands such as 'grep', 'awk', 'sort'$
awk -f transpose.awk original_data/fang_et_al_genotypes.txt > transposed_genotypes.txt

# prints the records, ignoring the first 3 rows (since they are headers), of the 'transposed_genotypes.txt' file and directs the output to the sort function which determines if the data are sorted by SNP IDs. 
awk 'NR > 3' transposed_genotypes.txt | sort -k1,1 -c
echo $?

# prints the records, ignoring the first row (since it's a header), of the 'snp_position.txt' file and directs the output to the sort function that determines if the records are sorted by SNP ID.
awk 'NR > 1' snp_position.txt | sort -k1,1 -c
echo $?
```

###Maize Data

```
here is my snippet of code used for data processing
```

Here is my brief description of what this code does


###Teosinte Data

```
here is my snippet of code used for data processing
```

Here is my brief description of what this code does
