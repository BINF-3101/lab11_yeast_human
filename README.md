# lab11_yeast_human

This week we are going to find orthologs between genes in the human genome and your yeast genome. We will use blast to do search the human chromosome 1. 

# Outline

[Step 1 - Folder setup](#step-1---folder-setup)

[LQ 1](#lq-1)

[LQ 2](#lq-2)

[Step 3 - Blastp](#step-3---run-blastp)

[Step 4 - Analyse Results](#step-4---compare-human-and-yeast-genes)

[LQ 4](#lq-4)

[Step 5 - Similarity](#step-5---high-similarity)

[LQ 5](#lq-5)

[LQ 6](#lq-6)

[Step 6 - Gene](#step-6---investigate-the-gene)

[LQ 7](#lq-7)

&ensp;

## Step 1 - Folder setup

&ensp;

### Step 1a - Create lab 11 folder

From your home directory, you will need to create a new folder for lab 11. 

&ensp;

### Step 1b - Copy the "clear" protein sequences

You will need to copy your protein sequences into the lab 11 folder

The file name should be "SRR00000.prot.fasta," and it is in several of the folders we have created this semester. 

&ensp;

### Step 1c - Copy human chromosome 1

There is a copy of all the proteins in human chromosome #1 in the class folder. 

The directory is ```/projects/class/binf3101_001/human_chr/chr1.fasta```. Copy this file into your new lab 11 folder

**Your folder should now contain the following files**
- SRR00000.prot.fasta
- chr1.fasta

&ensp;

# LQ 1

How many genes are annotated on human chromosome #1?

&ensp;

# LQ 2

How many genes are annotated in your yeast genome?

&ensp;

## Step 2 - Create a blast Database

We have been using fasta files as both our query and our subject in previous blast searches. This time, we want to speed up the process by creating a **blast database**. This formats the data so it is easier to search. 

To make a blast database we will use the command ```makeblastdb```

There are two required parts of the command. There is the input_file and the dbtype. 

There are two possible input types: ```nucl``` for nucleotides and ```prot``` for proteins. 

```
module load blast

makeblastdb -h

USAGE
  makeblastdb [-h] [-help] [-in input_file] [-dbype type]
...
```

**Take a look at the chromosome 1 file and determine if we are using proteins or nucleotides**

The input file will be the chromosome 1 fasta file. 

**Write the command and create the blast database**

You should now see _a lot_ of database files such as
```chr1.fasta.pdb```
```chr1.fasta.pin```
```chr1.fasta.pto```

&ensp;

## Step 3 - Run blastp

We will use blastp to search against human chromosome 1. We will use several blastp options 

- ```-db``` This refers to the database files we created above. We _only_ need to use the original fasta file name (no additional letters after fasta)
- ```-query``` This is what we are searching for. This would be our yeast protein sequences
- ```-outfmt``` We will set this to **6** again so we get it in tabular format
- ```-out``` Set this to the output file where the search results will be saved
- ```-max_target_seqs 1 -evalue 1e-50``` - We used this command last time to filter for only 1 sequence and with a reasonable e-value threshold

Create your ```blastp``` command and execute it. 

**This may take several minutes**

&ensp;

# LQ 3
What **percent** of your yeast sequences have a high similarity to a gene on chromosome 1 in humans?

&ensp;

## Step 4 - Compare human and yeast genes

&ensp;

## Step 4a - Unique human genes

It is possible that a single human gene could map to two unique fungal genes via the process of gene duplication. We want to find out **how many unique human genes** had hits to our yeast genome. 

There are many ways we could calculate this, but we will use the built-in capabilities in the command line 

To get the total number of human genes will string together several commands. 

&ensp;

### Command 1 - extract the 2nd column

The name of the human genes is in the second column of the output file
```
21701-22283(+)_1        sp|Q06830|PRDX1_HUMAN   56.701  194     81      2       3       193     6       199   9.06e-78 229
6632-9089(+)_1  sp|O15381|NVL_HUMAN     40.000  620     302     10      210     780     256     854     3.71e-144      444
6632-9089(+)_1  sp|O15381|NVL_HUMAN     39.683  252     147     2       196     442     558     809     1.58e-52       194
129247-133102(+)_1      tr|K7EMY3|K7EMY3_HUMAN  41.487  511     257     8       422     909     80      571   2.08e-114        382
34586-36194(+)_1        sp|Q9Y672|ALG6_HUMAN    39.896  386     213     7       50      428     3       376   9.93e-82 261
11624-12680(+)_1        sp|Q96LA8|ANM6_HUMAN    36.585  328     192     6       27      340     44      369   3.03e-66 211
```

To extract just the second column of the file we can use the command

```bash
cut -f2 youroutput.out
```

The ```cut``` command cuts the file into different fields (columns). The ```-f2``` asks for the 2nd column

### Command 2 - sort the results and get only unique results

If we use the cut command we will end up printing just the second column. We want to know _how many unique_ strings (words) are in this column. But before we can do that, we need to sort these strings. 

Therefore we can use the ```sort``` and ```uniq``` commands. 

### Command 3 - Count the number of lines

We have used the **w**ord **c**ount command before in the class. 
To get the total number of lines we use: 
```wc -l`` 

### String them all together

To put them all together we use 

```bash
cut -f2 youroutput.out | sort | uniq | wc -l
```

&ensp;

# LQ 4
What **percent** of the genes on chromosome 1 have a highly similar sequence in your yeast genome?

&ensp;

## Step 5 - High similarity

We will now sort the entire file based on the p-value stored in the 11th field. We will look at the top 10 hits. 

To sort the entire file on column 11 we will use ```sort -g -k11```. The ```-g``` command tells the sort program that we are looking at numbers. See below for the difference between standard sort and numeric sort. The second part ```-k11``` tells the program to sort based on the 11th column

_Differences in sort_

Imagine we have the words bats, apes, and apples. To sort these alphabetically is really easy. We start by sorting on the first letter (apes & apples), bats. Then we sort on the second letter: apes, apples, bats. 

Imagine we have the following numbers 0.0, 1e-5, and 0.005. If we sorted them like we do words we would end up with the order being

0.0
0.005
1e-5

To sort _numerically_ we would end up with
0.0
1e-5
0.005

To find the 10 genes with the lowest p-value in the results file we would use

```bash
sort -g -k11 youroutput.txt | head -10
```

Pick a gene from that top 10. 

&ensp;

# LQ 5
What is the name of that gene (column 2. Should be in the format XXXX_HUMAN)

&ensp;

# LQ 6
For the human gene selected in the previous question, what is the percent identity between the human and yeast gene (column 3)

&ensp;

## Step 6 - Investigate the gene

Head to https://www.uniprot.org/ - the catalog where I obtained the gene sequences on chromosome #1. In the search bar, search for you gene in the format XXXX_Human

&ensp;

# LQ 7

Provide the following details about your gene

1. Protein name (listed under protein)
2. Function of the gene (copy or summarize the function)
3. Expression information. Report one tissue or organ where your gene has been expressed
4. Structural information. Report one feature (family or domain) in your protein
5. Disease & Variants. In this section you will find information about your gene from experiments or genomic data. Name one type of "Disease & Variant" category found in your gene (see below)

The Uniprot database contains curated (manually input) and automated data about genes. The data falls into the categories below: 

**Involvement in disease**	Description of the disease(s) associated with the deficiency of a protein

**Natural variant**	Natural variants of the protein that are associated with one of the diseases listed above

**Allergenic properties**	Information relevant to allergenic proteins

**Biotechnological use**	Use of a specific protein in the biotechnological industry

**Toxic dose**	Lethal, paralytic, or effect dose, or lethal concentration of a toxin

**Pharmaceutical use**	Description of the use of a protein as a pharmaceutical drug

**Disruption phenotype**	Description of the effects caused by the disruption of a protein-encoding gene

**Mutagenesis**	Site which has been experimentally altered by mutagenesis











