# Activity 04 — Multiple Sequence Alignment

## Goal

Build and inspect a multiple-sequence alignment (MSA) before phylogenetic inference.

A phylogenetic tree is only as meaningful as the homology represented in the alignment. At this stage, the goal is therefore not simply to generate an alignment file, but to check whether the sequences are aligned in a biologically sensible way.

For the workshop, we will compare **MAFFT L-INS-i** and **MUSCLE 5**, then inspect the result in **Jalview**.

---

## 1. Choose the input sequences

Your input will usually be one of the following:

- `domain_sequences.fasta` from ProtDomRetrieverSuite, if you decided to analyse a specific homologous domain;
- the filtered full-length FASTA from Activity 02, if whole-protein phylogeny is more appropriate.

Move to your alignment directory:

```bash
cd /projects/LaboratoireRDP/Deep_Phylogeny_Workshop_2026/participants/$USER/03_alignment
```

If you are using the domain sequences produced in Activity 03, copy them into the alignment directory:

```bash
cp ../02_domains/domain_sequences.fasta .
```

---

## 2. Build a MAFFT L-INS-i alignment

For the relatively small datasets used in this workshop, we will use the L-INS-i strategy as the main MAFFT alignment.

Run:

```bash
mafft --localpair --maxiterate 1000 \
    domain_sequences.fasta \
    > alignment_mafft_linsi.fasta
```

This command:

- uses local pairwise alignment information;
- performs iterative refinement;
- writes the aligned sequences to `alignment_mafft_linsi.fasta`.

The original FASTA is not modified.

---

## 3. Build a MUSCLE 5 alignment

We will also generate an independent alignment with MUSCLE 5:

```bash
muscle \
    -align domain_sequences.fasta \
    -output alignment_muscle5.fasta
```

The sequence order in the output may differ from the input or from MAFFT. This is not a problem.

What matters is the alignment itself.

---

## 4. Open the alignments in Jalview

Open the MAFFT alignment:

```bash
jalview alignment_mafft_linsi.fasta &
```

You can also inspect the MUSCLE alignment:

```bash
jalview alignment_muscle5.fasta &
```

The `&` keeps the terminal available while Jalview is running.

---

## 5. Inspect the alignment

Do not move directly from an alignment program to tree inference.

Look at the alignment and ask:

- Are strongly conserved residues or motifs aligned consistently?
- Are some sequences much longer or shorter than the others?
- Are there suspicious blocks containing many gaps?
- Are the N- or C-terminal regions poorly aligned?
- Are there long lineage-specific insertions?
- Are some sequences obvious outliers?
- Do MAFFT and MUSCLE agree on the major conserved regions?
- Do they disagree in regions that might influence the phylogeny?

A disagreement between aligners is not automatically an error. It often indicates a region in which homology is difficult to establish confidently.

---

## 6. Check the alignment dimensions

All sequences within an alignment must have the same aligned length.

For example:

```bash
python - <<'PY'
from pathlib import Path

for filename in ["alignment_mafft_linsi.fasta", "alignment_muscle5.fasta"]:
    records = {}
    name = None

    for line in Path(filename).read_text().splitlines():
        if line.startswith(">"):
            name = line[1:]
            records[name] = ""
        else:
            records[name] += line.strip()

    print(f"\n{filename}")
    for name, seq in records.items():
        print(len(seq), name)
PY
```

Within each alignment, every sequence should have the same aligned length.

This is primarily a technical sanity check. Equal alignment length does not mean that the alignment is biologically correct.

---

## 7. MAFFT versus MUSCLE

For the workshop, MAFFT L-INS-i will be our primary sequence alignment.

MUSCLE 5 provides an independent comparison.

If the two alignments are very similar across the conserved core of the protein or domain, that is reassuring.

If they differ substantially, inspect the problematic regions before proceeding.

Possible reasons include:

- weak sequence conservation;
- long insertions or deletions;
- incorrect domain boundaries;
- unusual or fragmented proteins;
- non-homologous regions remaining in the dataset.

If necessary, return to the previous curation or domain-extraction steps.

---

## 8. Do not over-edit the alignment

Manual inspection is essential, but avoid changing residues or gaps simply because an alignment "looks nicer".

Any trimming or exclusion of poorly aligned regions should have a clear rationale and should be reproducible.

The objective is to represent positional homology as accurately as possible, not to maximize visual neatness.

---

## Key point

**Alignment is a hypothesis of positional homology.**

Tree-building methods will treat aligned columns as comparable evolutionary characters. If residues that are not homologous are forced into the same columns, downstream statistical methods cannot repair that mistake.

Once you are satisfied with the alignment, the MAFFT alignment:

```text
alignment_mafft_linsi.fasta
```

will be used for the next stage of the workshop: alignment evaluation and phylogenetic inference.
