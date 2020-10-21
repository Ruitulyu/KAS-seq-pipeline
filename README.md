# KAS-pipe
KAS-pipe is an analysis pipeline for KAS-seq data. KAS-seq is a kethoxal-assisted single-stranded DNA sequencing (KAS-seq) approach, based on the fast and specific reaction between N3-kethoxal and guanines in ssDNA. KAS-seq allows rapid (within 5 min), sensitive and genome-wide capture and mapping of ssDNA produced by transcriptionally active RNA polymerases or other processes in situ using as few as 1,000 cells. KAS-seq can also enable definition of a group of enhancers that are single-stranded and enrich unique sequence motifs. Overall, KAS-seq facilitates fast and accurate analysis of transcription dynamics and enhancer activities simultaneously in both low-input and high-throughput manner. KAS-pipe as a analysis pipeline for KAS-seq data, which provides many shell scripts for KAS-seq data processing, for example, reference genome index setup, adapter sequence trimming, alignment, differential analysis and so on.   

![image](https://github.com/Ruitulyu/KAS-pipe/blob/master/images/Schematic%20diagram%20for%20KAS-seq.png)

# Citation:
Wu, Tong, et al. [Kethoxal-assisted single-stranded DNA sequencing captures global transcription dynamics and enhancer activity in situ](https://www.nature.com/articles/s41592-020-0797-9). Nature Methods 17.5 (2020): 515-523.

# Dependencies:
- samtools ==1.9
- bedtools ==2.29.0
- picard ==2.20.7
- fastqc

- ucsc-fetchchromsizes ==357 
- ucsc-bedgraphtobigwig ==357
- ucsc-bedsort

- r ==3.5.1
- r-devtools
- r-corrplot
- r-RColorBrewer
- bioconductor-rsamtools
- bioconductor-deseq2

- bowtie2 ==2.3.4.3
- bowtie
- deeptools ==3.3.1
- cutadapt ==2.5
- trim-galore ==0.6.5
- macs2 ==2.2.4

- java-jdk
- python3

# Installation:
Please make sure you have [miniconda3](https://docs.conda.io/en/latest/miniconda.html) or [anaconda3](https://www.anaconda.com/products/individual) environments in your server in order to use the provided shell script to install the dependencies. Or you can follow the user guide to accomplish the conda installation: https://docs.conda.io/projects/conda/en/latest/user-guide/install/.

## Install KAS-pipe by cloning this repository:
```Swift
$ git clone https://github.com/Ruitulyu/KAS-pipe
$ cd KAS-pipe
$ chmod 755 setup.sh
$ ./setup.sh
$ install_conda_env.sh
```
## Install reference genome using KAS-pipe shell script:
```Swift
# Please create a directory where you want to install your reference genome and index. Example: mkdir -p ~/Software/;
$ cd ~/Software/
$ build_reference_genome.sh hg19 ~/Software/

# build_reference_genome.sh - This script is used to install reference genome <assembly> in a directory <dest_dir>.
# Usage: build_reference_genome.sh <assembly> <dest_dir>
# Example: nohup build_reference_genome.sh hg19 /your/genome/data/path/ &
# Options:
# <assembly> Input the assembly of the reference genome you want to download and install(mm9, mm10, hg19, hg38...).
# <dest_dir> Input the path of the directory that you want to install your interested genome in your server.
```
# Usage:
