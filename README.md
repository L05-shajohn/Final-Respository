# Method-Section
Methods Code
ncbi-acc-download -F fasta -m protein XP_001620799.2
#This downloads the protein sequence. 

blastp -db allprotein.fas -query XP_001620799.2.fa -outfmt 0 -max_hsps 1 -out synaptotagmin.blastp.typical.out
#This conducts a search that aligns the query sequence to those in the database returning alignments with high scoring pairs. 

blastp -db allprotein.fas -query XP_001620799.2.fa -outfmt "6 sseqid pident length mismatch gapopen evalue bitscore pident stitle" -max_hsps 1 -out synaptotagmin.blastp.detail.out
#This converts the alignments into an output that is more detailed and easier to process. 

awk '{if ($6<0.0000000000000000000000001)print $1 }' synaptotagmin.blastp.detail.out > synaptotagmin.blastp.detail.filtered.out
#This sets an evalue of 1e-25 which gets rid of some hits ultimately yielding an ideal number of 33 proteins which falls with the 15-50 protein range that was alloted for this analysis. 

muscle -in synaptotagmin.blastp.detail.filtered.fas -out synaptotagmin.blastp.detail.filtered.aligned.fas
#This creates multiple simulatneous alignments using the query sequence and the sequences in the database. 

t_coffee -other_pg seq_reformat -in synaptotagmin.blastp.detail.filtered.aligned.fas -output sim
#This provides statistics about the multiple sequence alignment such as average percent identity among all sequences as well as the average percent identity between the query sequence and all other sequences. 
