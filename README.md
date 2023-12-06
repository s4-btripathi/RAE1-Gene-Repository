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
# Part 5 - Filter the BLAST results to obtain homologs with high scores
Use the following command with e value of 1e-35, which will aid in finding out genes similar to RAE1 found in the species such as homosapiens. 
```bash
awk '{if ($6< 1e-35)print $1 }' globins.blastp.detail.out > RAE1.blastp.detail.filtered.out
```
Referring to table 1 in my research. Use the following command to obtain the number of paralogs for the RAE evolutionary study. 
```bash
grep -o -E "^[A-Z]\.[a-z]+" RAE1.blastp.detail.filtered.out  | sort | uniq -c
```
# Part 5 - The Gene Family Sequence Alignment 
INSTALLING SOFTWARES
We will be installing softwares to convert the text based sequence alignment to HTML file using aha. This will be installed with conda.
Then use a2ps to turn the html file into a postscript file. This file later could be viewed into pdf. yum, the package manager will be installed simultaneously. 
Following are the commands to be run to install software. execute the commands once to run . 
```bash
conda install -y -n base -c conda-forge aha
```
```bash
sudo yum install -y a2ps
```
```bash
pip install buddysuite
```
# Part 5 - To view the files as pdfs, install a VS code extension 
Install the extension vscode-pdf by tomoki1207 . 

# Part 6 - Aligning mRNA export factor and Bub3 gene family memmbers
lets create a folder for the mRNA export factor and Bub3 gene family. Use the following command 
```bash
mkdir ~/labs/lab04-$MYGIT/RAE1
```
```bash
cd ~/labs/lab04-$MYGIT/RAE1
```
There are sequences in the BLAST output file, use the following seqkit command to get the sequences: 
```bash
seqkit grep --pattern-file ~/labs/lab03-$MYGIT/RAE1/RAE1.blastp.detail.filtered.out ~/labs/lab03-$MYGIT/allprotein.fas > ~/labs/lab04-$MYGIT/RAE1/RAE1.homologs.fas
```
# Part 7 - Perform a Global Multiple Sequence Alignment in MUSCLE
Our goal is to align the sequences using the entire length. The resulting alignment will provide necessary information as to which positions are homologs to each other among RAE1 proteins and which positions contain insertions or deletions with regard to other sequences. 
Using multiple sequence alignment program (MUSCLE), use the following command to make multiple sequence alignment: 
``` bash
muscle -in ~/labs/lab04-$MYGIT/RAE1/RAE1.homologs.fas -out ~/labs/lab04-$MYGIT/RAE1/RAE1.homologs.al.fas
```
Now, take a look at the alignment in ALV using the following command: 
```bash
alv -kli  ~/labs/lab04-$MYGIT/RAE1/RAE1.homologs.al.fas | less -RS
```
Lets also try the "majority" option. 
```bash
alv -kli --majority ~/labs/lab04-$MYGIT/RAE1/RAE1.homologs.al.fas | less -RS
```
To view the alignment into a pdf, use the following command 
```bash
muscle -in ~/labs/lab04-$MYGIT/RAE1/RAE1.homologs.fas -html -out ~/labs/lab04-$MYGIT/RAE1/RAE1.homologs.al.html
```
```bash
alv -ki -w 100 ~/labs/lab04-$MYGIT/RAE1/RAE1.homologs.al.fas | aha > ~/labs/lab04-$MYGIT/RAE1/RAE1.homologs.al.html
```
```bash
a2ps -r --columns=1 ~/labs/lab04-$MYGIT/RAE1/RAE1.homologs.al.html -o ~/labs/lab04-$MYGIT/RAE1/RAE1.homologs.al.ps
```
```bash
​​ps2pdf ~/labs/lab04-$MYGIT/RAE1/RAE1.homologs.al.ps ~/labs/lab04-$MYGIT/RAE1/RAE1.homologs.al.pdf
```
# Part 8 - Gathering information about the alignment 
Command below is used to calculate the length of the alignment: 
```bash
alignbuddy  -al  ~/labs/lab04-$MYGIT/RAE1/RAE1.homologs.al.fas
```
To calculate the length of the alignment removing any columns with gaps. Use the following command: 
```bash
alignbuddy -trm all  ~/labs/lab04-$MYGIT/RAE1/RAE1.homologs.al.fas | alignbuddy  -al
```
To calculate the legnth of the alignment removing the invariant also also known as the conserved positions, use the following command: 
```bash
alignbuddy -dinv 'ambig' ~/labs/lab04-$MYGIT/RAE1/RAE1.homologs.al.fas | alignbuddy  -al
```
# Part 9 - the Average Percent Identity Calculation 
We will be using the t_coffee command to calculate the average percent identity along all sequences
```bash
t_coffee -other_pg seq_reformat -in ~/labs/lab04-$MYGIT/RAE1/RAE1.homologs.al.fas -output sim
```
Then, we will repeat calculating the average percent identity using alignbuddy. Use the following command: 
```bash
alignbuddy -pi ~/labs/lab04-$MYGIT/RAE1/RAE1.homologs.al.fas | awk ' (NR>2)  { for (i=2;i<=NF  ;i++){ sum+=$i;num++} }
     END{ print(100*sum/num) } '
```
# Part 10 - Building a phylogenetic tree for RAE1 homologs from sequence data 
For this part, the software IQ-TREE is used to find the most optimal phylogenetic tree from from a sequence alignment. To import out data, lets create a directory using the following command: 
```bash
mkdir /home/ec2-user/RAE1
cd ~/labs/lab05-$MYGIT/RAE1 
```
note: cd is used to change the directory

To proceed, we will be copying the alignment from RAE1 folder from lab04 into the lab 05. IQ Tree will put the output files in the same directory as the input file. Use the command to go to this directory: 
```bash
cp ~/labs/lab04-$MYGIT/RAE1/RAE1.homologs.al.fas ~/labs/lab05-$MYGIT/RAE1/RAE1.homologs.al.fas   
```
We will now be using the IQ-Tree to obtain the maximum likelihood tree estimation using the following command: 
```bash
iqtree -s ~/labs/lab05-$MYGIT/RAE1/RAE1.homologs.al.fas -bb 1000 -nt 2 
```
Softare will calculate the optimal amino acid substitution model and amino acid frequences. It will then create a tree search which is done so by estimating branch lengths. 

# Part 11 - Rooting the Ideal Phylogeny 
We will begin by using a type of rooting called midpoint. the software gotree is used to reroot the tree. To do so, use the following command: 
```bash
gotree reroot midpoint -i ~/labs/lab05-$MYGIT/RAE1/RAE1.homologs.al.fas.treefile -o ~/labs/lab05-$MYGIT/RAE1/RAE1.homologs.al.mid.treefile
```
This will give us optimal midpoint rooted tree 

The tree can be viewed with the following command: 
```bash
nw_order -c n ~/labs/lab05-$MYGIT/RAE1/RAE1.homologs.al.mid.treefile  | nw_display -
```
This command can be used to view the ASCII tree 

Now, in order to make the tree better for view, following command with nw_order can be used 
```bash
nw_order -c n ~/labs/lab05-$MYGIT/RAE1/RAE1.homologs.al.mid.treefile | nw_display -w 1000 -b 'opacity:0' -s  >  ~/labs/lab05-$MYGIT/RAE1/RAE1.homologs.al.mid.treefile.svg -
```
what the nw_order does is that it orders the clades by numbers of descendents. Meaning this will make large trees easier to look at. This may however end up as limitation in research as it introduces bias. 

# Part 12 - Branch Lengths
Use the following command to determine between phylograms and cladograms and visualizing short branch: 
```bash
nw_order -c n ~/labs/lab05-$MYGIT/RAE1/RAE1.homologs.al.mid.treefile | nw_topology - | nw_display -s  -w 1000 > ~/labs/lab05-$MYGIT/RAE1/RAE1.homologs.al.midCl.treefile.svg -
```
The tree shown by the nw_display is a phylogram which means the length of each branch are to be proportional to the substitution numbers in the sequence along that branch. Clades can be hard to see in short branch. It is easier to view in cladogram. Use the following command to do so :
```bash
nw_order -c n ~/labs/lab05-$MYGIT/RAE1/RAE1.homologs.al.mid.treefile | nw_topology - | nw_display -s  -w 1000 > ~/labs/lab05-$MYGIT/RAE1/RAE1.homologs.al.midCl.treefile.svg -
```
# Part 13 - Outgroup Rooting
To specify outgroup , use the following command: 
```bash
nw_reroot subphyla.tre Echinodermata >subphyla.echinoderm_reroot.tre
nw_display ~/labs/lab05-$MYGIT/subphyla.echinoderm_reroot.tre
```
By doing so, it reroots the tree with branching of echinoderms as the starting point. But the true root is the ancestor of Echninoderms/Hemichordates as the deuterostomes.

# Part 14 - Printing RAE1 Alignment into PDF to view 
Lets install latex suit and use the Rscript to print the alignment with the following command: 
```bash
sudo yum install texlive
```
```bash
Rscript --vanilla ~/labs/lab05-$MYGIT/installMSA.R
```
```bash
Rscript --vanilla ~/labs/lab05-$MYGIT/plotMSA.R  ~/labs/lab05-$MYGIT/RAE1/RAE1.homologs.al.fas ~/labs/lab05-$MYGIT/RAE1/RAE1.homologs.al.fas.pdf
```
# PART 15 - Reconciling the mRNA export factor and Bub3 gene family with the species tree
To store the information, using the following command to create a directory: 
```bash
cd ~/labs/lab06-$MYGIT/AKAP
```
First, check if the midpoint rooted tree is present in the folder using the following command: 
```bash
ls ~/labs/lab06-$MYGIT/RAE1/RAE1.homologs.al.mid.treefile
```
Now, midpoint rooted tree will be used for your own gene family. To ensure you are using your gene, use the following command:
```bash
mkdir ~/labs/lab06-$MYGIT/RAE1
```
Now, make a copy of this gene tree from earlier (lab 5) into your current directory folder using the following command: 
```bash
cp ~/labs/lab05-$MYGIT/RAE1/RAE1.homologs.al.mid.treefile ~/labs/lab06-$MYGIT/RAE1/RAE1.homologs.al.mid.treefile
```
# Part 16 - Reconcile the Gene and Species Tree using the NOTUNG
Using the following command, perform reocnciliation: 
```bash
java -jar ~/tools/Notung-3.0-beta/Notung-3.0-beta.jar -s ~/labs/lab06-$MYGIT/species.tre -g ~/labs/lab06-$MYGIT/RAE1/RAE1.homologs.al.mid.treefile --reconcile --speciestag prefix --savepng --events --outputdir ~/labs/lab06-$MYGIT/RAE1/
```
This should result in a reconciliation tree based on your gene family. 

Now, look at the duplications and losses within your specific tree using the following command which looks importantly at duplications and losses: 
```bash
nw_display ~/labs/lab06-$MYGIT/species.tre
```
To view the internal nodes that do not have formal taxonomic names, use the following command: 
```bash
grep NOTUNG-SPECIES-TREE ~/labs/lab06-$MYGIT/RAE1/RAE1.homologs.al.mid.treefile.reconciled | sed -e "s/^\[&&NOTUNG-SPECIES-TREE//" -e "s/\]/;/" | nw_display -
```
This command allows us to know which lineages the internal nodes come from 

Now, Generate RecPhyloXML object and view the gene within species tree through thirdkind. Use the following command: 
```bash
python2.7 ~/tools/recPhyloXML/python/NOTUNGtoRecPhyloXML.py -g ~/labs/lab06-$MYGIT/RAE1/RAE1.homologs.al.mid.treefile.reconciled --include.species
```
```bash
thirdkind -Iie -D 40 -f ~/labs/lab06-$MYGIT/RAE1/RAE1.homologs.al.mid.treefile.reconciled.xml -o  ~/labs/lab06-$MYGIT/RAE1/RAE1.homologs.al.mid.treefile.reconciled.svg
```
This creates a gene reconciliation with species tree reconciliation using third kind to view the tree. 

# Part 17 - Protein Domain Prediction 
We need to use unaligned protein sequence. First, Create a directory for RAE1 sequences and change into that directory using the following command:
```bash
mkdir ~/labs/lab08-$MYGIT/RAE1 && cd ~/labs/lab08-$MYGIT/RAE1
```
```bash
cp ~/labs/lab05-$MYGIT/RAE1/RAE1.homologs.al.mid.treefile ~/labs/lab08-$MYGIT/RAE1
```
Now, you need to make a copy of the raw unaligned sequence using sed's substitute command and direct output it into folder (lab 8). Use the following command: 
```bash
sed 's/*//' ~/labs/lab04-$MYGIT/RAE1/RAE1.homologs.fas > ~/labs/lab08-$MYGIT/RAE1/RAE1.homologs.fas
```
Then, download the Pfam database using the following command: 
```bash
wget -O ~/data/Pfam_LE.tar.gz ftp://ftp.ncbi.nih.gov/pub/mmdb/cdd/little_endian/Pfam_LE.tar.gz && tar xfvz ~/data/Pfam_LE.tar.gz  -C ~/data
```
The command above is used to download the Pfam database that consists models that allow us to predit domains. 

# Part 18 - Running RPS Blast 
Let us first run RPS Blast using the following command: 
```bash
rpsblast -query ~/labs/lab08-$MYGIT/RAE1/RAE1.homologs.fas -db ~/data/Pfam -out ~/labs/lab08-$MYGIT/RAE1/RAE1.rps-blast.out  -outfmt "6 qseqid qlen qstart qend evalue stitle" -evalue .01
```
This results in output of the Pfram data for the RAE1 gene. 

Now, lets run a script that allows the plotting of the Pfam domain predictions given by rps blast next to thier protein on the tree with the follwing command: 
```bash
sudo /usr/local/bin/Rscript  --vanilla ~/labs/lab08-$MYGIT/plotTreeAndDomains.r ~/labs/lab08-$MYGIT/RAE1/RAE1.homologs.al.mid.treefile
~/labs/lab08-$MYGIT/RAE1/RAE1.rps-blast.out
~/labs/lab08-$MYGIT/RAE1/RAE1.tree.rps.pdf
```
For brief explanations on the commands, sudo is used in cases you might need permission to install packages./usr/local/bin/Rscript, the rscript program enables you to run R script from the command line, not opening the R console. While --vanilla indicates to R to not save or restore a workspace or previous setting.
Rps blast file will create the midpoint rooted tree for RAE1 proteins, Pfam domains from rps blast and output pdf file.

To now look at the domains for research, use the following command to focus on the predictions in pfam database :
```bash
mlr --inidx --ifs "\t" --opprint  cat ~/labs/lab08-$MYGIT/RAE1/RAE1.rps-blast.out | tail -n +2 | less -S
```
This command should create a domain-on-tree graphic 

Now, we will move onto analyzing distribution of pfam domains in RAE1 protein. Use the following command to do so: 
```bash 
cut -f 1 ~/labs/lab08-$MYGIT/RAE1/RAE1.rps-blast.out | sort | uniq -c
```
```bash
cut -f 6 ~/labs/lab08-$MYGIT/RAE1/RAE1.rps-blast.out | sort | uniq -c
```
```bash
awk '{a=$4-$3;print $1,'\t',a;}' ~/labs/lab08-$MYGIT/RAE1/RAE1.rps-blast.out |  sort  -k2nr
```
```bash
awk '{a=$4-$3;print $1,'\t',a;}' ~/labs/lab08-$MYGIT/RAE1/RAE1.rps-blast.out |  sort  -k2nr
```
Doing so will tell us the proteins that have more than one annotation, annotaion that is commonly found and protein that has longest/shortest annotated protein domain. 

Now we will try to figure out which protein has domain annotation with best e-value. Use the following command to do so: 
```bash
cut -f 1,5 -d $'\t' ~/labs/lab08-$MYGIT/RAE1/RAE1.rps-blast.out | sort -k2,2rn -t $'\t'
```

















