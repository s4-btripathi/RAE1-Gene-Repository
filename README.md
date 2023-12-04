# Lab 15: My Gene-Repository
Hi. Welcome to my repository! This serves as an outline for my research term paper for evolution RAE 1 Gene within the deuterostomes. It consists of procedures, tools and commands I used to obtain the results I got. 

# Part 1 - Create a Directory
To begin working on the gene analysis, lets create directory where we can input all of the information in a folder that is related to the gene RAE1. Create a folder for RAE 1 gene using the following command:  
```bash
mkdir /home/ec2-user/RAE1
```
Following command will create a directory in ec2-user folder in your readme. 

# Part 2 - Download your query protein 
Lets get the CDC40 gene as our query sequence by using the following command :
```bash
ncbi-acc-download -F fasta -m protein NP_003601.1
```
This command retrieves the RAE1 gene sequence from NCBI database and converts it to a fasta file. 

# Part 3 - Perform the BLAST search 
Perform a BLAST search usig the query protein. Use the following command: 
```bash
blastp -db ../allprotein.fas -query NP_003601.1.fa -outfmt 0 -max_hsps 1 -out RAE1.blastp.typical.out
```
# Part 4 - Perform a BLAST search, and request tabular outout 
The command below will generate a particular output format for the analysis of RAE1 gene. 
```
blastp -db ../allprotein.fas -query NP_003601.1.fa  -outfmt "6 sseqid pident length mismatch gapopen evalue bitscore pident stitle"  -max_hsps 1 -out RAE1.blastp.detail.out
```


