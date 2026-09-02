# Activity 1 — Define the Protein Family and Retrieve Sequences

## Goal

Start from a protein of interest, identify the most appropriate InterPro entry for the evolutionary question, and retrieve homologous proteins from the workshop taxon set.

---

## 1. Start from your protein of interest

Open the UniProt entry for your protein:

- [UniProt](https://www.uniprot.org/)

Note the **UniProt accession** of your protein.

---

## 2. Inspect the protein in InterPro

Open:

- [InterPro](https://www.ebi.ac.uk/interpro/)

Search for your protein and inspect its annotations.

A protein may be associated with several different levels of classification, for example:

```text
Homologous superfamily
        ↓
Protein family
        ↓
Domain
        ↓
More specific functional signatures
```

### What should you choose?

There is no automatic best answer.

We will decide together which InterPro entry is the most appropriate for your evolutionary question.

Consider:

- Does the entry correspond to the whole protein or only one domain?
- Is the protein multidomain?
- Is the selected entry too broad or too specific?
- How widely is the entry distributed across taxa?
- Would a broader entry help detect distant homologues?
- Would a narrower entry avoid mixing unrelated proteins?

Use the **taxonomic distribution** shown in InterPro to help evaluate the choice.

Record the selected InterPro accession:

```text
IPRxxxxxx
```

---

## 3. Retrieve matching proteins from UniProt

We will use a predefined set of workshop organisms.

There are two ways to perform the search.

### Option A — UniProt website

For a small number of taxa, you can build the query directly in UniProt.

Example:

```text
(xref:interpro-IPR037239) AND
(
organism_id:9606 OR
organism_id:3702 OR
organism_id:39947
)
```

This searches for proteins associated with the selected InterPro entry in the specified organisms.

Use the UniProt results table to inspect:

- accession;
- protein name;
- organism;
- sequence length;
- number of proteins returned per organism;
- InterPro/Pfam annotations.

> A taxon may return zero, one, or several proteins. Multiple hits may reflect paralogues, gene-family expansions, fragments, or annotation differences.

---

### Option B — Workshop retrieval tool

For the complete workshop taxon list, use the provided command-line tool.

First activate the workshop environment (if you have not already done so, or if you are working in a new terminal window):

```bash
source /projects/LaboratoireRDP/Deep_Phylogeny_Workshop_2026/activate_workshop.sh
```

Move to your sequence directory:

```bash
cd /projects/LaboratoireRDP/Deep_Phylogeny_Workshop_2026/participants/$USER/01_sequences
```

Run:

```bash
retrieve_uniprot
```

Enter the InterPro accession when prompted, for example:

```text
IPR037239
```

Then press **Enter twice**.

The script automatically uses the workshop taxon list and queries the exact organism IDs.

---

## 4. Inspect the output files

The retrieval tool creates a `.tsv` table and a `.fasta` file in the current directory.

List the files:

```bash
ls
```

Open the table in LibreOffice Calc:

```bash
libreoffice --calc *.tsv
```

Inspect the results before proceeding.

Pay particular attention to:

- organisms with no hits;
- organisms with many hits;
- unusually short or long proteins;
- inconsistent protein names;
- possible paralogues;
- differences in domain annotation.

> Retrieval is not curation. The fact that a sequence matches the query does not automatically mean that it should be included in the final phylogenetic dataset.

---

## 5. Optional: export FASTA directly

The retrieval tool can also generate FASTA output:

```bash
retrieve_uniprot -a IPRxxxxxx -o fasta
```

However, for the workshop we will normally inspect the TSV results **before** deciding which sequences should be retained for downstream analysis.

---

## Output of this activity

At the end of this step, you should have:

- a selected InterPro accession;
- a table of candidate proteins from the workshop taxa;
- an initial understanding of the number and diversity of homologues recovered;
- a first list of sequences that may require curation.
