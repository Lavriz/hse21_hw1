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
MultiQC by untrimmed files:
![image](https://user-images.githubusercontent.com/55647212/138617688-59fd53bc-a5f9-4241-9c4c-07accd5cdb9f.png)
![image](https://user-images.githubusercontent.com/55647212/138617743-dc80d06a-3751-4f75-bc5d-35a96279f156.png)
![image](https://user-images.githubusercontent.com/55647212/138617757-f6221fe4-2779-491a-95c5-240ae63d4000.png)
![image](https://user-images.githubusercontent.com/55647212/138617810-8bb7dc78-0007-4bd7-b503-81ab2fc9f2af.png)
![image](https://user-images.githubusercontent.com/55647212/138617832-a84e23b4-fff2-4314-a025-96333f4fb72d.png)
![image](https://user-images.githubusercontent.com/55647212/138617859-6478f4c8-a0fa-4c5c-a77d-787eb546db0d.png)
![image](https://user-images.githubusercontent.com/55647212/138617885-72248583-1c59-4c29-8ef0-acbe8eed1848.png)
![image](https://user-images.githubusercontent.com/55647212/138617915-832b237e-2a16-442e-ad23-5c25d068419e.png)
![image](https://user-images.githubusercontent.com/55647212/138617946-b10e19d9-6b13-4822-8d9a-e54c5aac6884.png)
![image](https://user-images.githubusercontent.com/55647212/138617979-444ee547-d8ca-4448-b619-a70e86d36cc1.png)
MultiQC by trimmed files:
![image](https://user-images.githubusercontent.com/55647212/138618013-3568573b-c2f1-46f1-bd27-05b0cdf8d51f.png)
![image](https://user-images.githubusercontent.com/55647212/138618034-f6ea4d89-3aa9-48b9-9fc5-c9ea30f3a480.png)
![image](https://user-images.githubusercontent.com/55647212/138618092-19175553-bd65-4a96-8f20-d308456d16f1.png)
![image](https://user-images.githubusercontent.com/55647212/138618132-1684166a-1163-4a91-bcfa-df9e1a2d6d37.png)
![image](https://user-images.githubusercontent.com/55647212/138618156-443d7648-9bfe-451d-b05d-bb1f19cb957c.png)
![image](https://user-images.githubusercontent.com/55647212/138618175-68c14097-6297-40de-bcf5-549f7e9acfd3.png)
![image](https://user-images.githubusercontent.com/55647212/138618210-bd66fbb9-c2e9-4d9f-86ed-0e4aa291aadf.png)
![image](https://user-images.githubusercontent.com/55647212/138617447-49024835-23fa-4c80-8661-421c3da946af.png)
![image](https://user-images.githubusercontent.com/55647212/138617458-06f5cc29-523f-489c-9291-ebc5485917a6.png)
![image](https://user-images.githubusercontent.com/55647212/138617460-d5027f37-cc5b-49ff-8c3a-aebd8a70e189.png)
![image](https://user-images.githubusercontent.com/55647212/138617462-0f426c60-fcb3-49dd-ae85-7afa020d2812.png)
