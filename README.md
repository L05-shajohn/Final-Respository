# Final Respository

ncbi-acc-download -F fasta -m protein XP_001620799.2

#This downloads the protein sequence. 

blastp -db allprotein.fas -query XP_001620799.2.fa -outfmt 0 -max_hsps 1 -out synaptotagmin.blastp.typical.out

#This conducts a search that aligns the query sequence to those in the database returning alignments with high scoring pairs. 

blastp -db allprotein.fas -query XP_001620799.2.fa -outfmt "6 sseqid pident length mismatch gapopen evalue bitscore pident stitle" -max_hsps 1 -out synaptotagmin.blastp.detail.out

#This converts the alignments into an output that is more detailed and easier to process. 

awk '{if ($6<0.0000000000000000000000001)print $1 }' synaptotagmin.blastp.detail.out > synaptotagmin.blastp.detail.filtered.out

#This sets an evalue of 1e-25 which gets rid of some hits ultimately yielding an ideal number of 33 proteins which falls with the 15-50 protein range that was alloted for this analysis. 

wc -l synaptotagmin.blastp.detail.filtered.out

#This counts the number of results. 

seqkit grep --pattern-file synaptotagmin.blastp.detail.filtered.out allprotein.fas > synaptotagmin.blastp.detail.filtered.fas

#This searches sequences by ID, name, sequence, and sequence motifs.

muscle -in synaptotagmin.blastp.detail.filtered.fas -out synaptotagmin.blastp.detail.filtered.aligned.fas

#This creates multiple simulatneous alignments using the query sequence and the sequences in the database. 

t_coffee -other_pg seq_reformat -in synaptotagmin.blastp.detail.filtered.aligned.fas -output sim

#This provides statistics about the multiple sequence alignment such as average percent identity among all sequences as well as the average percent identity between the query sequence and all other sequences. 

sed "s/ //g" synaptotagmin.blastp.detail.filtered.aligned.fas > synaptotagmin.blastp.detail.filtered.aligned.fas

#This replaces spaces in the annotations with underscores so they are not lost when making the phylogenetic tree. 

iqtree -s synaptotagmin.blastp.detail.filtered.aligned_.fas -nt 2

#This generates the most optimal gene tree; it is unrooted. 

gotree reroot midpoint -i synaptotagmin.blastp.detail.filtered.aligned_.fas.treefile -o synaptotagmin.blastp.detail.filtered.aligned_.fas.midpoint.treefile

#This roots the tree at its midpoint. 

nw_display -s synaptotagmin.blastp.detail.filtered.aligned_.fas.midpoint.treefile -w 1000 -b 'opacity:0' > synaptotagmin.blastp.detail.filtered.aligned_.fas.midpoint.treefile.svg

#This converts the tree to an svg file.

git add synaptotagmin.blastp.detail.filtered.aligned_.fas.midpoint.treefile.svg git commit -m "created a svg from a newick file" git pull --no-edit git push

#This pushes the svg file into the respository where it can be viewed as a png file. 

java -jar ~/tools/Notung-3.0-beta/Notung-3.0-beta.jar -s speciesTreeBilateriaCnidaria.tre -g synaptotagmin.genes.tre --reconcile --speciestag prefix --savepng --events

#This creates a reconciled tree using the gene tree and the species tree. 

python2.7 ~/tools/recPhyloXML/python/NOTUNGtoRecPhyloXML.py -g synaptotagmin.genes.tre.reconciled --include.species

#This creates a RecPhyloXML object to view the gene-within-species tree.

thirdkind -f synaptotagmin.genes.tre.reconciled.xml -o synaptotagmin.genes.tre.genes.tre.reconciled.svg

#This creates a gene-reconciliation-within-species tree.

git add synaptotagmin.genes.tre.genes.tre.reconciled.svg git commit -m "created a reconciled gene tree for synaptotagmin imposed on a species tree png " git pull --no-edit git push

#This pushes the reconciled tree svg fle to the respository to be viewed later on.

java -jar ~/tools/Notung-3.0-beta/Notung-3.0-beta.jar -s speciesTreeBilateriaCnidaria.tre -g synaptotagmin.genes.tre --root --speciestag prefix --savepng --events

#This creates a rerooted gene tree that minimizes gene duplications and losses. 

iqtree -s synaptotagmin.blastp.detail.filtered.aligned.fas -bb 1000 -nt 2 -m VT+F+R5 -t synaptotagmin.genes.tre -pre synaptotagmin.genes.ufboot

#This re-ran the iqtree search but with bootstrap support. 

gotree reroot midpoint -i synaptotagmin.genes.ufboot -o synaptotagmin.genes.midpoint.ufboot

#This reroooted the tree at the midpoint. 

sed 's_/ /_/g' synaptotagmin.blastp.detail.filtered.fas > synaptotagmin.blastp.detail.filtered.renamed.fas_

#This changes the spaces in the fasta file to underscores so the sequence match the names in the final phylogenetic tree.

    iprscan5   —sharon.john.email@stonybrook.edu  --multifasta --useSeqId --sequence   synaptotagmin.blastp.detail.filtered.fas
   
#This runs iprscan5 which searches for protein domains. 

cat ~/labs/lab8-L05-shajohn/synaptotagmin/*.tsv.tsv > ~/labs/lab8-L05-shajohn/synaptotagmin.domains.all.tsv

#This concatenates the the protein domains into one file. 

grep Pfam ~/labs/lab8-L05-shajohn/synaptotagmin.domains.all.tsv >  ~/labs/lab8-L05-shajohn/synaptotagmin.domains.pfam.tsv

#This filters for the domains identified by Pfam only.

awk 'BEGIN{FS="\t"} {print $1"\t"$3"\t"$7"@"$8"@"$5}' ~/labs/lab8-L05-shajohn/synaptotagmin.domains.pfam.tsv | datamash -sW --group=1,2 collapse 3 | sed 's/,/\t/g' | sed 's/@/,/g' > ~/labs/lab8-L05-shajohn/synaptotagmin.domains.pfam.evol.tsv

#This fixes up the output by re-arranging the interproscan output. 

