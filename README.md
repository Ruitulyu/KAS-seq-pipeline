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
# Install conda environment.
$ install_conda_env.sh
```
## Install reference genome using KAS-pipe shell script:
```Swift
# Please create a directory where you want to install your reference genome and index.
$ mkdir -p ~/Software/
$ cd ~/Software/Genome/
$ build_reference_genome.sh hg19 ~/Software/Genome/

build_reference_genome.sh - This script is used to install reference genome <assembly> in a directory <dest_dir>.
Usage: build_reference_genome.sh <assembly> <dest_dir>
Example: nohup build_reference_genome.sh hg19 /your/genome/data/path/ &

Options:
<assembly>               Input the assembly of the reference genome you want to download and install(mm9, mm10, hg19, hg38...).
<dest_dir>               Input the path of the directory that you want to install your interested genome in your server.
-h or --help             Print the help.
```
# Usage:

## Adapter and low quality sequence trimming:
trim_adapter.sh - This script is used to trim adapter and low quality sequence from Raw KAS-seq data.
```Swift
Single_end: trim_adapter.sh <Adapter_type> <Minimum_reads_length> <threads> <single> <raw_fastq_read>
Paired_end: trim_adapter.sh <Adapter_type> <Minimum_reads_length> <threads> <paired> <raw_fastq_read1> <raw_fastq_read2>

Example:
nohup trim_adapter.sh illumina 30 10 single raw_fastq_read1.fastq.gz &
nohup trim_adapter.sh illumina 30 10 paired raw_fastq_read1.fastq.gz raw_fastq_read2.fastq.gz &

Options:
<adapter_type>           Input the adapter types during KAS-seq libraries construction(illumina, nextera, small_rna).
<min_reads_length>       Discard reads that became shorter than length <min_reads_length> because of either quality or adapter trimming.
<threads>                Input the number of cores to be used for trimming.
<paired_or_single>       Specify the mode of sequencing data(single, paired).
<raw_fastq_read1>        Input the single-end raw fastq file or read 1 of paired-end raw fastq files.
<raw_fastq_read2>        Input the read 2 of paired-end raw fastq files.
-h or --help             Print the help.
Note: This shell script invokes the trim-galore, please refer http://www.bioinformatics.babraham.ac.uk/projects/trim_galore/ for more information. 
```

## Map KAS-seq data to reference genome:
map_KAS-seq.sh - This script is used to map KAS-seq data to the reference genome.
```Swift
Usage:
Single_end: map_KAS-seq.sh <bowtie2_index_path> <threads> <basename> <assembly> <single> <raw_fastq_read>
Paired_end: map_KAS-seq.sh <bowtie2_index_path> <threads> <basename> <assembly> <paired> <raw_fastq_read1> <raw_fastq_read2>

Example:
Single_end: nohup map_KAS-seq.sh /Genome/hg19_Bowtie2Index/hg19 10 KAS-seq_data hg19 single KAS-seq.single.fastq.gz &
Paired_end: nohup map_KAS-seq.sh /Genome/hg19_Bowtie2Index/hg19 10 KAS-seq_data hg19 paired KAS-seq.paired.R1.fastq.gz KAS-seq.paired.R2.fastq.gz &

Options:
<bowtie2_index_path>      Input the path of reference genome bowtie2 index.
<threads>                 Input the number of threads.
<basename>                Input the basename of KAS-seq_mapping.sh output files.
<assembly>                Input the assembly of your reference genome(mm9, mm10, hg19, hg38...).
<paired_or_single>        Specify the mode of KAS-seq data alignment(single, paired).
<raw_fastq_read1>         Input the single-end KAS-seq fastq file or read 1 of paired-end KAS-seq fastq files.
<raw_fastq_read2>         Input the read 2 of paired-end KAS-seq fastq files.
-h or --help              Print the help.
Note: map_KAS-seq.sh will generate KAS-seq deduplicated mapped reads(bam and bed files), density files(bedGraph file) and mapping summary file.

If you want to transfer normalized bedGraph file to bigWig file, please refer to the provided shell scripts --normalize_KAS-seq.sh & --make_BigWig_files.sh.
```

## KAS-seq peaks calling:
call_KAS-seq_peaks.sh - This script is used to call peaks for KAS-seq data.
```Swift
# Bed files or indexed bam files are needed for KAS-seq peaks calling.

Usage:
regular peaks: call_KAS-seq_peaks.sh <KAS_seq_files> <Input_files> <regular> <basename> <genome_size>
broad peaks: call_KAS-seq_peaks.sh <KAS_seq_files> <Input_files> <broad> <basename> <genome_size>

Example:
nohup call_KAS-seq_peaks.sh KAS_seq_files.txt Input_files.txt regular KAS-seq_regular hg &
nohup call_KAS-seq_peaks.sh KAS_seq_files.txt Input_files.txt broad KAS-seq_broad hg &

Options:
<KAS_seq_files>           Input the text file containing the name of KAS-seq IP bed or indexed bam files generated by map_KAS-seq.sh.
Example: KAS.rep1.bed KAS.rep2.bed KAS.rep3.bed        ---KAS_seq_files.txt

<Input_files>             Input the text file containing the name of KAS-seq Input bed or indexed bam files..
Example: Input.rep1.bed Input.rep2.bed Input.rep3.bed  ---Input_files.txt

<regular_or_broad>        Specify regular or broad to tell macs2 that if put nearby highly enriched regions into a broad region with loose cutoff.
<basename>                Input the basename of output files.
<genome_size>             Input mappable genome size or effective genome size which is defined as the genome size which can be sequenced(hs, mm, ce, dm).
Note: hs for Homo sapiens, mm for Mus musculus, ce for Caenorhabditis elegans, dm for Drosophila melanogaster.

-h or --help              Print the help.
```Swift



