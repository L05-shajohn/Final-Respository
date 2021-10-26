# Method-Section
Methods Code
ncbi-acc-download -F fasta -m protein XP_001620799.2

blastp -db allprotein.fas -query XP_001620799.2.fa -outfmt 0 -max_hsps 1 -out synaptotagmin.blastp.typical.out

blastp -db allprotein.fas -query XP_001620799.2.fa -outfmt "6 sseqid pident length mismatch gapopen evalue bitscore pident stitle" -max_hsps 1 -out synaptotagmin.blastp.detail.out

awk '{if ($6<0.0000000000000000000000001)print $1 }' synaptotagmin.blastp.detail.out > synaptotagmin.blastp.detail.filtered.out

muscle -in synaptotagmin.blastp.detail.filtered.fas -out synaptotagmin.blastp.detail.filtered.aligned.fas

t_coffee -other_pg seq_reformat -in synaptotagmin.blastp.detail.filtered.aligned.fas -output sim
