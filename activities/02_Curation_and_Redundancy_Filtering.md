# Activity 02 — Sequence Curation and Redundancy Filtering

## Goal

Starting from the sequences retrieved from UniProt:

1. inspect the candidate proteins;
2. remove obvious problematic entries;
3. reduce strong within-species redundancy;
4. retain a traceable curated dataset for downstream analyses.

> **Retrieval is not curation.**

---

## 1. Inspect the UniProt table

Enter your sequence directory:

```bash
cd /projects/LaboratoireRDP/Deep_Phylogeny_Workshop_2026/participants/$USER/01_sequences
```

Open the retrieved TSV:

```bash
libreoffice --calc *.tsv
```

Useful columns include:

- Accession
- Reviewed
- Protein Name
- Gene Names
- Ordered Locus Names
- Organism
- Organism ID
- Length
- Protein Existence
- Proteomes
- RefSeq
- GeneID
- Ensembl
- Ensembl Plants
- Gramene
- TAIR
- InterPro
- Pfam
- GO
- EC
- KEGG
- PDB

---

## 2. Inspect the candidate sequences

Look for:

- unusually short or long sequences;
- obvious fragments;
- incompatible protein annotations;
- unexpected domain architectures;
- large numbers of nearly identical sequences from the same organism;
- possible alternative gene models or isoforms.

Do not automatically remove a sequence simply because it is unreviewed.

For publication-quality datasets, provenance may require additional inspection of genome assemblies and gene models using resources such as NCBI, Ensembl Plants, Gramene, or organism-specific genome databases.

For the workshop, we will perform a faster first-pass curation.

---

## 3. Save your curated TSV

Delete only the rows that you have decided should not be retained.

Save the resulting table as a tab-separated file, for example:

```text
myprotein_curated.tsv
```

Keep the original UniProt FASTA unchanged.

The redundancy-filtering script will use the accessions retained in the TSV to select the corresponding sequences from the FASTA.

---

## 4. Reduce redundancy within each organism

We will use CD-HIT with a 90% sequence-identity threshold.

Importantly, clustering is performed **independently for each Organism ID**.

Therefore, sequences from different organisms are never clustered together, even if they are highly similar or identical.

Run:

```bash
cdhit_by_organism     myprotein_curated.tsv     myprotein.fasta
```

Default parameters are:

```text
sequence identity = 90%
CD-HIT global identity
2 CPU threads
```

CD-HIT defines global identity here as the number of identical residues divided by the full length of the shorter sequence.

This step is a pragmatic redundancy filter. It is **not** an orthology-inference method and does not automatically distinguish recent paralogues.

---

## 5. Examine the results

Four files are produced:

```text
*_cdhit90.fasta
*_cdhit90.tsv
*_cdhit90_summary.tsv
*_cdhit90_clusters.tsv
```

### FASTA

The non-redundant sequences retained for downstream analyses.

### TSV

The corresponding UniProt metadata for the retained representatives.

### Summary

The number of sequences before and after clustering for each organism.

Example:

```text
Arabidopsis thaliana     25 -> 14
Oryza sativa             10 -> 8
```

### Clusters

A detailed record of every CD-HIT cluster, including:

- organism;
- Organism ID;
- representative sequence;
- cluster members;
- identity to the representative.

Inspect this file if an important sequence appears to have disappeared during redundancy filtering.

---

## 6. Questions to consider

- Are some organisms strongly overrepresented?
- Are many nearly identical proteins derived from the same genome?
- Could some clusters represent alternative gene models?
- Could a cluster contain genuine recent paralogues?
- Are sequence lengths now reasonably coherent?
- Does the remaining dataset still represent the taxonomic diversity relevant to your biological question?

Do not aim for a fixed number of sequences.

The goal is a biologically interpretable dataset, not simply the smallest one.

---

## Key point

A 90% CD-HIT threshold is used here as a practical way to reduce strong within-organism redundancy before alignment and phylogenetic analysis.

It should not replace biological inspection of the sequences or genome annotations.
