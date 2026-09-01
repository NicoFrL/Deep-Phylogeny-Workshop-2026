# Activity 03 — Domain Definition and Extraction

## Goal

After sequence curation and redundancy filtering, decide which homologous region should be used for the phylogenetic analysis and extract that region consistently across the dataset.

For some proteins, the **whole protein** is the appropriate unit of comparison.

For multidomain proteins, or when only one region is evolutionarily homologous across the dataset, a **specific domain** may be more appropriate.

The goal is not simply to trim sequences. The goal is to define a biologically meaningful homologous unit before alignment.

---

## 1. Start from the filtered accession list

After Activity 02, you should have a file such as:

```text
myprotein_curated_cdhit95_accessions.txt
```

This file contains one retained UniProt accession per line and can be used directly by ProtDomRetrieverSuite.

Move to your domain-analysis directory:

```bash
cd /projects/LaboratoireRDP/Deep_Phylogeny_Workshop_2026/participants/$USER/02_domains
```

You do not need to manually extract accessions from the TSV.

---

## 2. Revisit the domain architecture

Before extracting anything, inspect your protein or representative proteins in **InterPro**.

Ask:

- Is the protein composed of one domain or several?
- Is the region of interest annotated as a domain, family, repeat, or homologous superfamily?
- Is the same region present across the taxonomic diversity of your dataset?
- Are additional domains present only in some lineages?
- Does your biological question concern the evolution of the entire protein or of one particular domain?

The InterPro entry used for retrieval in Activity 01 does not automatically have to be the region used for phylogeny.

Choose the entry that best represents the homologous unit you want to compare.

---

## 3. Launch ProtDomRetrieverSuite

ProtDomRetrieverSuite is a graphical application, so launch it from the X2Go session:

```bash
protdomretrieversuite
```

In the graphical interface, provide:

### Input file

Select the accession list produced by Activity 02, for example:

```text
myprotein_curated_cdhit95_accessions.txt
```

### Output directory

Use your workshop domain directory:

```text
/projects/LaboratoireRDP/Deep_Phylogeny_Workshop_2026/participants/$USER/02_domains
```

### Domain/signature entry

Enter the InterPro or other supported signature entry corresponding to the region you want to extract.

For example:

```text
IPR000971
```

The appropriate entry will depend on your own protein family.

---

## 4. Retrieve the domain sequences

For the sequence-phylogeny workflow, enable FASTA retrieval.

For this stage of the workshop, structural downloads are not required unless specifically needed for your project.

Run the analysis.

A successful ProtDomRetrieverSuite run can produce files including:

```text
domain_analysis.tsv
domain_ranges.txt
domain_sequences.fasta
interpro_results.json
```

---

## 5. Inspect the output

### `domain_analysis.tsv`

Contains the accession, selected InterPro/signature entry, and the detected domain coordinates.

Example:

```text
Protein Accession    InterPro Entry          Start 1    End 1
P69905               IPR000971 (d1:[2,142]) 2          142
```

### `domain_ranges.txt`

Provides a compact representation of the extracted ranges.

Example:

```text
P69905[2-142]
```

### `domain_sequences.fasta`

Contains the extracted domain sequences that will be used for downstream alignment.

Example header:

```text
>P69905[2-142] IPR000971
```

### `interpro_results.json`

Stores the retrieved InterPro information used by the analysis.

---

## 6. Quality-control the extracted domains

Before moving to alignment, inspect the results.

Check:

- Are domain lengths reasonably consistent?
- Are some domains unexpectedly short or long?
- Are some proteins missing the selected domain?
- Are there proteins containing more than one copy of the domain?
- Are the detected boundaries biologically plausible?
- Are there lineage-specific insertions or domain fusions that should be retained?
- Does the extracted region still correspond to the evolutionary question you want to ask?

Do not assume that an automated domain annotation is automatically the correct phylogenetic unit.

---

## 7. Whole-protein versus domain phylogeny

Domain extraction is not mandatory.

Use the **whole protein** when the full-length proteins are homologous and their overall architecture is biologically meaningful for the question.

Use an **extracted domain** when:

- proteins have different additional domains;
- the homologous region represents only part of the protein;
- domain fusion or acquisition would otherwise dominate the alignment;
- the evolutionary history of the domain itself is the question.

In some projects, comparing both whole-protein and domain-only trees can be informative.

---

## Key point

**Define the homologous unit before aligning the sequences.**

A good alignment cannot rescue a dataset in which non-homologous regions have been mixed together.

The output `domain_sequences.fasta` will be used in the next activity for multiple-sequence alignment.
