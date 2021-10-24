# hse21_hw1
```bash
# create links to files
ln -s /usr/share/data-minor-bioinf/assembly/oil_R1.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oil_R2.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R1_001.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R2_001.fastq

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


# fastqc + multiqc
mkdir fastqc2
ls *.fq.* | xargs -P 4 -tI{} fastqc -o fastqc2 {}

multiqc -o multiqc fastqc2

mkdir trimmed_fq
mv -v *.fq.* *trimmed trimmed_fq/

cd ../
rm sub*

tmux
bash 

time platanus assemble -o Poil -t 1 -f trimmed_fq/subR1_pair.fq.trimmed trimmed_fq/subR2_pair.fq.trimmed

time platanus scaffold -o Poil -t 1 -c Poil_contig.fa -IP1 trimmed_fq/subR1_pair.fq.trimmed trimmed_fq/subR2_pair.fq.trimmed -OP2 trimmed_fq/subMP1.fq.int_trimmed trimmed_fq/subMP2.fq.int_trimmed 2> scaffold.log

time platanus gap_close -o Poil -t 1 -c Poil_scaffold.fa -IP1 trimmed_fq/subR1_pair.fq.trimmed trimmed_fq/subR2_pair.fq.trimmed -OP2 trimmed_fq/subMP1.fq.int_trimmed trimmed_fq/subMP2.fq.int_trimmed 2>gapclose.log

tmux kill-server

# assembly with less data 
seqtk sample -s1205 oil_R1.fastq 3000000 > subR1_pair.fq
seqtk sample -s1205 oil_R2.fastq 3000000 > subR2_pair.fq
seqtk sample -s1205 oilMP_S4_L001_R1_001.fastq 1000000 > subMP1.fq
seqtk sample -s1205 oilMP_S4_L001_R2_001.fastq 1000000 > subMP2.fq
# repeat trimming
```
MultiQC by trimmed files:
![image] (https://user-images.githubusercontent.com/55647212/138617462-0f426c60-fcb3-49dd-ae85-7afa020d2812.png)
