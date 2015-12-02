# BaseMod: PacBio DNA Modification Sequence Analysis

TODO

Table of contents
=================

  * [Overview](#overview)
  * [Manual](#manual)
    * [Running with SMRTLink](#running-with-smrtanalysis)
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
 
**Note:** You will need to have an index file for your `reference.fasta` and for your `aligned.subreads.bam`. 

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

### MotifMaker Options

## Files

### PBAlign Files

### kineticsTools Files

__Modifications File (modifications.csv)__

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

### MotifMaker Files


## Algorithm Modules

## Glossary
