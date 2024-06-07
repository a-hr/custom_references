# How to create custom reference files

> Álvaro Herrero Reiriz, 2024

Having your own reference files comes in handy when working with custom oligos or plasmids. Here is a step-by-step guide on how to create your own reference files.

- [How to create custom reference files](#how-to-create-custom-reference-files)
  - [Custom FASTA](#custom-fasta)
  - [FASTA index (`.fai`)](#fasta-index-fai)
  - [Custom GTF](#custom-gtf)
  - [Custom BED](#custom-bed)
  - [Custom IGV BAM lists](#custom-igv-bam-lists)

## Custom FASTA

These are used to be mapped against (e.g. with `minimap2`). They contain the whole sequence of the oligo or plasmid to be mapped against. Each entry usually contains a whole contig (e.g. chromosome, plasmid, etc.).

The structure of a FASTA file is as follows:

```fasta
>Contig_name
ACGTACGTACGT # Sequence of the contig
```

## FASTA index (`.fai`)
The `.fai` file is a tab-separated file that contains the index of the contigs in the FASTA file. It is created with the `samtools faidx` command.

For example, to create the index of a FASTA file named `human_genome.fa`:

```bash
samtools faidx human_genome.fa
```

## Custom GTF

These are used as reference for plotting, splicing analysis... They contain the coordinates of the features of the oligo or plasmid to be visualized. Each entry usually contains a feature (e.g. gene, transcript, exon, etc.).

It is good practice to have each feature followed by its sub-features. For example, a gene should be followed by its transcripts, and each transcript should be followed by its exons.

The structure of a GTF file is as follows:

```gtf
Contig_name	Source	Feature	Start	End	Score	Strand	Frame	Attributes
Contig_name	custom	gene	898	3459	.	+	.	gene_id "g1";
Contig_name	custom	exon	898	1109	.	+	.	gene_id "g1";     transcript_id "tr1";     exon_id "ex1";    
Contig_name	custom	exon	2154	2208	.	+	.	gene_id "g1";     transcript_id "tr1";     exon_id "ex2";    
Contig_name	custom	exon	2653	3459	.	+	.	gene_id "g1";     transcript_id "tr1";     exon_id "ex3";
```
> Note: it is a tab-separated file.

- `Contig_name`: Name of the contig where the feature is located. Must match the name of the contig in the FASTA file.
- `Source`: Source of the feature (e.g. `custom`).
- `Feature`: Type of feature (e.g. `gene`, `transcript`, `exon`, etc.).
- `Start`: Start position of the feature.
- `End`: End position of the feature.
- `Score`: Score of the feature. (not needed)
- `Strand`: Strand of the feature (`+` or `-`).
- `Frame`: Frame of the feature. (not needed).
- `Attributes`: Attributes of the feature. Must contain at least `gene_id` and `transcript_id` for genes and transcripts, respectively. For exons, it must contain `gene_id`, `transcript_id`, and `exon_id`.

## Custom BED

These are used as reference for plotting, splicing analysis... They contain the coordinates of the features of the oligo or plasmid to be visualized. Each entry usually contains a feature (e.g. gene, transcript, exon, etc.).

The most straightforward way to create a BED file is to convert a GTF file to a BED file. This can be done with the `gtf2bed` script from the `bedops` package.

To install `bedops`:

```bash
conda install -c bioconda bedops
```

To convert a GTF file to a BED file:

```bash
gtf2bed < file.gtf > file.bed
```
## Custom IGV BAM lists

When dealing with big projects (hundreds of samples), it is useful to have a list of the BAM files to be visualized in IGV. This list will allow us to dynamically load the files without burning the computer's memory.

To create a list of `.bam` files:

1. Move the target `.bam` files to a single directory. (including the index `.bai` files)
2. Create a list filw for the group:
   ```bash
   cd /path/to/bam/files
   realpath *.bam > GROUP_NAME.bam.list
   ```