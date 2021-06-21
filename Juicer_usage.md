# Juicer usage

This is an instruction of Juicer usage using the reference data from Juicer.

##Running Juicer with correct folder path:

    ~/opt/juicer/scripts/juicer.sh \
    -z ~/opt/juicer/references/Homo_sapiens_assembly19.fasta #reference genome\
    -p ~/opt/juicer/restriction_sites/hg19.chrom.sizes #chromosome size \
    -y ~/opt/juicer/restriction_sites/hg19_MboI.txt #restriction_sites\
    -d ~/rawdata/A549/replicate1 #hi-c data\
    -D ~/juicer_data #working directory

    Usage: juicer.sh [-g genomeID] [-d topDir] [-q queue] [-l long queue] [-s site]
                 [-a about] [-S stage] [-p chrom.sizes path]
                 [-y restriction site file] [-z reference genome file]
                 [-C chunk size] [-D Juicer scripts directory]
                 [-Q queue time limit] [-L long queue time limit] [-b ligation] [-t threads]
                 [-A account name] [-e] [-h] [-f] [-j]
