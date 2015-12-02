# BaseMod: PacBio DNA Modification Sequence Analysis

TODO

Table of contents
=================

  * [Overview](#overview)
  * [Manual](#manual)
    * [Running with SMRTLink](#running-with-smrtlink)
    * [Running on the Command Line](#running-on-the-command-line)
    * [Running on the Command Line with pbsmrtpipe](#running-on-the-command-line-with-pbsmrtpipe)
  * [Advanced Analysis Options](#advanced-analysis-options)
    * [SMRTLink/pbsmrtpipe BaseMod Options](#smrtlinkpbsmrtpipe-basemod-options)
    * [PBAlign Options](#pbalign-options)
    * [kineticsTools Options](#kineticstools-options)
    * [MotifMaker Options](#motifmaker-options)
  * [Files](#files)
    * [PBAlign Files](#pbalign-files)
    * [kineticsTools Files](#kineticstools-files)
    * [MotifMaker Files](#motifmaker-files)
  * [Algorithm Modules](#algorithm-modules)
  * [Glossary](#glossary)

## Overview

![base modification and motif analysis](https://cloud.githubusercontent.com/assets/12494820/11547830/d8a1a4aa-990b-11e5-885b-4a5d5db276e8.png)

Analyses are performed in four stages, PBAlign, kineticsTools, MotifMaker Find, and MotifMaker Reprocess. 
* __PBAlign__
  * todo
* __kineticsTools__
  * todo 
* __MotifMaker__
  * todo


## Manual

### Running with SMRTLink

To run Isoseq using SMRTLink, follow the usual steps for analysing data on SMRTLink. TODO: Link to document explaining SMRTLink.

### Running on the Command Line

On the command line, the analysis is performed in 4 steps:

1. Run PBAlign on your unaligned subreads, generating an aligned BAM.
2. Run kineticsTools on your aligned BAM, generating a GFF and CSV containing base modification information.
3. Run MotifMaker on your GFF generated from kineticsTools, generating a CSV containing motif information.
4. Run MotifMaker on your base modification GFF and motif CSV, generating a (todo)

__Step 1. PBAlign__

First, align your sequences to your chosen reference.

     pbalign --concordant --hitPolicy=randombest --minAccuracy 70 --minLength 50 --algorithmOptions="-minMatch 12 -bestn 10 -minPctIdentity 70.0" subreads.bam reference.fasta aligned_subreads.bam

Where `reference.fasta` contains your reference sequences.

Where `subreads.bam` contains your unaligned reads.

Where `aligned_subreads.bam` is where your aligned reads will be stored. 

__Step 2. kineticsTools__

Next, analyze your aligned sequences for base modifications.

     ipdSummary aligned_subreads.bam --reference reference.fasta --gff basemods.gff --csv basemods.csv --pvalue 0.001 --numWorkers 16 --identify m4C,m6A
 
**Note:** You will need to have an index file for your `reference.fasta` and for your `aligned_subreads.bam`. 

To index your `reference.fasta`, use `samtools faidx reference.fasta`. 

To index your `aligned_subreads.bam`, use `pbindex aligned_subreads.bam`. 

__Step 3. MotifMaker Find__

Next, identify consensus motifs.

     motifMaker find -f reference.fasta -g basemods.gff -o motifs.csv

__Step 4. MotifMaker Reprocess__

Finally, generate a GFF file of all modifications that are part of motifs. 

     motifMaker reprocess -f reference.fasta -g basemods.gff -m motifs.csv -o motifs.gff


### Running on the Command Line with pbsmrtpipe

## Advanced Analysis Options

### SMRTLink/pbsmrtpipe Basemod Options

### PBAlign Options

| Type  |  Parameter          |     Example      |  Explanation      |
| ----- | ------------------ | ---------------- | ----------------- |
| positional | Input File |  unaligned.bam | SubreadSet or unaligned .bam |
| positional | Reference File |  reference.fasta | Reference DataSet or FASTA file |
| positional | Output File |  aligned.bam | Output AlignmentSet file |
| optional | Help | -h, --help | show this help message and exit |
| optional | Version | -v, --version | show program's version number and exit |
| optional | Verbose | --verbose | No Description (greg) |
| optional | Debug | --debug | Writes the debug reporting to stdout |
| optional | Profile | --profile | Print runtime profile at exit |
| optional input | Region Table | --regionTable REGIONTABLE | Specify a region table for filtering reads. |
| optional input | Configuration File | --configFile CONFIGFILE | Specify a set of user-defined argument values. |
| optional input | Pulse File | --pulseFile PULSEFILE | When input reads are in fasta format and output is a cmp.h5 this option can specify pls.h5 or bas.h5 or FOFN files from which pulse metrics can be loaded for Quiver. |
| alignment | Algorithm | --algorithm {blasr, bowtie, gmap} | Select an aligorithm from ('blasr', 'bowtie', 'gmap'). Default algorithm is blasr. |
| alignment | Maximum Hits | --maxHits MAXHITS | The maximum number of matches of each read to the reference sequence that will be evaluated. Default value is 10. |
| alignment | Minimum Anchor Size | --minAnchorSize MINANCHORSIZE | The minimum anchor size defines the length of the read that must match against the reference sequence. Default value is 12. |
| alignment | Use CCS | --useccs {useccs, useccsall, useccsdenovo} | Map the ccsSequence to the genome first, then align subreads to the interval that the CCS reads mapped to. useccs: only maps subreads that span the length of the template. useccsall: maps all subreads. useccsdenovo: maps ccs only. |
| alignment | No Split Subreads | --noSplitSubreads | Do not split reads into subreads even if subread regions are available. Default value is False. |
| alignment | Concordant | --concordant | Map subreads of a ZMW to the same genomic location. |
| alignment | Number of Threads | --nproc NPROC | Number of threads. Default value is 8. |
| alignment | Algorithm Options | --algorithmOptions ALGORITHMOPTIONS | Pass alignment options through. |
| filter criteria | Maximum Divergence | --maxDivergence MAXDIVERGENCE | The maximum allowed percentage divergence of a read from the reference sequence. Default value is 30.0. |
| filter criteria | Minimum Accuracy | --minAccuracy MINACCURACY | The minimum percentage accuracy of alignments that will be evaluated. Default value is 70.0. |
| filter criteria | Minimum Length | --minLength MINLENGTH | The minimum aligned read length of alignments that will be evaluated. Default value is 50. |
| filter criteria | Score Cutoff | --scoreCutoff SCORECUTOFF | The worst score to output an alignment. |
| filter criteria | Hit Policy | --hitPolicy {randombest, allbest, random, all, leftmost} | Specify a policy for how to treat multiple hit random: selects a random hit. all: selects all hits. allbest: selects all the best score hits. randombest: selects a random hit from all best score hits. leftmost: selects a hit which has the best score and the smallest mapping coordinate in any reference. Default value is randombest. |
| filter criteria | Filter Adapter-Only | --filterAdapterOnly |  If specified, do not report adapter-only hits using annotations with the reference entry. |
| for cmp.h5 | For Quiver | --forQuiver | The output cmp.h5 file which will be sorted, loaded with pulse QV information, and repacked, so that it can be consumed by quiver directly. This requires the input file to be in PacBio bas/pls.h5 format, and --useccs must be None. Default value is False. |
| for cmp.h5 | Load QVs | --loadQVs | Similar to --forQuiver, the only difference is that --useccs can be specified. Default value is False. |
| for cmp.h5 | By Read | --byread | Load pulse information using -byread option instead of -bymetric. Only works when --forQuiver or --loadQVs are set. Default value is False. |
| for cmp.h5 | Metrics | --metrics METRICS | Load the specified (comma-delimited list of) metrics instead of the default metrics required by quiver. This option only works when --forQuiver  or --loadQVs are set. Default: DeletionQV, DeletionTag, InsertionQV, MergeQV, SubstitutionQV |
| miscellaneous | Seed | --seed SEED | Initialize the random number generator with a none-zero integer. Zero means that current system time is used. Default value is 1. |
| miscellaneous | Temporary Directory | --tmpDir TMPDIR | Specify a directory for saving temporary files. Default is /scratch. |

### kineticsTools Options

|  Parameter          |     Example      |  Explanation      |
| ------------------ | ---------------- | ----------------- |
| Input Reads | aligned_subreads.bam | This is a positional argument and the first argument to be specified. Can be a BAM or Alignment DataSet. |
| Help |  -h, --help | show this help message and exit |
| Version | -v, --version | show program's version number and exit |
| Emit Tool Contract | --emit-tool-contract | Emit Tool Contract to stdout (default: False) (nat) |
| Resolved Tool Contract | --resolved-tool-contract RESOLVED_TOOL_CONTRACT | Run Tool directly from a PacBio Resolved tool contract (default: None) (nat) |
| Log Level | --log-level {DEBUG, INFO, WARNING, ERROR, CRITICAL} | Set log level (default: INFO) |
| Debug Mode | --debug | Debug to stdout (default: False) |
| Reference File | --reference REFERENCE | Fasta or Reference DataSet (default: None) |
| Modifications GFF | --gff GFF | Output GFF file of modified bases (default: None) |
| Modifications CSV | --csv CSV | Output CSV file out per-nucleotide information (default: None) |
| Number of Jobs | --numWorkers NUMWORKERS, -j NUMWORKERS | Number of thread to use (-1 uses all logical cpus) (default: 1) |
| P-Value | --pvalue PVALUE | P-value cutoff (default: 0.01) |
| Maximum Length | --maxLength MAXLENGTH | Maximum number of bases to process per contig (default: 3000000000000) |
| Indentify |  --identify IDENTIFY | Specific modifications to identify (comma-separated list). Currrent options are m6A, m4C, m5C_TET. Cannot be used with --control. (default: ) |
| Methyl Fraction | --methylFraction | In the --identify mode, add --methylFraction to command line to estimate the methylated fraction, along with 95% confidence interval bounds. (default: False) |
| Output Files | --outfile OUTFILE | Use this option to generate all possible output files. Argument here is the root filename of the output files. (default: None) |
| m5C Scores | --m5Cgff M5CGFF | Name of output GFF file containing m5C scores (default: None) |
| m5C Classifier | --m5Cclassifer M5CCLASSIFIER | Specify csv file containing a 127 x 2 matrix (default: None) |
| CSV H5 Format | -csv_h5 CSV_H5 | Name of csv output to be written in hdf5 format. (default: None) |
| Pickle | --pickle PICKLE | Name of output pickle file. (default: None) (nat) |
| Summary H5 | --summary_h5 SUMMARY_H5 | Name of output summary h5 file. (default: None) |
| Multisite Detection | --ms_csv MS_CSV | Multisite detection CSV file. (default: None) |
| Case-Control Mode | --control CONTROL  | cmph.h5 file containing a control sample. Tool will perform a case-control analysis (default: None) |
| LDA | --useLDA | Set this flag to debug LDA for m5C/Ca5C detection (default: False) |
| Help | --paramsPath PARAMSPATH | Directory containing in-silico trained model for each chemistry (default: /mnt/secondary/builds/full/3.0.0/prod/smrtanalysis_3.0.0.167527/private/pacbio/pythonpkgs/kineticsTools/lib/python2.7/site-packages/kineticsTools/resources) (nat) |
| Minimum Call Coverage | --minCoverage MINCOVERAGE | Minimum coverage required to call a modified base (default: 3) |
| Maxmimum Queue Size | --maxQueueSize MAXQUEUESIZE | Max Queue Size (default: 20) |
| Maximum Coverage | --maxCoverage MAXCOVERAGE | Maximum coverage to use at each site (default: -1) |
| MapQV Threshold | --mapQvThreshold MAPQVTHRESHOLD | No Description (nat) |
| IPD Model | --ipdModel IPDMODEL | Alternate synthetic IPD model HDF5 file (default: None) |
| Model Iterations | --modelIters MODELITERS | [Internal] Number of GBM model iteration to use (default: -1) (nat) |
| IPD Percentile Cap | --cap_percentile CAP_PERCENTILE | Global IPD percentile to cap IPDs at (default: 99.0) |
| Minimum MethylFraction Coverage | --methylMinCov METHYLMINCOV | Do not try to estimate methylFraction unless coverage is at least this. (default: 10) |
| Minimum Modification Coverage | --identifyMinCov IDENTIFYMINCOV | Do not try to identify the modification type unless coverage is at least this. (default: 5) |
| Maximum Alignments |  --maxAlignments MAXALIGNMENTS | Maximum number of alignments to use for a given window (default: 1500) |
| Reference Window |  -w REFERENCEWINDOWSASSTRING, --referenceWindow REFERENCEWINDOWSASSTRING, --referenceWindows REFERENCEWINDOWSASSTRING, --refContigs REFERENCEWINDOWSASSTRING | The window (or multiple comma-delimited windows) of the reference to be processed, in the format refGroup [:refStart-refEnd] (default: entire reference). (default: None) |
| Reference Contig Index | --refContigIndex REFCONTIGINDEX | For debugging purposes only - rather than enter a reference contig name, simply enter an index (default: -1) (nat) |
| Reference Window File | -W REFERENCEWINDOWSASSTRING, --referenceWindowsFile REFERENCEWINDOWSASSTRING, --refContigsFile REFERENCEWINDOWSASSTRING | A file containing reference window designations, one per line (default: None) |
| Skip Unregognized Contigs | --skipUnrecognizedContigs SKIPUNRECOGNIZEDCONTIGS | Whether to skip, or abort, unrecognized contigs in the -w/-W flags (default: False) |
| Alignment Window | --alignmentSetRefWindows | Use refWindows in dataset (default: False) |
| Thread | --threaded, -T | Run threads instead of processes (for debugging purposes only) (default: False) (nat) |
| Profile | --profile  | Enable Python-level profiling (using cProfile). (default: False) |
| PBD Debugger | --usePdb | Enable dropping down into pdb debugger if an Exception is raised. (default: False) (nat) |
| Seed | --seed RANDOMSEED | Random seed (for development and debugging purposes only) (default: None) |
| Verbose | --verbose | Print verbose job information to stdout | 

### MotifMaker Options

MotifMaker options, except for `--help`, require the specification of a command first- either `find` or `reprocess`. 

| Command  |  Parameter          |     Example      |  Explanation      |
| ----- | ------------------ | ---------------- | ----------------- |
| | Help | -h, --help | show this help message and exit |
| find | Reference FASTA |  -f, --fasta | Reference fasta file |
| find | Modifications GFF | -g, --gff | modifications.gff or .gff.gz file |
| find | Minimum Qmod Score |  -m, --minScore | Minimum Qmod score to use in motif finding (default: 30.0) |
| find | Motif CSV | -o, --output | Output motifs csv file |
| find | Parallelize | -p, --parallelize | Parallelize motif finder (default: true) |
| find | Motif XML | -x, --xml | Output motifs xml file |
| reprocess | Modifications CSV | -c, --csv |  Raw modifications.csv file |
| reprocess | Reference FASTA | -f, --fasta |  Reference fasta file |
| reprocess | Modifications GFF | -g, --gff |  original modifications.gff or .gff.gz file |
| reprocess | Minimum MethylFraction | --minFraction |  Only use motifs above this methylated fraction (default: 0.0) |
| reprocess | Motifs CSV | -m, --motifs |  motifs csv |
| reprocess | Reprocessed Modifications GFF | -o, --output |  Reprocessed modifications.gff file |

## Files

### PBAlign Files

### kineticsTools Files

__Modifications CSV File (basemods.csv)__

The modifications.csv file contains one row for each (reference position, strand) pair that appeared in the dataset with coverage at least x. x defaults to 3, but is configurable with ‘–minCoverage’ flag to ipdSummary.py. The reference position index is 1-based for compatibility with the gff file the R environment.

The output columns vary depending on whether `--control` was used to run in case-control mode, or whether the program ran in the default in-silico control mode.

**in-silico control mode output columns**

|Column	| Description |
| ----- | ----------- |
|refId	| reference sequence ID of this observation |
tpl		| 1-based template position |
strand		| native sample strand where kinetics were generated. ‘0’ is the strand of the original FASTA, ‘1’ is opposite strand from FASTA	| 
base		| the cognate base at this position in the reference	| 
score		| Phred-transformed pvalue that a kinetic deviation exists at this position	| 
tMean		| capped mean of normalized IPDs observed at this position	| 
tErr		| capped standard error of normalized IPDs observed at this position (standard deviation / sqrt(coverage)	| 
modelPrediction		| normalized mean IPD predicted by the synthetic control model for this sequence context	| 
ipdRatio		| tMean / modelPrediction	| 
coverage		| count of valid IPDs at this position (see Filtering section for details)	| 
frac		| estimate of the fraction of molecules that carry the modification	| 
fracLow		| 2.5% confidence bound of frac estimate	| 
fracUpp		| 97.5% confidence bound of frac estimate	| 

**case-control mode output columns**

|Column	| Description |
| ----- | ----------- |
|refId		| reference sequence ID of this observation|
|tpl		| 1-based template position|
|strand		| native sample strand where kinetics were generated. ‘0’ is the strand of the original FASTA, ‘1’ is opposite strand from FASTA|
|base		| the cognate base at this position in the reference|
|score		| Phred-transformed pvalue that a kinetic deviation exists at this position|
|caseMean		| mean of normalized case IPDs observed at this position|
|controlMean		| mean of normalized control IPDs observed at this position|
|caseStd		| standard deviation of case IPDs observed at this position|
|controlStd		| standard deviation of control IPDs observed at this position|
|ipdRatio		| tMean / modelPrediction|
|testStatistic		| t-test statistic|
|coverage		| mean of case and control coverage|
|controlCoverage		| count of valid control IPDs at this position (see Filtering section for details)|
|caseCoverage		| count of valid case IPDs at this position (see Filtering section for details)|

__Modifications GFF File (basemods.gff)__

The modifications.gff is compliant with the GFF Version 3 [specification](http://www.sequenceontology.org/gff3.shtml). Each template position / strand pair whose p-value exceeds the pvalue threshold appears as a row. The template position is 1-based, per the GFF spec. The strand column refers to the strand carrying the detected modification, which is the opposite strand from those used to detect the modification. The GFF confidence column is a Phred-transformed pvalue of detection.

The modifications.gff file will not work directly with most genome browsers. You will likely need to make a copy of the GFF file and convert the _seqid_ columns from the generic ‘ref0000x’ names generated by PacBio, to the FASTA headers present in the original reference FASTA file. The mapping table is written in the header of the modifications.gff file in #sequence-header tags. This issue will be resolved in the 1.4 release of kineticsTools.

The auxiliary data column of the GFF file contains other statistics which may be useful downstream analysis or filtering. In particular the coverage level of the reads used to make the call, and +/- 20bp sequence context surrounding the site.

|Column	| Description |
| ----- | ----------- |
|seqid	 | Fasta contig name|
|source	 | Name of tool – ‘kinModCall’|
|type	 | Modification type – in identification mode this will be m6A, m4C, or m5C for identified bases, or the generic tag ‘modified_base’ if a kinetic event was detected that does not match a known modification signature|
|start	 | Modification position on contig|
|end	 | Modification position on contig|
|score	 | Phred transformed p-value of detection - this is the single-site detection p-value|
|strand	 | Sample strand containing modification|
|phase	 | Not applicable|
|attributes	 | Extra fields relevant to base mods. IPDRatio is traditional IPDRatio, context is the reference sequence -20bp to +20bp around the modification, and coverage level is the number of IPD observations used after Mapping QV filtering and accuracy filtering. If the row results from an identified modification we also include an identificationQv tag with the from the modification identification procedure. identificationQv is the phred-transformed probability of an incorrect identification, for bases that were identified as having a particular modification. frac, fracLow, fracUp are the estimated fraction of molecules carrying the modification, and the 5% confidence intervals of the estimate. The methylated fraction estimation is a beta-level feature, and should only be used for exploratory purposes. |

### MotifMaker Files

__Motifs GFF File (motifs.gff)__

This is a reprocessed version of modifications.gff with the following changes. If a detected modification occurs on a motif detected by the motif finder, the modification is annotated with motif data. An attribute ‘motif’ is added containing the motif string, and an attribute ‘id’ is added containing the motif id, which is the motif string for unpaired motifs or ‘motifString1/motifString2’ for paired motifs. If a motif instance exists in the genome, but was not detected in modifications.gff, an entry is added to motifs.gff, indicating the presence of that motif and the kinetics that were observed at that site.

__Motifs CSV File (motifs.csv)__

This file summarizes the modified motifs discovered by the tool. The CSV contains one row per detected motif, with the following columns.

|Column	| Description |
| ----- | ----------- |
|motifString	| Detected motif sequence| 
|centerPos	| Position in motif of modification (0-based)| 
|fraction	| Fraction of instances of this motif with modification QV above the QV threshold| 
|nDetected	| Number of instances of this motif with above threshold| 
|nGenome	| Number of instances of this motif in reference sequence| 
|groupTag	| A string idetifying the motif grouping. For paired motifs this is “<motifString1>/<motifString2>”, For unpaired motifs  this equals motifString| 
|partnerMotifString	| motifString of paired motif (motif with reverse-complementary motifString)| 
|meanScore	| Mean Modification Qv of detected instances| 
|meanIpdRatio	| Mean IPD ratio of detected instances| 
|meanCoverage	| Mean coverage of detected instances| 
|objectiveScore	| Objective score of this motif in the motif finder algorithm| 

## Algorithm Modules

### kineticsTools

##### Synthetic Control

Studies of the relationship between IPD and sequence context reveal that most of the variation in mean IPD across a genome can be predicted from a 12-base sequence context surrounding the active site of the DNA polymerase. The bounds of the relevant context window correspond to the window of DNA in contact with the polymerase, as seen in DNA/polymerase crystal structures. To simplify the process of finding DNA modifications with PacBio data, the tool includes a pre-trained lookup table mapping 12-mer DNA sequences to mean IPDs observed in C2 chemistry.

##### Filtering and Trimming

kineticsTools uses the Mapping QV generated by BLASR and stored in the cmp.h5 file to ignore reads that aren’t confidently mapped. The default minimum Mapping QV required is 10, implying that BLASR has 90% confidence that the read is correctly mapped. Because of the range of readlengths inherent in PacBio dataThis can be changed in using the –mapQvThreshold command line argument, or via the SMRTPortal configuration dialog for Modification Detection.

There are a few features of PacBio data that require special attention in order to acheive good modification detection performance. kineticsTools inspects the alignment between the observed bases and the reference sequence – in order for an IPD measurement to be included in the analysis, the PacBio read sequence must match the reference sequence for k around the cognate base. In the current module k=1 The IPD distribution at some locus be thought of as a mixture between the ‘normal’ incorporation process IPD, which is sensitive to the local sequence context and DNA modifications and a contaminating ‘pause’ process IPD which have a much longer duration (mean >10x longer than normal), but happen rarely (~1% of IPDs). Note: Our current understanding is that pauses do not carry useful information about the methylation state of the DNA, however a more careful analysis may be warranted. Also note that modifications that drastically increase the Roughly 1% of observed IPDs are generated by pause events. Capping observed IPDs at the global 99th percentile is motivated by theory from robust hypothesis testing. Some sequence contexts may have naturally longer IPDs, to avoid capping too much data at those contexts, the cap threshold is adjusted per context as follows: capThreshold = max(global99, 5*modelPrediction, percentile(ipdObservations, 75))

##### Statistical Testing

We test the hypothesis that IPDs observed at a particular locus in the sample have a longer means than IPDs observed at the same locus in unmodified DNA. If we have generated a Whole Genome Amplified dataset, which removes DNA modifications, we use a case-control, two-sample t-test. This tool also provides a pre-calibrated ‘synthetic control’ model which predicts the unmodified IPD, given a 12 base sequence context. In the synthetic control case we use a one-sample t-test, with an adjustment to account for error in the synthetic control model.

### MotifMaker

## Glossary
