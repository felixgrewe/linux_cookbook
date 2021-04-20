# Complex Genome Annotation Pipeline

This pipeline is a prototype of complex genome annotation using maker and other softerware.

##Software prerequisites

1) RepeatModeler and RepeatMasker with RepBase in optional
2) Maker
3) Augustus
4) BUSCO
5) SNAP
6) BEDtools

## Data/resources

1) Genome assembled contings (fasta file)
2) A de novo transcriptome assembly created using Trinity with mRNAseq data
3) Amino acid protein sequence from reference on NCBI
4) Optional repeat library

## Gene prediction

Prediction use mainly maker with Augustus SNAP as reference.

### Reorder contigs

**reorder and rename contigs by size:**
   
    seqkit sort --by-length --reverse contigs.fasta | seqkit replace --pattern '.+' --replacement 'Contig_{nr}' --nr-width 3

### De Novo Repeat Identification

Repeat annotation using RepeatModeler

    BuildDatabase -name pelargonium -engine ncbi pelargonium_citronellum.fasta
    RepeatModeler -pa 8 -engine ncbi -database pelargonium 2>&1 | tee repeatmodeler.log

### Run maker

 1.  Edit maker_opts.ctl file with following settings
    #-----Genome (these are always required)
    genome=pelargonium_citronellum.fasta #genome sequence (fasta file or fasta embeded in GFF3 file)
    organism_type=eukaryotic #eukaryotic or prokaryotic. Default is eukaryotic
    
    #-----Re-annotation Using MAKER Derived GFF3
    maker_gff= #MAKER derived GFF3 file
    est_pass=0 #use ESTs in maker_gff: 1 = yes, 0 = no
    altest_pass=0 #use alternate organism ESTs in maker_gff: 1 = yes, 0 = no
    protein_pass=0 #use protein alignments in maker_gff: 1 = yes, 0 = no
    rm_pass=0 #use repeats in maker_gff: 1 = yes, 0 = no
    model_pass=0 #use gene models in maker_gff: 1 = yes, 0 = no
    pred_pass=0 #use ab-initio predictions in maker_gff: 1 = yes, 0 = no
    other_pass=0 #passthrough anyything else in maker_gff: 1 = yes, 0 = no
    
    #-----EST Evidence (for best results provide a file for at least one)
    est=Pcitro-RNA-TrinityAssembly.fna #set of ESTs or assembled mRNA-seq in fasta format
    altest= #EST/cDNA sequence file in fasta format from an alternate organism
    est_gff= #aligned ESTs or mRNA-seq from an external GFF3 file
    altest_gff= #aligned ESTs from a closly relate species in GFF3 format
    
    #-----Protein Homology Evidence (for best results provide a file for at least one)
    protein=Arabidopsis_pro.fasta  #protein sequence file in fasta format (i.e. from mutiple organisms)
    protein_gff=  #aligned protein homology evidence from an external GFF3 file
    
    #-----Repeat Masking (leave values blank to skip repeat masking)
    model_org=all #select a model organism for RepBase masking in RepeatMasker
    rmlib= #provide an organism specific repeat library in fasta format for RepeatMasker
    repeat_protein=/home/ysun/opt/maker/data/te_proteins.fasta #provide a fasta file of transposable element proteins for RepeatRunner
    rm_gff= #pre-identified repeat elements from an external GFF3 file
    prok_rm=0 #forces MAKER to repeatmask prokaryotes (no reason to change this), 1 = yes, 0 = no
    softmask=1 #use soft-masking rather than hard-masking in BLAST (i.e. seg and dust filtering)
    
    #-----Gene Prediction
    snaphmm= #SNAP HMM file
    gmhmm= #GeneMark HMM file
    augustus_species= #Augustus gene prediction species model
    fgenesh_par_file= #FGENESH parameter file
    pred_gff= #ab-initio predictions from an external GFF3 file
    model_gff= #annotated gene models from an external GFF3 file (annotation pass-through)
    run_evm=0 #run EvidenceModeler, 1 = yes, 0 = no
    est2genome=1 #infer gene predictions directly from ESTs, 1 = yes, 0 = no
    protein2genome=1 #infer predictions from protein homology, 1 = yes, 0 = no
    trna=1 #find tRNAs with tRNAscan, 1 = yes, 0 = no
    snoscan_rrna= #rRNA file to have Snoscan find snoRNAs
    snoscan_meth= #-O-methylation site fileto have Snoscan find snoRNAs
    unmask=0 #also run ab-initio prediction programs on unmasked sequence, 1 = yes, 0 = no
    
    #-----Other Annotation Feature Types (features MAKER doesn't recognize)
    other_gff= #extra features to pass-through to final MAKER generated GFF3 file
    
    #-----External Application Behavior Options
    alt_peptide=C #amino acid used to replace non-standard amino acids in BLAST databases
    cpus=20 #max number of cpus to use in BLAST and RepeatMasker (not for MPI, leave 1 when using MPI)
    
    #-----MAKER Behavior Options
    max_dna_len=100000 #length for dividing up contigs into chunks (increases/decreases memory usage)
    min_contig=1 #skip genome contigs below this length (under 10kb are often useless)
    
    pred_flank=200 #flank for extending evidence clusters sent to gene predictors
    pred_stats=0 #report AED and QI statistics for all predictions as well as models
    AED_threshold=1 #Maximum Annotation Edit Distance allowed (bound by 0 and 1)
    min_protein=0 #require at least this many amino acids in predicted proteins
    alt_splice=1 #Take extra steps to try and find alternative splicing, 1 = yes, 0 = no
    always_complete=0 #extra steps to force start and stop codons, 1 = yes, 0 = no
    map_forward=0 #map names and attributes forward from old GFF3 genes, 1 = yes, 0 = no
    keep_preds=0 #Concordance threshold to add unsupported gene prediction (bound by 0 and 1)
    
    split_hit=10000 #length for the splitting of hits (expected max intron size for evidence alignments)
    min_intron=20 #minimum intron length (used for alignment polishing)
    single_exon=0 #consider single exon EST evidence when generating annotations, 1 = yes, 0 = no
    single_length=250 #min length required for single exon ESTs if 'single_exon is enabled'
    correct_est_fusion=0 #limits use of ESTs in annotation to avoid fusion genes
    
    tries=2 #number of times to try a contig if there is a failure for some reason
    clean_try=0 #remove all data from previous run before retrying, 1 = yes, 0 = no
    clean_up=0 #removes theVoid directory with individual analysis files, 1 = yes, 0 = no
    TMP= #specify a directory other than the system default temporary directory for temporary files
    
 2.  Edit maker_opts.ctl file 
	- put the location of executables. For example:
		makeblastdb=/home/FM/xxx/anaconda3/bin/makeblastdb			
		blastn=/home/FM/xxx/anaconda3/bin/blastn
		blastx=/home/FM/xxx/anaconda3/bin/blastx
		RepeatMasker=/home/FM/xxx/opt/RepeatMasker/RepeatMasker
		snap=/usr/bin/snap
		augustus=/home/FM/xxx/opt/Augustus/bin/augustus
		
3. Run maker `~/opt/maker/bin/maker`

### Training SNAP

    mkdir snap
    mkdir snap/round1
    cd snap/round1
    ~/opt/maker/bin/maker2zff -x 0.25 -l 50 -d /home/ysun/projects/pelargonium/new/pelargonium_citronellum.maker_1stround.output/pelargonium_citronellum_master_datastore_index.log
    rename 's/genome/pelargonium_rnd1.zff.length50_aed0.25/g' *
    fathom pelargonium_rnd1.zff.length50_aed0.25.ann pelargonium_rnd1.zff.length50_aed0.25.dna -gene-stats > gene-stats.log 2>&1
    fathom pelargonium_rnd1.zff.length50_aed0.25.ann pelargonium_rnd1.zff.length50_aed0.25.dna -validate > validate.log 2>&1
    fathom pelargonium_rnd1.zff.length50_aed0.25.ann pelargonium_rnd1.zff.length50_aed0.25.dna -categorize 1000 > categorize.log 2>&1
    fathom uni.ann uni.dna -export 1000 -plus > uni-plus.log 2>&1
    mkdir params
    cd params
    forge ../export.ann ../export.dna > ../forge.log 2>&1
    cd ../
    hmm-assembler.pl pelargonium_rnd1.zff.length50_aed0.25 params > pelargonium_rnd1.zff.length50_aed0.25.hmm

### Training Augustus with BUSCO
“Using the BUSCO 4.1.4 for the trainning”

    samtools index pelargonium_citronellum.fasta
    mkdir augustus
    mkdir augustus/round1
    cd augustus/round1
….bash mRNA_annotations.sh
    busco -i pelargonium_rnd1.all.maker.transcripts1000_redu.fasta -o pelargonium_rnd1_maker -l embryophyta_odb10 -m genome -c 8 --long --augustus_species arabidopsis --augustus_parameters='--progress=true'
    cd /home/ysun/projects/pelargonium/new/augustus/round1/pelargonium_rnd1_maker/run_embryophyta_odb10/augustus_output/retraining_parameters/BUSCO_pelargonium_rnd1_maker/
    rename 's/BUSCO_pelargonium_rnd1_maker/pelargonium_citronellum/g' *
    sed -i 's/BUSCO_pelargonium_rnd1_maker/pelargonium_citronellum/g' pelargonium_citronellum_parameters.cfg
    sed -i 's/BUSCO_pelargonium_rnd1_maker/pelargonium_citronellum/g' pelargonium_citronellum_parameters.cfg.orig1
    mkdir $AUGUSTUS_CONFIG_PATH/species/pelargonium
### Maker 2nd run
1.  Edit maker_opts.ctl file with following settings
    #-----Genome (these are always required)
    genome=pelargonium_citronellum.fasta #genome sequence (fasta file or fasta embeded in GFF3 file)
    organism_type=eukaryotic #eukaryotic or prokaryotic. Default is eukaryotic
    
    #-----Re-annotation Using MAKER Derived GFF3
    maker_gff= #MAKER derived GFF3 file
    est_pass=0 #use ESTs in maker_gff: 1 = yes, 0 = no
    altest_pass=0 #use alternate organism ESTs in maker_gff: 1 = yes, 0 = no
    protein_pass=0 #use protein alignments in maker_gff: 1 = yes, 0 = no
    rm_pass=0 #use repeats in maker_gff: 1 = yes, 0 = no
    model_pass=0 #use gene models in maker_gff: 1 = yes, 0 = no
    pred_pass=0 #use ab-initio predictions in maker_gff: 1 = yes, 0 = no
    other_pass=0 #passthrough anyything else in maker_gff: 1 = yes, 0 = no
    
    #-----EST Evidence (for best results provide a file for at least one)
    est= #set of ESTs or assembled mRNA-seq in fasta format
    altest= #EST/cDNA sequence file in fasta format from an alternate organism
    est_gff= pelargonium_rnd1.maker.output/pelargonium_rnd1.all.maker.est2genome.gff#aligned ESTs or mRNA-seq from an external GFF3 file
    altest_gff= #aligned ESTs from a closly relate species in GFF3 format
    
    #-----Protein Homology Evidence (for best results provide a file for at least one)
    protein=  #protein sequence file in fasta format (i.e. from mutiple organisms)
    protein_gff= pelargonium_rnd1.maker.output/pelargonium_rnd1.all.maker.protein2genome.gff #aligned protein homology evidence from an external GFF3 file
    
    #-----Repeat Masking (leave values blank to skip repeat masking)
    model_org=all #select a model organism for RepBase masking in RepeatMasker
    rmlib= #provide an organism specific repeat library in fasta format for RepeatMasker
    repeat_protein #provide a fasta file of transposable element proteins for RepeatRunner
    rm_gff= pelargonium_rnd1.maker.output/pelargonium_rnd1.all.maker.repeats.gff #pre-identified repeat elements from an external GFF3 file
    prok_rm=0 #forces MAKER to repeatmask prokaryotes (no reason to change this), 1 = yes, 0 = no
    softmask=1 #use soft-masking rather than hard-masking in BLAST (i.e. seg and dust filtering)
    
    #-----Gene Prediction
    snaphmm= snap/round1/pelargonium_rnd1.zff.length50_aed0.25.hmm #SNAP HMM file
    gmhmm= #GeneMark HMM file
    augustus_species= pelargonium #Augustus gene prediction species model
    fgenesh_par_file= #FGENESH parameter file
    pred_gff= #ab-initio predictions from an external GFF3 file
    model_gff= #annotated gene models from an external GFF3 file (annotation pass-through)
    run_evm=0 #run EvidenceModeler, 1 = yes, 0 = no
    est2genome=1 #infer gene predictions directly from ESTs, 1 = yes, 0 = no
    protein2genome=1 #infer predictions from protein homology, 1 = yes, 0 = no
    trna=1 #find tRNAs with tRNAscan, 1 = yes, 0 = no
    snoscan_rrna= #rRNA file to have Snoscan find snoRNAs
    snoscan_meth= #-O-methylation site fileto have Snoscan find snoRNAs
    unmask=0 #also run ab-initio prediction programs on unmasked sequence, 1 = yes, 0 = no
    
    #-----Other Annotation Feature Types (features MAKER doesn't recognize)
    other_gff= #extra features to pass-through to final MAKER generated GFF3 file
    
    #-----External Application Behavior Options
    alt_peptide=C #amino acid used to replace non-standard amino acids in BLAST databases
    cpus=20 #max number of cpus to use in BLAST and RepeatMasker (not for MPI, leave 1 when using MPI)
    
    #-----MAKER Behavior Options
    max_dna_len= 300000 #length for dividing up contigs into chunks (increases/decreases memory usage)
    min_contig=1 #skip genome contigs below this length (under 10kb are often useless)
    
    pred_flank=200 #flank for extending evidence clusters sent to gene predictors
    pred_stats=0 #report AED and QI statistics for all predictions as well as models
    AED_threshold=1 #Maximum Annotation Edit Distance allowed (bound by 0 and 1)
    min_protein=0 #require at least this many amino acids in predicted proteins
    alt_splice=1 #Take extra steps to try and find alternative splicing, 1 = yes, 0 = no
    always_complete=0 #extra steps to force start and stop codons, 1 = yes, 0 = no
    map_forward=0 #map names and attributes forward from old GFF3 genes, 1 = yes, 0 = no
    keep_preds=0 #Concordance threshold to add unsupported gene prediction (bound by 0 and 1)
    
    split_hit=20000 #length for the splitting of hits (expected max intron size for evidence alignments)
    min_intron=20 #minimum intron length (used for alignment polishing)
    single_exon=0 #consider single exon EST evidence when generating annotations, 1 = yes, 0 = no
    single_length=250 #min length required for single exon ESTs if 'single_exon is enabled'
    correct_est_fusion=0 #limits use of ESTs in annotation to avoid fusion genes
    
    tries=2 #number of times to try a contig if there is a failure for some reason
    clean_try=0 #remove all data from previous run before retrying, 1 = yes, 0 = no
    clean_up=0 #removes theVoid directory with individual analysis files, 1 = yes, 0 = no
    TMP= #specify a directory other than the system default temporary directory for temporary files
2. run maker

### Run Maker 3rd run with retrained SNAP and Augustus using the same procedure