#UNIX Assignment

##Data Inspection

###Attributes of `fang_et_al_genotypes`

```
# displays the 'fang_et_al_genotypes.txt' file without wrapping the lines. This makes it more intuitive to visualize.
less -S fang_et_al_genotypes.txt

# returns the number of lines in the 'fang_et_al_genotypes.txt' file
wc -l fang_et_al_genotypes.txt

# returns the number of columns in the 'fang_et_al_genotypes.txt' file
awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt
```

By inspecting this file I learned that:

1. The first three columns do not contain any SNP IDs. Thus, after transposition, the first three rows would be headers.
2. There are 'x' columns in the file. After transposition, this translates to 'x' rows. Considering the first 3 rows are headers, that means there are 'x' - 3 SNP IDs in the file.
3. Since there are 'y' lines in the file, there will be 'y' columns in the transposed file. The first line/column lists the SNP IDs, so there are 'y' - 1 data points per sample. This means there are ('y' - 1) * ('x' - 3) total SNP data points (not considering each data point represents 2 chromosomes since these are diploid organisms).

###Attributes of `snp_position.txt`

```
# displays the 'snp_position.txt' file without wrapping the lines. This makes it more intuitive to visualize.
less -S snp_position.txt

# returns the number of lines in the 'snp_position.txt' file
wc -l snp_position.txt

# returns the number of columns in the 'snp_position.txt' file
awk -F "\t" '{print NF; exit}' snp_position.txt
```

By inspecting this file I learned that:

1. The 1st row is a header, and the 1st column are the SNP IDs. Additionally, column 3 are the chromosomes and column 4 are the SNP positions.
2. There are 'z' lines in this file. After subtracting the header, there are 'z' - 1 SNP IDs in this file which is equal to the number in the `fang_et_al_genotypes.txt` file.
3. There are 15 columns in this file.

##Data Processing

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
