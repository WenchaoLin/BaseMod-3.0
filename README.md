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

Analyses are performed in three stages, PBAlign, kineticsTools, and MotifMaker. 
* __PBAlign__
  * todo
* __kineticsTools__
  * todo 
* __MotifMaker__
  * todo


## Manual

### Running with SMRTLink

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

### kineticsTools Options

|  Parameter          |     Example      |  Explanation      |
| ------------------ | ---------------- | ----------------- |
| Input Reads | aligned_subreads.bam | This is a positional argument and the first argument to be specified. Can be a BAM or Alignment DataSet. |
| Help |  -h, --help | show this help message and exit |
| Version | -v, --version | show program's version number and exit |
| Emit Tool Contract | --emit-tool-contract | Emit Tool Contract to stdout (default: False) |
| Resolved Tool Contract | --resolved-tool-contract RESOLVED_TOOL_CONTRACT | Run Tool directly from a PacBio Resolved tool contract (default: None) |
| Log Level | --log-level {DEBUG, INFO, WARNING, ERROR, CRITICAL} | Set log level (default: INFO) |
| Debug Mode | --debug | Debug to stdout (default: False) |
| Reference File | --reference REFERENCE | Fasta or Reference DataSet (default: None) |
| Modifications GFF | --gff GFF | Output GFF file of modified bases (default: None) |
| Modifications CSV | --csv CSV | Output CSV file out per-nucleotide information (default: None) |
| Help |  -h, --help | show this help message and exit |
| Help |  -h, --help | show this help message and exit |
| Help |  -h, --help | show this help message and exit |
| Help |  -h, --help | show this help message and exit |
| Help |  -h, --help | show this help message and exit |
| Help |  -h, --help | show this help message and exit |
| Help |  -h, --help | show this help message and exit |




### MotifMaker Options

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

## Glossary
