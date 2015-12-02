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

     ipdSummary aligned.subreads.bam --reference /path/to/reference.fasta --gff basemods.gff --csv basemods.csv --pvalue 0.001 --numWorkers 16 --identify m4C,m6A
 
**Note:** that you will need to have an index file for your reference.fasta and for your aligned.subreads.bam. 

To index your reference.fasta, use `samtools faidx reference.fasta`. 

To index your aligned.subreads.bam, use `pbindex aligned.subreads.bam`. 

### Running on the Command Line with pbsmrtpipe

## Advanced Analysis Options

### SMRTLink/pbsmrtpipe Basemod Options

### PBAlign Options

### kineticsTools Options

### MotifMaker Options

## Algorithm Modules

## Glossary
