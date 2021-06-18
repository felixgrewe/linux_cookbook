# Juicer installation

This is an instruction of Juicer installation for CPU version, which can deal with Hi-C data.

##Create and conda environment for Juicer:

    conda create –name juicer
    conda activate juicer

##Download the Juicer and reference files:

    mkdir ~/opt/Juicer
    git clone https://github.com/theaidenlab/juicer.git
    cd ~/opt/Juicer
    ln -s ~/juicer/CPU scripts
    cd scripts/common
    wget https://hicfiles.tc4ga.com/public/juicer/juicer_tools.1.9.9_jcuda.0.8.jar
    ln -s juicer_tools.1.9.9_jcuda.0.8.jar  juicer_tools.jar
    cd ../..
###Blow is just an example for Juicer data format, please see the Juicer build below
    mkdir references
    cp <my_reference_fastas_and_indices> references/
    # this is optional, only needed for fragment-delimited files
    ln -s <myRestrictionSiteDir> restriction_sites
    cd <myWorkingDir>
    mkdir fastq
    mv <fastq_files> fastq/
    ~/opt/Juicer/scripts/juicer.sh -D <myJuicerDir>

##Installation the dependencies for Juicer:

1) GNU CoreUtils
    conda install -c conda-forge coreutils
2) Burrows-Wheeler Aligner (BWA)
    conda install -c bioconda bwa
