# Activity 06 — Maximum-Likelihood Phylogeny with IQ-TREE

## Goal

Infer a maximum-likelihood protein phylogeny from the selected multiple-sequence alignment using **IQ-TREE 3**.

In this activity, IQ-TREE will:

- select an amino-acid substitution model with **ModelFinder**;
- infer a maximum-likelihood tree;
- calculate **SH-aLRT** branch support;
- calculate **ultrafast bootstrap (UFBoot)** branch support;
- write a supported tree that can be inspected in **FigTree**.

The objective is not only to obtain a tree, but also to understand which model was selected, how branch support was calculated, and which output files should be retained.

---

## 1. Move to the phylogeny directory

```bash
cd /projects/LaboratoireRDP/Deep_Phylogeny_Workshop_2026/participants/$USER/04_phylogeny
```

The input for IQ-TREE should be the alignment selected after Activity 05.

For example, if `alignment_7.fasta` was selected:

```bash
cp ../03_alignment/mafft_results_domain_sequences/alignment_7.fasta \
    selected_alignment.fasta
```

Replace `alignment_7.fasta` with the candidate that you actually selected.

Using the stable name:

```text
selected_alignment.fasta
```

makes the downstream analysis easier to reproduce.

---

## 2. Run IQ-TREE

Run:

```bash
iqtree3 \
    -s selected_alignment.fasta \
    -m MFP \
    -B 1000 \
    --alrt 1000 \
    -T 4 \
    -pre iqtree_run
```

### Meaning of the options

```text
-s selected_alignment.fasta
```

specifies the multiple-sequence alignment.

```text
-m MFP
```

runs **ModelFinder Plus**, which compares candidate substitution models and selects the best-fitting model.

```text
-B 1000
```

runs **1000 ultrafast bootstrap replicates (UFBoot)**.

**Important:** uppercase `-B` is ultrafast bootstrap. It is not the same as lowercase `-b`, which requests the conventional non-parametric bootstrap.

```text
--alrt 1000
```

runs **1000 SH-aLRT replicates**.

```text
-T 4
```

uses 4 CPU threads.

```text
-pre iqtree_run
```

sets a common prefix for the output files.

---

## 3. About CPU threads

For the workshop, we use:

```bash
-T 4
```

as a practical default.

IQ-TREE can also determine the number of threads automatically with:

```bash
-T AUTO
```

However, `AUTO` first benchmarks several thread counts on the current dataset. For small or moderate alignments, this benchmarking step can itself take a substantial fraction of the total run time.

For larger or more computationally demanding alignments, `-T AUTO` may be useful because the optimal thread count depends on the alignment size, sequence length, and computational complexity.

For the workshop datasets, start with `-T 4`.

---

## 4. Watch the run

IQ-TREE will first examine the alignment and evaluate substitution models.

ModelFinder can be one of the longest parts of the analysis, especially for protein alignments.

During the run, IQ-TREE will report information such as:

```text
Best-fit model: ...
BEST SCORE FOUND : ...
```

and later the branch-support calculations.

Do not interrupt the program while it is writing its final output files.

---

## 5. Inspect the important output files

After the run:

```bash
ls -lh iqtree_run.*
```

The most important files are:

```text
iqtree_run.iqtree
iqtree_run.log
iqtree_run.treefile
iqtree_run.contree
```

### `iqtree_run.iqtree`

This is the main human-readable report.

It contains, among other information:

- alignment statistics;
- ModelFinder results;
- the best-fit substitution model;
- likelihood information;
- branch-support information;
- run-time statistics.

For a quick summary:

```bash
grep -E \
"Best-fit model|BEST SCORE FOUND|Total wall-clock|Total CPU time" \
iqtree_run.iqtree iqtree_run.log
```

### `iqtree_run.log`

This is the detailed execution log.

It is useful for troubleshooting and for reconstructing exactly what IQ-TREE did.

### `iqtree_run.treefile`

This is the maximum-likelihood tree with branch lengths and support values.

Display it in the terminal with:

```bash
cat iqtree_run.treefile
```

### `iqtree_run.contree`

This is the consensus tree produced from the ultrafast-bootstrap analysis.

Keep it together with the other IQ-TREE outputs.

---

## 6. Understand the branch-support labels

With:

```bash
-B 1000 --alrt 1000
```

IQ-TREE writes branch support in the order:

```text
SH-aLRT / UFBoot
```

For example:

```text
92.4/98
```

means:

```text
SH-aLRT = 92.4%
UFBoot  = 98%
```

The two values are different support statistics and should not be confused.

Support values should be interpreted together with the biological question, taxon sampling, alignment quality, and model assumptions. A high support value does not by itself prove that a phylogenetic hypothesis is biologically correct.

---

## 7. Open the tree in FigTree

Launch FigTree:

```bash
figtree iqtree_run.treefile &
```

The `&` keeps the terminal available.

When FigTree asks how to interpret or name the node-label field, use:

```text
aLRT/UFBoot
```

This is important because the IQ-TREE node labels contain two support values in the order:

```text
SH-aLRT / UFBoot
```

Naming the field explicitly prevents the two statistics from becoming ambiguous later when the figure is edited or exported.

---

## 8. Inspect the tree

When looking at the tree, ask:

- Are expected homologous groups recovered?
- Are there unusually long branches?
- Are some sequences placed in surprising positions?
- Do suspicious sequences correspond to unusual alignments or domain architectures?
- Are important internal branches supported by both SH-aLRT and UFBoot?
- Are poorly supported nodes associated with ambiguous alignment regions?
- Could taxon sampling explain unstable relationships?

Do not interpret branch support independently of the alignment.

If a biologically important relationship depends on a region that was unstable during Activity 05, return to the alignment and inspect that region again.

---

## 9. Model choice

ModelFinder reports the best-fit substitution model in the `.iqtree` and `.log` files.

For example:

```text
Best-fit model according to BIC: ...
```

Record the selected model when documenting or reporting the phylogenetic analysis.

Do not assume in advance that a particular empirical amino-acid matrix will be optimal for every dataset.

---

## 10. Keep the analysis reproducible

Retain at least:

```text
selected_alignment.fasta
iqtree_run.iqtree
iqtree_run.log
iqtree_run.treefile
iqtree_run.contree
```

The alignment and IQ-TREE report together provide the essential information needed to reconstruct the sequence-based phylogenetic analysis.

---

## Key point

**A phylogenetic tree is the result of a chain of explicit hypotheses: sequence selection, positional homology, substitution model, tree search, and branch-support estimation.**

IQ-TREE provides statistical support for a tree inferred from the alignment. It cannot correct an incorrect sequence set or an incorrect alignment.

After validating the sequence-based phylogeny, the next part of the workshop will compare it with phylogenetic information derived from protein structure.
