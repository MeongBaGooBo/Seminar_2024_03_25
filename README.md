# Seminar_2024_03_25
* Bacteria genome assembly and annotation

# 1.Bacteria genome assembly

* Illumina data: B04_S13_R1_001.fastq, B04_S13_R2_001.fastq
* Nanopore data: B04.fastq

1.1 SPAdes installation
----
SPAdes link: https://github.com/ablab/spades

    wget https://github.com/ablab/spades/releases/download/v3.15.5/SPAdes-3.15.5-Linux.tar.gz
    tar -xzf SPAdes-3.15.5-Linux.tar.gz
    cd SPAdes-3.15.5-Linux/bin/
    spades.py --test
    
* Path 설정
  
1.2 Illumina read trimming
----    
    spades.py --only-error-correction -1 B04_S13_R1_001.fastq -2 B04_S13_R2_001.fastq -o SPAdes_error_correction_output -t 60
    
* -1, -2: illumina data
* -o: output directory
* -t: number of threads

* Error corrected read data: SPAdes_error_correction_output/corrected/B04_S13_R1_001.00.0_0.cor.fastq,B04_S13_R2_001.00.0_0.cor.fastq

1.3 Chromosome assembly
----
      spades.py --isolate -1 B04_S13_R1_001.fastq -2 B04_S13_R2_001.fastq --nanopore B04.fastq -o SPAdes_chromosome_assembly_output -t 60
  
* -1, -2: illumina data
* --nanopore: nanopore data
* -o: output directory
* -t: number of threads

* Assembled chromosome data: SPAdes_chromosome_assembly_output/contigs.fasta

Fasta 파일 내 첫 sequence (가장 긴 서열) parsing

    awk '/^>/{if(NR>1) exit; print; next} {if (!/^>/) print}' contigs.fasta > SPAdes_chromosome.fasta

1.4 Plasmid assembly
----
    spades.py --plasmid -1 B04_S13_R1_001.fastq -2 B04_S13_R2_001.fastq --nanopore B04.fastq -o SPAdes_plasmid_assembly_output -t 60 

* -1, -2: illumina data
* --nanopore: nanopore data
* -o: output directory
* -t: number of threads

* Assembled plasmid data: SPAdes_plasmid_assembly_output/contigs.fasta

# 2. Circularization
Circulator tool은 BWA, Prodigal, Samtools, Mummer tool들을 필요로 하여 먼저 설치 후 path 설정해주어야 함.

Circlator link: https://github.com/sanger-pathogens/circlator

2.1 필요한 tool들 설치
---

* BWA

BWA link: https://github.com/lh3/bwa  

      wget https://github.com/lh3/bwa/archive/refs/tags/v0.7.17.tar.gz
      tar -zxvf v0.7.17.tar.gz
      cd bwa-0.7.17/
      make

* Prodigal

Prodigal link: https://github.com/hyattpd/Prodigal

      wget https://github.com/hyattpd/Prodigal/releases/download/v2.6.3/prodigal.linux
      chmod 777 prodigal.linux
      mv prodigal.linux prodigal

* Samtools

Samtools link: https://www.htslib.org/
      
      wget https://github.com/samtools/samtools/releases/download/1.19.2/samtools-1.19.2.tar.bz2
      tar -jxvf samtools-1.19.2.tar.bz2
      cd samtools-1.19.2
      ./configure
      make

* Mummer

Mummer link: https://github.com/mummer4/mummer

      wget https://github.com/mummer4/mummer/releases/download/v4.0.0rc1/mummer-4.0.0rc1.tar.gz
      tar -zxvf v4.0.0rc1.tar.gz
      cd mummer-4.0.0rc1
      make

2.2 Circlator install
--
      pip3 install circlator
  
2.3 Circularization by Circlator
--
      circlator all --threads 8 SPAdes_chromosome.fasta B04.fastq Circlator
      
* --threads:  number of threads
* SPAdes_chromosome.fasta: assembled genome sequence
* B04.fastq: Nanopre data
* Circlator: output directory

* Output file인 04.merge.circularise.log 확인하여  circularization check
* Circularised genome data: 06.fixstart.fasta

# 3. Bacterial genome annotation
Prokka 설치를 위해서 anaconda를 먼저 설치해주어야함.

Prokka link: https://github.com/tseemann/prokka

3.1 Anaconda install
--
Anaconda linl: https://www.anaconda.com/

    wget https://repo.anaconda.com/archive/Anaconda3-2024.02-1-Linux-x86_64.sh
    sh ./Anaconda3-2024.02-1-Linux-x86_64.sh
    
* 중간 중간 약관 확인 및 anaconda 설치 위치 등 입력해주어야 함.

3.2 Prokka install
--
    conda install -c conda-forge -c bioconda -c defaults prokka
    
3.3 Prokka DB setup
--
    prokka --setupdb
    
3.4 Annotation by Prokka
--
    prokka -outdir ./Prokka_output 06.fixstart.fasta
    
* 06.fixstart.fasta: circularised genome sequence
* --outdir: output directory

# 4. Copying files to my computer

    scp -r -P 2222 Prokka_output /Users/seongminkim/Desktop

* -r: directory copying
* -P: port number 
