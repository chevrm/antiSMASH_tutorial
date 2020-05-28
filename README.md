# Tiny Earth antiSMASH Tutorial

<center>
	<img src="/images/TinyEarthBannerRGB.png" alt="tinyearth" title="" width="350" height="100" />
	<img src="/images/antismash_logo.svg" alt="antiSMASH" title="" width="100" height="100" />
</center>

Materials herein were prepared for the [2020 Tiny Earth Symposium](https://tinyearth.wisc.edu/2020-symposium/) by [Marc Chevrette](http://chevrm.github.io) (University of Wisconsin-Madison, Wisconsin Institute for Discovery, Tiny Earth Chemistry Hub) and [Kristin Labby](https://www.beloit.edu/live/profiles/248-kristin-labby) (Beloit College, Tiny Earth Partner Instructor).

### Attribution

If you find antiSMASH useful in your research, please cite the appropriate version in any work or publications. This tutorial currently applies to [antiSMASH v5](https://academic.oup.com/nar/article/47/W1/W81/5481154).

# Introduction
*From the [antiSMASH 5.0 User Manual](https://docs.antismash.secondarymetabolites.org/PDFmanual/antiSMASH5manual.pdf) (accessed 2020-01-08):*

>Many microbial genomes contain several (up to 30-40) gene clusters encoding the biosynthesis of secondary metabolites. Subsequently mining genetic data has become a very important method in modern screening approaches for bioactive compounds like antibiotics. The antibiotics and secondary metabolites analysis shell antiSMASH is a comprehensive pipeline for the automated mining of finished or draft genome data for the presence of secondary metabolite biosynthetic gene clusters. antiSMASH is an Open Source software written in Python.

## What is a genome, anyway?

The word 'genome' gets thrown around a lot nowadays with ever-decreasing costs of DNA sequencing and ever-increasing piles of sequencing data. Here, we’ll use the word genome to refer to a DNA sequencing assembly. 

### A brief overview of DNA sequencing
To generate an assembly of a bacteria’s genome, researches (including those at the Tiny Earth Chemistry Hub) have to lyse open the cells and physically or chemically isolate the DNA.  This DNA is then prepped for sequencing (different technologies have different preparation protocols) before being read through a sequencer, one molecule at a time. It’s important to remember that each of these molecules is not the entire genome, but a small piece of it. These pieces are called *reads*. In Illumina (Solexa) sequencing, reads are between 150 and a few hundred base pairs long. In “long read” technologies like PacBio or Oxford Nanopore, the reads can be ~100,000 base pairs long. Regardless of the read length, currently we don’t have the technology to sequence an entire genome from beginning to end, so we end up with these reads/pieces that we need to put back together. Since in a given sequencing run will sequence millions of these reads randomly broken at various loci in the genome, a lot of reads will overlap and assembly algorithms can use this information to put your genome back together into *contigs*, or large contiguous stretches of DNA sequence. Keep in mind that this is not a perfect process, so sometimes there are more than one contig after assembly. This could be due to biology (e.g. plasmids will be assembled to their own contigs) or because of technical reasons (e.g. poor quality DNA prep, insufficient sequencing depth, etc.).

<center>
	<img src="/images/sequencing_overview.PNG" alt="antiSMASH submission page" title="Shotgun DNA sequencing overview" width="600" height="400" />
</center>

### Fasta format
Many different formatting conventions exist for files containing sequencing data. For this tutorial, we will focus on one: *fasta* format. It is quite simple and is the standard for storing assemblies. Nucleotide assemblies are typically stored as fasta files with the file extension \*.fasta or \*.fna. They have two components:
1. *The header*
	- A fasta header is what you (or NCBI or the assembly program) name your sequence. These lines start with a **>** and will immediately be followed by the sequence name (no spaces). Optionally, after the name descriptions are added after a space. These descriptions are just for humans and are typically ignored by analysis software.
2. *The sequence itself*
	- The sequence itself begins on the line just after a header. This can be multiple lines, all on one line, whatever you like. Anything after a given header will be treated as one contiguous sequence, until the next header is seen.

~~~
>example_sequence_name Example description. This is my favorite sequence
AAAGGGCCCTTTAAAGGGCCCTTTAAAGGGCCCTTTAAAGGGCCCTTT
AAAGGGCCCTTTAAAGGGCCCTTTAAAGGGCCCTTTAAAGGGCCCTTT
AAAGGGCCCTTTAAAGGGCCCTTTAAAGGGCCCTTTAAAGGGCCCTTT
>another_sequence_name This is not my favorite sequence
GCGCATATGCGCATATGCGCATATGCGCATATGCGCATATGCGCATATGCGCATAT
GCGCATATGCGCATATGCGCATATGCGCATATGCGCATATGCGCATATGCGCATAT
GCGCATATGCGCATATGCGCATATGCGCATATGCGCATATGCGCATATGCGCATAT
~~~

In the example above, the T at the end of line 2 is directly connected to the first A of line 3. The newlines (enters) are ignored. Notice that where genes begin and end are not captured here. No annotations, just a long string of sequence. This is where antiSMASH comes in...it will predict genes from your fasta, annotate their proposed functions, and identify which genes are arranged in biosynthetic gene clusters.

# Using antiSMASH to identify biosynthetic gene clusters within a genomic DNA sequence

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

# Using antiSMASH in a Tiny Earth course

## Procedure
Use antiSMASH to identify biosynthetic gene clusters within genomic DNA sequences. You should perform at least two antiSMASH analyses of genomes from:
1. One of our TE isolates.
	1. If available, use the complete genomic nucleotide sequence of one of our isolates, or use a genomic TE sequence file shared from the ChemHub.
	2. If you had successful PCR and sequencing of the 16S rRNA gene of one of your isolates, you can use the genomic, nucleotide sequence of the top BLAST match.
2. An interesting bacterial secondary metabolite.
Use the list of natural products below to use antiSMASH to identify the biosynthetic gene cluster responsible for making that bacterial secondary metabolite.
	1. Table 1, below, is a list of interesting natural products. Your job will be to do some research to find what bacterial organism(s) make that natural product. Then search NCBI genome database to find the genomic, nucleotide accession number for that species. (You may also add your own bacterial natural product of interest to Table 1!)
	2. Use this genome to run antiSMASH. Please add the species name and accession number to Table 1.
 
| Bacterial secondary metabolite | Student name | Natural product class | Organism | NCBI accession |
| :----------------------------: | :----------: | :-------------------: | :------: | :------------: |
| erythromycin    | | | | |
| zwittermicin A  | | | | |
| oxytetracycline | | | | |
| cyphomycin | | | | |
| keyicin | | | | |
| ...etc | | | | |
| Students can add their own | | | | |

**_Table 1:_** *List of interesting bacterial secondary metabolites for antiSMASH analysis.*


| Bacterial secondary metabolite | Natural product class | Biosynthetic gene cluster type | Organism | NCBI accession |
| :----: | :----: | :----: | :----: | :----: |
| erythromycin | macrolide (aka cyclic polyketide) | type 1 polyketide | *Saccharopolyspora erythraea* NRRL 2338 | [NC_009142.1](https://www.ncbi.nlm.nih.gov/nuccore/NC_009142.1/) |
| zwittermicin A  | aminopolyol | hybrid type 1 polyketide, *trans*-AT polyketide, nonribosomal peptide | *Bacillus cereus* UW85 | [NZ_LYVD00000000](https://www.ncbi.nlm.nih.gov/nuccore/NZ_LYVD00000000) |
| oxytetracycline | polyketide (tetracycline type) | type 1 polyketide | *Streptomyces rimosus* WT5260 | [NZ_CP025551.1](https://www.ncbi.nlm.nih.gov/nuccore/NZ_CP025551.1) |
| cyphomycin | polyketide (polyene type) | type 1 polyketide | *Streptomyces* sp. ISID311 | [NZ_VOQD00000000.1](https://www.ncbi.nlm.nih.gov/nuccore/NZ_VOQD00000000.1) |
| keyicin | anthracycline | type 2 polyketide | *Micromonospora* sp. WMMB235 | [NZ_MDRX01000002.1](https://www.ncbi.nlm.nih.gov/nuccore/NZ_MDRX01000002.1) |


**_Table 2:_** *Example instructor key. Not sure which clusters/metabolites/organisms to add? Check out [MIBiG](https://mibig.secondarymetabolites.org/).*

## Results and Discussion

### Example classroom activity
1. Analyze antiSMASH results of one our TE isolates.
	1. Analyze your results on the overview page and click on each region to find more information. How many total regions were identified within your genomic sequence? Are there any regions that have identifiable biosynthetic gene clusters? (“most similar known cluster”) Be sure to pay attention to similarity – what does this mean?
	2. Share your results: Add this information to our class spreadsheet of TE isolates. To the column titled “possible secondary metabolites produced”, list secondary metabolite clusters with 70% or higher similarity.
	3. Share your results: Make a slide(s) in our Google Slides for this project describing the isolate, BLAST results, and antiSMASH results. Include a screen capture of your antiSMASH results. Add structures of the possible secondary metabolites identified. Do a little bit of literature research – how much have these metabolites been studied? Are they reported to have antibacterial activity? Are there any procedures for extraction and isolation?
2. Analyze antiSMASH results of a known bacterial secondary metabolite.
	1. Did antiSMASH identify a region corresponding to the biosynthetic gene cluster that particular secondary metabolite? What is the percent similarity for this region, and what does that indicate? How many total regions were identified within your genomic sequence? Are there any other regions that have identifiable biosynthetic gene clusters? (“most similar known cluster”) What molecules do these biosynthetic gene clusters produce?
	2. Share your results: Make a slide(s) in our Google Slides for this project describing your antiSMASH results. Include a screen capture of your antiSMASH results, structure of the bacterial secondary metabolite and answers to the questions above. Do some literature research. Has this biosynthetic gene cluster been characterized? If so, include a figure and reference.

### Example topic for classroom discussion

1. Did you notice any secondary metabolites that appear in several bacterial species? What is the function of these molecules? (geosmin, hopene, etc.)?

# Resources and links

- **[antiSMASH](https://antismash.secondarymetabolites.org/#!/start)**: **anti**biotics and **S**econdary **M**etabolite **A**nalysis **S**hell, a bacterial biosynthetic gene cluster prediction and characterization tool
- **[antiSMASH documentation](https://docs.antismash.secondarymetabolites.org/)**: a helpful user guide to antiSMASH
- **[fungiSMASH](https://fungismash.secondarymetabolites.org/#!/start)**: similar to antiSMASH, but specifically built for fungal genomes
- **[plantiSMASH](http://plantismash.secondarymetabolites.org/)**: similar to antiSMASH, but specifically built for plant genomes
- **[MIBiG](https://mibig.secondarymetabolites.org/)**: **M**inimum **I**nformation about a **Bi**osynthetic **G**ene cluster repository, a large, curated repository of biosynthetic gene clusters with annotations and links to relevant publications and/or genomic data
- **[ARTS](http://arts.ziemertlab.com/)**: **A**ntibiotic **R**esistant **T**arget **S**eeker, a tool to help identify resistance genes within BGCs.
