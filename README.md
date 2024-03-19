# Seminar_2024_03_25
* Bacteria genome assembly and annotation

# Bacteria genome assembly

* Illumina data: B04_S13_R1_001.fastq, B04_S13_R2_001.fastq
* Nanopore data: B04.fastq

1.SPAdes installation
=

    wget https://github.com/ablab/spades/releases/download/v3.15.5/SPAdes-3.15.5-Linux.tar.gz
    cd SPAdes-3.15.5-Linux/bin/
    tar -xzf SPAdes-3.15.5-Linux.tar.gz
    spades.py --test

2. Chromosome assembly
=
* Path 설정 

      spades.py --isolate -1 B04_S13_R1_001.fastq -2 B04_S13_R2_001.fastq --nanopore B04.fastq -o SPAdes_chromosome_output -t 60
  
* -1, -2: illumina data
* --nanopore: nanopore data
* -o: output directory
* -t: number of threads

3. Plasmid assembly
=
