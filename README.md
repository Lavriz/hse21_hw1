# hse21_hw1
```bash
ls /usr/share/data-minor-bioinf/assembly
ls -lh /usr/share/data-minor-bioinf/assembly

# create links to files
ls /usr/share/data-minor-bioinf/assembly* | xargs -tI{} ln -s {}

# 1st step
seqtk sample -s1205 oil_R1.fastq 5000000 > subR1_pair.fq
seqtk sample -s1205 oil_R2.fastq 5000000 > subR2_pair.fq
seqtk sample -s1205 oilMP_S4_L001_R1_001.fastq 1500000 > subMP1.fq
seqtk sample -s1205 oilMP_S4_L001_R2_001.fastq 1500000 > subMP2.fq

# fastqc & multiqc
mkdir fastqc
ls sub* | xargs -P 4 -tI{} fastqc -o fastqc {}
mkdir multifastqc 
multiqc -o multiqc fastqc

# trimming files + this part is completely repeated with less data 
platanus_trim subR1_pair.fq subR2_pair.fq
platanus_internal_trim subMP1.fq subMP2.fq

mkdir trimmed_fq
mv -v *trimmed trimmed_fq/

# fastqc & multiqc for the trimmed files
ls *.trimmed | xargs -P 4 -tI{} fastqc -o fastqc {}
multiqc -o multiqc fastqc

rm sub*

tmux
bash 

time platanus assemble -o Poil -t 1 -f trimmed_fq/subR1_pair.fq.trimmed trimmed_fq/subR2_pair.fq.trimmed

time platanus scaffold -o Poil -t 1 -c Poil_contig.fa -IP1 trimmed_fq/subR1_pair.fq.trimmed trimmed_fq/subR2_pair.fq.trimmed -OP2 trimmed_fq/subMP1.fq.int_trimmed trimmed_fq/subMP2.fq.int_trimmed 2> scaffold.log

time platanus gap_close -o Poil -t 1 -c Poil_scaffold.fa -IP1 trimmed_fq/subR1_pair.fq.trimmed trimmed_fq/subR2_pair.fq.trimmed -OP2 trimmed_fq/subMP1.fq.int_trimmed trimmed_fq/subMP2.fq.int_trimmed 2>gapclose.log

tmux kill-server

// assembly with less data 

seqtk sample -s1205 oil_R1.fastq 3000000 > subR1_pair.fq
seqtk sample -s1205 oil_R2.fastq 3000000 > subR2_pair.fq
seqtk sample -s1205 oilMP_S4_L001_R1_001.fastq 1000000 > subMP1.fq
seqtk sample -s1205 oilMP_S4_L001_R2_001.fastq 1000000 > subMP2.fq
```
