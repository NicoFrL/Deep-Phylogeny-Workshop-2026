# Activity 04 — Multiple Sequence Alignment

## Goal

Build and inspect a multiple-sequence alignment (MSA) before testing alignment sensitivity.

A phylogenetic tree is only as meaningful as the positional homology represented in the alignment. At this stage, the goal is therefore not simply to generate an alignment file, but to check whether the sequences are aligned in a biologically sensible way.

For the workshop, we will use **MAFFT** as the primary sequence aligner and inspect the result in **Jalview**. In the next activity, we will deliberately perturb MAFFT parameters and include an independent **MUSCLE 5** alignment to test how sensitive the alignment is to methodological choices.

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

## 2. Choose a MAFFT strategy

The appropriate MAFFT strategy depends on the biological unit being aligned.

### G-INS-i

For extracted homologous domains that are expected to be alignable across most of their length, G-INS-i is often a good starting point:

```bash
mafft --globalpair --maxiterate 1000 \
    domain_sequences.fasta \
    > alignment_ginsi.fasta
```

G-INS-i uses global pairwise alignment information together with iterative refinement.

### L-INS-i

If the sequences contain conserved local regions separated by more variable segments, long insertions, or poorly alignable regions, L-INS-i can be more appropriate:

```bash
mafft --localpair --maxiterate 1000 \
    domain_sequences.fasta \
    > alignment_linsi.fasta
```

L-INS-i uses local pairwise alignment information together with iterative refinement.

The choice between G-INS-i and L-INS-i is not a universal rule. It should reflect the sequence architecture and the evolutionary question.

The original FASTA is not modified.

---

## 3. Open the alignment in Jalview

For example:

```bash
jalview alignment_ginsi.fasta &
```

or:

```bash
jalview alignment_linsi.fasta &
```

The `&` keeps the terminal available while Jalview is running.

---

## 4. Inspect the alignment

Do not move directly from an alignment program to tree inference.

Look at the alignment and ask:

- Are strongly conserved residues or motifs aligned consistently?
- Are some sequences much longer or shorter than the others?
- Are there suspicious blocks containing many gaps?
- Are the N- or C-terminal regions poorly aligned?
- Are there long lineage-specific insertions?
- Are some sequences obvious outliers?
- Are domain boundaries coherent?
- Does one divergent sequence force many gaps into all other sequences?

A visually unusual region is not automatically wrong. The question is whether the columns represent plausible positional homology.

If the alignment looks fundamentally inconsistent, return to the sequence-curation or domain-definition steps before continuing.

---

## 5. Check the alignment dimensions

All sequences within one alignment must have the same aligned length.

For example:

```bash
python - <<'PY'
from pathlib import Path

filename = "alignment_ginsi.fasta"

records = {}
name = None

for line in Path(filename).read_text().splitlines():
    if line.startswith(">"):
        name = line[1:]
        records[name] = ""
    else:
        records[name] += line.strip()

for name, seq in records.items():
    print(len(seq), name)
PY
```

Every sequence in the same alignment should have the same aligned length.

This is only a technical sanity check. Equal alignment length does not mean that the alignment is biologically correct.

---

## 6. Do not over-edit the alignment

Manual inspection is essential, but avoid changing residues or gaps simply because an alignment "looks nicer".

Any trimming or exclusion of poorly aligned regions should have a clear rationale and should be reproducible.

The objective is to represent positional homology as accurately as possible, not to maximize visual neatness.

---

## Key point

**Alignment is a hypothesis of positional homology.**

Tree-building methods will treat aligned columns as comparable evolutionary characters. If residues that are not homologous are forced into the same columns, downstream statistical methods cannot repair that mistake.

In the next activity, we will test how stable this hypothesis is by generating multiple MAFFT alignments with perturbed parameters, adding an independent MUSCLE 5 alignment, and comparing them with a common scoring framework.
