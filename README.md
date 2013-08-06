Table of Content
================
* [Overview](#Overview)
* [Change log](#Change)
 * [Release candidate (RC) version 1.3](#RC13)
 * [Release candidate (RC) version 1.2](#RC12)
 * [Release candidate (RC) version 1.1](#RC11)
 * [Release candidate (RC) version 1.0](#RC10)
* [Prerequisites](#Prerequisites)
 * [Install required perl packages](#irpp)
 * [Install required software](#irs)
 * [Download required database](#drd)
* [Usage](#Usage)
 * [fastq QC](#fqc)
 * [bam QC](#bqc)
 * [vcf QC](#vqc)
* [Example](#Example)
 * [Download example data](#ded)
 * [fastq QC example](#fqcE)
 * [bam QC example](#bqcE)
 * [vcf QC example](#vqcE)
* [Results](#Results)
 * [Introduction for fastq QC result](#ifr)
 * [Introduction for bam QC result](#ibr)
 * [Introduction for vcf QC result](#ivr)
* [Contacts](#Contacts)

<a name="Overview"/>
# Overview #
Say something

<a name="Change"/>
# Change log #

<a name="RC13">
## Release candidate (RC) version 1.3 on August 06, 2013
Release candidate version 1.3 for test
 * The ANNOVAR annotation function in vcf QC was changed;
 * The codes in bam QC and fastq QC were improved;

<a name="RC12">
## Release candidate (RC) version 1.2 on July 31, 2013
Release candidate version 1.2 for test
 * The algorithm in bam QC was improved and the memory usage was highly decreased;

<a name="RC11">
## Release candidate (RC) version 1.1 on July 29, 2013
Release candidate version 1.1 for test
 * Documents were improved;
 * Some bugs were fixed;
 * Example files were provided;

<a name="RC10">
## Release candidate (RC) version 1.0 on July 26, 2013
Release candidate version 1.0 for test

<a name="Prerequisites"/>
# Prerequisites #
<a name="irpp"/>
## Install required perl packages ##

There are several other packages needed to be installed on your computer.

	#go the the folder where your software is.
	#And test whether all the required modules have been installed.
	bash test.modules

The successful output would look like this

	ok   File::Basename
	ok   FindBin
	ok   Getopt::Long
	ok   HTML::Template
	ok   source::bamSummary
	ok   source::fastqSummary
	ok   source::makeReport
	ok   source::vcfSummary

Otherwise, it may look like this

	ok   File::Basename
	ok   FindBin
	ok   Getopt::Long
	fail HTML::Template is not usable (it or a sub-module is missing)
	ok   source::bamSummary
	ok   source::fastqSummary
	ok   source::makeReport
	ok   source::vcfSummary

Then you need to install the missing packages from [CPAN](http://www.cpan.org/). A program was also provided to make the package installation more convenient.
	
	#if HTML::Template was missing
	bash install.modules HTML::Template

<a name="irs"/>
## Install required software ##

### R ###

R is a free software environment for statistical computing and graphics. It could be downloaded [here](http://www.r-project.org/).

After you install R and add R bin file to your Path, the software can find and use R automatically. Or you can change the qc3.pl script and tell the program where the R is on your computer. Here is the line you need to modify.

	$config{'RBin'}        = "R";       #where the R bin file is

### samtools ###

SAM Tools provide various utilities for manipulating alignments in the SAM format, including sorting, merging, indexing and generating alignments in a per-position format. It could be downloaded [here](http://samtools.sourceforge.net/).

After you install SAMtools and add SAMtools bin file to your Path, the software can find and use SAMtools automatically. Or you can change the qc3.pl script and tell the program where the SAMtools is on your computer. Here is the line you need to modify.

	$config{'samtoolsBin'} = "samtools";	#where the SAMtools bin file is


### annovar ###

**Please note: ANNOVAR and ANNOVAR database are not essential for vcf QC. If not provided or not found, ANNOVAR annotation will not be performed but other functions in vcf QC work well**.

ANNOVAR is an efficient software tool to utilize update-to-date information to functionally annotate genetic variants detected from diverse genomes. We used ANNOVAR for annotation in vcf QC. It could be downloaded from [ANNOVAR website](http://www.openbioinformatics.org/annovar/).

After you install ANNOVAR and add ANNOVAR bin file to your Path, the software can find and use ANNOVAR automatically. Or you can change the qc3.pl script and tell the program where the ANNOVAR is on your computer. Here is the line you need to modify.

	$config{'annovarBin'}  = "annotate_variation.pl";		#where the ANNOVAR bin file is
	$config{'annovarConvert'}  = "convert2annovar.pl";		#where the ANNOVAR convert bin file is
	$config{'annovarOption'}  = "-buildver hg19 -protocol refGene,snp137 -operation g,f --remove --otherinfo "; #other options for ANNOVAR

The ANNOVAR database files were also needed for ANNOVAR annotation. Please refer to the [ANNOVAR document](http://www.openbioinformatics.org/annovar/annovar_db.html) for the  database preparation. Or you can simply use the following codes to prepare ANNOVAR database files.

	#assume ANNOVAR was installed and added to Path. Then you want to download ANNOVAR database in annovarDatabase directory.
	mkdir annovarDatabase
	cd annovarDatabase
	annotate_variation.pl -downdb -buildver hg19 -webfrom annovar snp137 humandb/
	annotate_variation.pl -downdb -buildver hg19 -webfrom annovar refGene humandb/

<a name="drd"/>
## Download required database ##
A gtf and/or a bed file was taken as database files in bam QC, and the position annotation for each sequence was exported from them. At least one of them should be provided in bam QC. These files could be downloaded at [UCSC table browser](http://genome.ucsc.edu/cgi-bin/hgTables?command=start). The format of these files were: 

	hg19_protein_coding.bed
	Column1	    Column2         Column3
	Chromosome	StartPosition	EndPosition

	Homo_sapiens.GRCh37.63_protein_coding_chr1-22-X-Y-M.gtf
	Column1   	...	Column3	Column4	        Column5	...
	Chromosome	...	exon	StartPosition	EndPosition	...

<a name="Usage"/>
# Usage #
Example code for running QC with given example dataset could be:

	perl qc3.pl -m module -i inputFile -o outputDir (some other parameters)
	
	-m [module]         QC module used. It should be f (fastq QC), b (bam QC), or v (vcf QC).
	-i [inputFile]      Input file. It should be a file list including all analyzed files in fastq QC and bam QC. In vcf QC, it should be the vcf file.
	-o [outputDir]      Output directory for QC result. If the directory doesn't exist, it would be created. If the directory already existed, the files in it would be deleted.
	-t [int]            Threads used in analysis. The default value is 4. This parameter only valid for fastq and bam QC. Only one thread would be used in vcf QC.

Here is more details for each module:

<a name="fqc"/>
## fastq QC 

	perl qc3.pl -m f -i inputFileList -o outputDir -t threads

	-p				whether the fastq files were pair-end data. -p = pair-end.

<a name="bqc"/>
## bam QC

	perl qc3.pl -m b -i inputFileList -o outputDir -t threads

	-r  [database]	A targetregion file.
	-g  [database]	A gtf file.
	-cm [int]	    Calculation method for data summary, should be 1 or 2. Method 1 means mean and method 2 means median. The default value is 1.
	-d				whether the depth in on-/off-target regions will be calculated, -d = will be calculated.

<a name="vqc"/>
## vcf QC

	perl qc3.pl -m v -i inputFile -o outputDir

	-s [int]		    Method used in consistence calculation, should be 1 or 2. In method 1, only the two samples will completely same allele will be taken as consist. In method 2, the two samples satisfy the criterion in method 1, or if the two samples were 0/0 vs 0/1 but 0/0 sample has some read counts in alternative allele, they will be taken as consistent. The default value is 1.
	-c [database]	A file indicating the filter arguments for vcf files. If not specified, the default file 'GATK.cfg' in qc3 directory with GATK best practices recommended arguments will be used.
	-a [database]	Directory of annovar database.

<a name="Example"/>
# Example #

The example files can be downloaded at [sourceforge](http://sourceforge.net/projects/qc3/files/).

You need to download and extract it to a directory. Then the example code for running QC with given example data set could be:

<a name="ded"/>
## download example data ##
	#download and extract example data into exampleDir
	mkdir exampleDir	
	cd exampleDir
	wget http://sourceforge.net/projects/qc3/files/example.tar.gz/download
	tar zxvf example.tar.gz
	ls

<a name="fqcE"/>
## fastq QC ##

	#assume qc3.pl in qc3Dir, examples in exampleDir
	cd exampleDir
	cd fastq
	perl qc3Dir/qc3.pl -m f -i example_fastq_list.txt -o example_fastq_result -p


<a name="bqcE"/>
## bam QC

	#assume qc3.pl in qc3Dir, examples in exampleDir
	cd exampleDir
	cd bam

	#a simple example, with only bed file
	perl qc3Dir/qc3.pl -m b -i example_bam_list.txt -r ../bamQcDatabase/hg19_protein_coding.bed.Part -o example_bam_result1
	
	#a example with both bed and gtf file, and will calculate the depth
	perl qc3Dir/qc3.pl -m b -i example_bam_list.txt -r ../bamQcDatabase/hg19_protein_coding.bed.Part -g ../bamQcDatabase/Homo_sapiens.GRCh37.63_protein_coding_chr1-22-X-Y-M.gtf.Part -o example_bam_result2

<a name="vqcE"/>
## vcf QC
	
	#assume qc3.pl in qc3Dir, examples in exampleDir
	cd exampleDir
	cd vcf

	#a simple example, will not perform ANNOVAR annotation
	perl qc3Dir/qc3.pl -m v -i CV-7261.vcf.top40000.txt -o example_vcf_result1
	
	#assume ANNOVAR database in annovarDatabase, will perform ANNOVAR annotation
	perl qc3Dir/qc3.pl -m v -i CV-7261.vcf.top40000.txt -s 2 -a annovarDatabase -o example_vcf_result2

<a name="Results"/>
# Results #
The first section in all reports was the date and command for report generation. So that the user can easily reproduce the report.
 
<a name="ifr"/>
## Introduction for fastq QC result ##

An example report could be accessed at [Here](http://htmlpreview.github.io/?https://github.com/slzhao/QC3/blob/master/reportExample/fastq/fastqReport.html)

fastq QC report was constituted by three sections. 

The "Result Table" section displayed the instrument, run number, flowcell, lane, total reads, base quality score and GC content for each fastq file. 

The "Sequence quality and content" section indicated the per base sequence quality and per base nucleotide content for each fastq file (For pair end results the two files indicating one sample were plotted in the same figure).

The "Batch effect" section indicated the statistics of reads, BQ and GC by different runs/machines/Flowcells/Lanes to demonstrate if there were batch effects in all experiments. Boxplot was used to visualize the distributions of statistics in different groups. Kruskal-Wallis rank sum test and Fligner-Killeen test was used to determine the significance of distribution in different groups.

<a name="ibr"/>
## Introduction for bam QC result ##

An example report could be accessed at [Here](http://htmlpreview.github.io/?https://github.com/slzhao/QC3/blob/master/reportExample/bam/bamReport.html)

bam QC report was constituted by three sections. 

The "Result Table" section displayed the instrument, run number, flowcell, lane, total reads, on/off target reads, intron/intergenic/mito reads,  for each bam file. 

The "Distribution" section visualized the distributions of statistics in all experiments.

The "Batch effect" section indicated the statistics of reads, on-target and off-target by different runs/machines/Flowcells/Lanes to demonstrate if there were batch effects in all experiments. Boxplot was used to visualize the distributions of statistics in different groups. Kruskal-Wallis rank sum test and Fligner-Killeen test was used to determine the significance of distribution in different groups.

<a name="ivr"/>
## Introduction for vcf QC result ##

An example report could be accessed at [Here](http://htmlpreview.github.io/?https://github.com/slzhao/QC3/blob/master/reportExample/vcf/vcfReport.html)

vcf QC report was constituted by six sections. 

The "Filter" section displayed the arguments for the filter of vcf files.

The "Statistics" section displayed the Transitions:Transversions ratio and Heterozygous:Non-reference homozygous ratio in all samples.

The "Consistency" section visualized the consistency in all sample to sample pairs.

The "SNP count" section displayed the SNP count of each sample in different chromesomes.

The "Score" section visualized the position and score of SNPs before and after filter.

The "Annovar annotation" section displayed the counts of SNPs with different functions, such as synonymous/nonsynonymous SNV, and whether they were in snp137 database.

<a name="Contacts"/>
# Contacts #
Shilin Zhao:	shilin.zhao@vanderbilt.edu

Yan Guo:	    yan.guo@vanderbilt.edu