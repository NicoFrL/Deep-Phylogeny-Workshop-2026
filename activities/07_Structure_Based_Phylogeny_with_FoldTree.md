# Activity 07 — Structure-Based Phylogeny with FoldTree

## Goal

Build a structure-based phylogeny from the **same homologous domain** used for the sequence-based analysis.

The workflow is:

```text
UniProt accessions
        ↓
ProtDomRetrieverSuite
        ↓
domain coordinates + domain sequences
        ↓
full AlphaFold structures
        ↓
structures trimmed to the selected domain
        ↓
Foldseek all-vs-all structural comparison
        ↓
FoldTree structural distance matrices
        ↓
structure-based trees
        ↓
FigTree inspection
```

Using the same domain for both sequence and structure analyses makes the comparison between the two phylogenetic approaches biologically meaningful.

---

## 1. Locate the trimmed domain structures

ProtDomRetrieverSuite already downloaded the AlphaFold structures and trimmed them to the domain coordinates selected in Activity 03.

Move to your domain-analysis directory:

```bash
BASE=/projects/LaboratoireRDP/Deep_Phylogeny_Workshop_2026

cd "$BASE/participants/$USER/02_domains"
pwd
```

Check that the trimmed structures are present:

```bash
ls -lh trimmed_structures
```

You should see files such as:

```text
P22059_domain1_trimmed.pdb
Q969R2_domain1_trimmed.pdb
...
```

The exact filenames will depend on your dataset.

---

## 2. Prepare the FoldTree working directory

Move to your FoldTree directory:

```bash
cd "$BASE/participants/$USER/06_foldtree"
pwd
```

Create the directory expected by FoldTree:

```bash
mkdir -p structs
```

Copy the trimmed domain structures:

```bash
cp ../02_domains/trimmed_structures/*.pdb structs/
```

FoldTree also expects an `identifiers.txt` file.

Because we are supplying our own precompiled structures, this file must be empty:

```bash
: > identifiers.txt
```

Check the input:

```bash
find structs -maxdepth 1 -name '*.pdb' | wc -l
```

The number should match the number of structures you intend to analyse.

You can also inspect a few filenames:

```bash
ls structs | head
```

---

## 3. Important — check your current directory

Before running FoldTree, verify that you are inside:

```text
.../participants/<your_username>/06_foldtree
```

Run:

```bash
pwd
```

If you are not in the FoldTree directory:

```bash
cd "$BASE/participants/$USER/06_foldtree"
```

Many commands below use filenames relative to the current directory. If you run them from another directory, the files may appear to be "missing" even though they exist.

---

## 4. Run FoldTree

For the workshop, run:

```bash
foldtree     --folder "$BASE/participants/$USER/06_foldtree"     --custom-structs     -c 4     --foldseek-cores 4     --conda-prefix "$BASE/software/foldtree_conda"     -p
```

### Meaning of the options

```text
--folder
```

defines the FoldTree working directory.

```text
--custom-structs
```

tells FoldTree to use the structures already present in:

```text
06_foldtree/structs/
```

rather than downloading structures itself.

```text
-c 4
```

allows Snakemake to run up to 4 parallel jobs.

```text
--foldseek-cores 4
```

uses 4 CPU threads for the Foldseek all-vs-all structural comparison.

```text
--conda-prefix "$BASE/software/foldtree_conda"
```

uses the FoldTree environments that were prepared centrally for the workshop.

```text
-p
```

prints the shell commands executed by the FoldTree/Snakemake workflow.

---

## 5. What FoldTree does

With the custom trimmed structures, FoldTree performs an exhaustive Foldseek all-vs-all comparison.

It then converts the structural comparisons into several distance matrices and builds structure-based trees.

The important intermediate files include:

```text
allvall_1.csv
foldtree_fastmemat.txt
alntmscore_fastmemat.txt
lddt_fastmemat.txt
```

The corresponding tree files include:

```text
foldtree_struct_tree.nwk
alntmscore_struct_tree.nwk
lddt_struct_tree.nwk
```

FoldTree then post-processes and roots the trees.

---

## 6. Inspect the final trees

First confirm that the final files exist:

```bash
cd "$BASE/participants/$USER/06_foldtree"
pwd
```

Then:

```bash
ls -lh     foldtree_struct_tree.PP.nwk.rooted.final     alntmscore_struct_tree.PP.nwk.rooted.final     lddt_struct_tree.PP.nwk.rooted.final
```

The three final structural trees are:

```text
foldtree_struct_tree.PP.nwk.rooted.final
alntmscore_struct_tree.PP.nwk.rooted.final
lddt_struct_tree.PP.nwk.rooted.final
```

For the workshop, we will use:

```text
foldtree_struct_tree.PP.nwk.rooted.final
```

as the main FoldTree structural phylogeny, while keeping the alternative structural trees for comparison.

---

## 7. Open the FoldTree phylogeny in FigTree

Make sure you are still in the FoldTree directory:

```bash
cd "$BASE/participants/$USER/06_foldtree"
pwd
```

Confirm that the file exists:

```bash
ls -lh foldtree_struct_tree.PP.nwk.rooted.final
```

Then open it:

```bash
figtree foldtree_struct_tree.PP.nwk.rooted.final &
```

The `&` keeps the terminal available while FigTree is open.

If FigTree reports:

```text
unable to open file: file not found
```

the most likely explanation is that your terminal is in the wrong directory.

Check:

```bash
pwd
ls
```

and return to:

```bash
cd "$BASE/participants/$USER/06_foldtree"
```

before trying again.

---

## 8. Inspect the structural phylogeny

Look at the FoldTree tree and ask:

- Do close sequence homologues also group together structurally?
- Are there groups that are clearer in the structural tree than in the sequence tree?
- Are very divergent sequences still structurally similar?
- Are there unexpectedly long structural branches?
- Do the FoldTree, alignment-TM-score and lDDT trees recover similar major groups?
- Are disagreements associated with unusual domain boundaries or low-confidence regions?
- Are some structures obvious structural outliers?

A structural tree is not automatically "more correct" than a sequence tree.

Sequence and structure preserve evolutionary information differently, and disagreement between them can itself be informative.

---

## 9. Compare with the IQ-TREE sequence phylogeny

The sequence-based tree from Activity 06 was inferred from the aligned sequence of the selected homologous domain.

The FoldTree analysis uses the AlphaFold structure trimmed to that same domain.

This allows a direct comparison between:

```text
sequence-based phylogeny
        versus
structure-based phylogeny
```

Compare:

- major clades;
- sister relationships;
- long branches;
- unstable sequences;
- deep relationships;
- obvious topological disagreements.

Do not interpret a disagreement immediately as an error. It may reflect different evolutionary signal retained at the sequence and structural levels.

---

## 10. About AlphaFold confidence

ProtDomRetrieverSuite retains the AlphaFold structural information while trimming the model to the selected domain.

FoldTree can optionally filter structures according to average pLDDT using:

```text
--filter
```

For this workshop, we do **not** enable this filter by default.

Instead, inspect suspicious or poorly behaving structures explicitly and consider whether low-confidence regions could influence the structural comparison.

---

## 11. Keep the important outputs

At minimum, retain:

```text
structs/
allvall_1.csv
foldtree_fastmemat.txt
alntmscore_fastmemat.txt
lddt_fastmemat.txt
foldtree_struct_tree.PP.nwk.rooted.final
alntmscore_struct_tree.PP.nwk.rooted.final
lddt_struct_tree.PP.nwk.rooted.final
```

The `structs/` directory is particularly important because it records the exact domain-trimmed structures used for the phylogenetic analysis.

---

## Key point

**Sequence phylogeny and structural phylogeny are two different views of the evolutionary history of the same homologous domain.**

The strength of this workflow comes from keeping the biological unit consistent:

```text
same accessions
        ↓
same domain definition
        ↓
domain sequence
        +
domain structure
        ↓
independent sequence- and structure-based phylogenies
```

The next step is to compare these phylogenies and evaluate which evolutionary relationships are robust across sequence and structural information.
