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

The redundancy-filtering script will use the accessions retained in your curated TSV, retrieve the corresponding sequences from the original FASTA, and perform CD-HIT independently within each organism.

**The original TSV and FASTA are never modified.**

---

## 4. Reduce redundancy within each organism

We will use CD-HIT as a conservative redundancy filter.

Importantly, clustering is performed **independently for each Organism ID**.

Therefore, sequences from different organisms are never clustered together, even if they are highly similar or identical.

Run:

```bash
cdhit_by_organism     myprotein_curated.tsv     myprotein.fasta
```

By default, the workshop script uses:

```text
sequence identity = 95%
CD-HIT global identity
2 CPU threads
```

CD-HIT defines global identity here as the number of identical residues divided by the full length of the shorter sequence.

### Changing the identity threshold

The 95% threshold is a **heuristic default**, not a universal biological rule.

You can change it with the `-c` (or `--identity`) option.

For example, to use a more conservative 98% threshold:

```bash
cdhit_by_organism     myprotein_curated.tsv     myprotein.fasta     -c 0.98
```

To use 90%:

```bash
cdhit_by_organism     myprotein_curated.tsv     myprotein.fasta     -c 0.90
```

Higher thresholds retain more closely related sequences. Lower thresholds remove more redundancy but increase the risk of clustering genuine recent paralogues.

This step is a pragmatic redundancy filter. It is **not** an orthology-inference method and does not automatically distinguish recent paralogues.

---

## 5. Examine the results

With the default 95% threshold, four files are produced:

```text
*_cdhit95.fasta
*_cdhit95.tsv
*_cdhit95_summary.tsv
*_cdhit95_clusters.tsv
```

If you use another threshold, the filenames change accordingly. For example, `-c 0.98` produces files containing `_cdhit98`.

### FASTA

The non-redundant sequences retained for downstream analyses.

### TSV

The corresponding UniProt metadata for the retained representatives.

### Summary

The number of sequences before and after clustering for each organism.

Example:

```text
Arabidopsis thaliana     25 -> 16
Oryza sativa             10 -> 9
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
- Would a stricter identity threshold be more appropriate for this particular protein family?

Do not aim for a fixed number of sequences.

The goal is a biologically interpretable dataset, not simply the smallest one.

---

## Key point

For this workshop, **95% sequence identity within each organism** is used as a conservative default for reducing strong redundancy before alignment and phylogenetic analysis.

The threshold is adjustable and should be treated as a practical filtering parameter, not as a definition of orthology, paralogy, or biological equivalence.
