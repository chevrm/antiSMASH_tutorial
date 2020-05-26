# Tiny Earth antiSMASH Tutorial

<center>
	<img src="/images/TinyEarthBannerRGB.png" alt="tinyearth" title="" width="350" height="100" />
	<img src="/images/antismash_logo.svg" alt="antiSMASH" title="" width="100" height="100" />
</center>

Materials herein were prepared for the [2020 Tiny Earth Symposium](https://tinyearth.wisc.edu/2020-symposium/) by [Marc Chevrette](http://chevrm.github.io) (University of Wisconsin-Madison, Wisconsin Institute for Discovery, Tiny Earth Chemistry Hub) and [Kristin Labby](https://www.beloit.edu/live/profiles/248-kristin-labby) (Beloit College, Tiny Earth Partner Instructor).

### Attribution

If you find antiSMASH useful in your research, please cite the appropriate version in any work or publications. This tutorial currently applies to [antiSMASH v5](https://academic.oup.com/nar/article/47/W1/W81/5481154).

## Using antiSMASH to identify biosynthetic gene clusters within a genomic DNA sequence

## Introduction
*From the [antiSMASH 5.0 UserManual](https://docs.antismash.secondarymetabolites.org/PDFmanual/antiSMASH5manual.pdf):*

Many microbial genomes contain several (up to 30-40) gene clusters encoding the biosynthesis of secondary metabolites. Subsequently mining genetic data has become a very important method in modern screening approaches for bioactive compounds like antibiotics.(1) The antibiotics and secondary metabolites analysis shell antiSMASH is a comprehensive pipeline for the automated mining of finished or draft genome data for the presence of secondary metabolite biosynthetic gene clusters (2–8). antiSMASH is an Open Source software written in Python.

## Directions for general use of antiSMASH (Tiny Earth specific instructions to follow)

### Prepare, locate, identify your genomic DNA sequence
- antiSMASH works with genomic DNA sequences in plain FASTA (nucleotide) format, EMBL, or Genbank. Two options exist for using the antiSMASH web interface. 
	1. Use genomic nucleotide accession number from NCBI database. (GenBank accession number works better than RefSeq; Usually starts with “NZ_” or “NC_”; note that some bacterial genomes may be more than one chromosome). This can be confusing for new users, so when in doubt you will want to use the **nucleotide** accession number, and not other accession numbers (e.g. assembly, protein, etc.).
	2. Upload your own genomic sequence as a FASTA file. If obtaining from NCBI genome page, download “Genbank (full)” or “FASTA (text)”. Note that "Genbank (full)" is not the same as "Genbank".

### Job submission
- In a web-browser open the the [antiSMASH homepage (bacterial version)](https://antismash.secondarymetabolites.org/#!/start).
- Enter your email address (you will get an email when results have been processed, ~20-30 mins for typical bacterial genomes).
- Load your sequence in one of two ways:
	1. Enter NCBI's **nucleotide** accession number to directly load sequence from NCBI
	2. Upload your sequence by using the “upload file” button.
- The default settings are recommended in most cases (more on this later).
	-  “Detection strictness: relaxed” is fine; Leave the following “Extra features”, KnownClusterBlast, ActiveSiteFinder, and SubClusterBlast, ON.
	- See section 4.1.2 of User Guide for more info on these extended parameters

<center>
	<img src="/images/as_home.png" alt="antiSMASH submission page" title="antiSMASH sample submission page" width="1000" height="400" />
</center>

### Interpreting results
- Results will be emailed to you if an email address was provided. They will also appear in your browser if you leave the page open while job is running. See below for an annotated example overview antiSMASH results page:

<center>
	<img src="/images/as_example.PNG" alt="example antiSMASH results overview" title="antiSMASH sample submission page" width="1000" height="615" />
</center>

	- 

