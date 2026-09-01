# Activity 08 — Structural Inspection, Hypothesis Testing, and Workshop Conclusions

## Goal

Use the sequence-based and structure-based phylogenies to return to the **biological question**.

In this final activity, you will:

- compare the IQ-TREE and FoldTree phylogenies;
- select structurally close and structurally distant protein pairs;
- inspect those structures directly in PyMOL;
- decide which evolutionary hypotheses are supported, weakened, or still unresolved;
- distinguish observations from interpretations;
- identify what additional analysis or experiment would be needed next.

The objective is **not** to finish with one definitive tree. The objective is to understand which parts of your evolutionary interpretation are robust to different sources of evidence.

---

## 1. Define the files you will compare

Set the workshop base directory:

```bash
BASE=/projects/LaboratoireRDP/Deep_Phylogeny_Workshop_2026
```

Your sequence-based phylogeny is in:

```text
$BASE/participants/$USER/04_phylogeny/
```

Your structure-based phylogeny and trimmed domain structures are in:

```text
$BASE/participants/$USER/06_foldtree/
```

Before opening anything, verify where you are:

```bash
pwd
```

---

## 2. Re-open the sequence phylogeny

Move to the IQ-TREE directory:

```bash
cd "$BASE/participants/$USER/04_phylogeny"
pwd
```

Confirm that the tree exists:

```bash
ls -lh iqtree_run.treefile
```

Open it:

```bash
figtree iqtree_run.treefile &
```

Remember that IQ-TREE branch labels generated with:

```text
--alrt 1000 -B 1000
```

are written as:

```text
SH-aLRT / UFBoot
```

In FigTree, label this field:

```text
aLRT/UFBoot
```

Do not interpret either value as the probability that the branch is "true".

---

## 3. Re-open the structural phylogeny

Move to the FoldTree directory:

```bash
cd "$BASE/participants/$USER/06_foldtree"
pwd
```

Confirm that the main tree exists:

```bash
ls -lh foldtree_struct_tree.PP.nwk.rooted.final
```

Open it:

```bash
figtree foldtree_struct_tree.PP.nwk.rooted.final &
```

You can also compare the alternative structural trees:

```text
alntmscore_struct_tree.PP.nwk.rooted.final
lddt_struct_tree.PP.nwk.rooted.final
```

These trees are produced from different structural distance representations. Agreement among them is informative, but disagreement is also useful information.

---

## 4. Compare the sequence and structural trees

Do not begin by asking which tree is "correct".

Instead, identify:

- clades recovered in both trees;
- relationships supported strongly by sequence but not by structure;
- relationships that appear stable structurally but weak or unstable in the sequence analysis;
- taxa that move substantially between trees;
- unusually long branches;
- possible effects of paralogy, taxon sampling, domain boundaries, or alignment uncertainty.

A useful worksheet is:

| Observation | Interpretation consistent with the observation | Alternative explanation | What would test it? |
|---|---|---|---|
| Same major clade in both trees | Sequence and structural signals are congruent | Shared bias or sampling effect | Add taxa; inspect alignment; inspect structures |
| Sequence support weak, structural grouping stable | Sequence erosion or alignment ambiguity may limit the sequence tree | Structural convergence | Inspect MSA and structures; broaden sampling |
| Sequence support strong, structural topology different | Sequence signal may be more informative for this split | Structural convergence, domain effect, model difference | Recheck boundaries, pLDDT, alternative structural trees |
| One taxon moves dramatically | Long branch, fragment, paralogue, annotation problem | Genuine unusual evolutionary history | Inspect sequence, structure, architecture, taxonomy |
| Both analyses unstable | Insufficient or conflicting signal | Dataset design problem | Revisit sampling, homology and domain definition |

---

## 5. Select two structurally close proteins

In the main FoldTree tree, identify a pair of proteins that are close relatives in the structural phylogeny.

Write down their filenames:

```text
Protein A: _______________________________
Protein B: _______________________________
```

Check that the corresponding trimmed structures exist:

```bash
ls structs/
```

Do **not** assume that "close in the tree" means "nearly identical structures". The tree summarizes pairwise structural distances across the dataset.

---

## 6. Inspect the close pair in PyMOL

Graphical programs should be launched from your **X2Go session**.

Move to the FoldTree directory:

```bash
cd "$BASE/participants/$USER/06_foldtree"
pwd
```

Start PyMOL:

```bash
pymol &
```

In the PyMOL command line, replace the filenames below with your selected structures:

```text
load structs/PROTEIN_A_domain1_trimmed.pdb, close_A
load structs/PROTEIN_B_domain1_trimmed.pdb, close_B

hide everything
show cartoon, close_A or close_B

super close_B, close_A
orient
```

Ask:

- Is the overall fold conserved?
- Are secondary-structure elements positioned similarly?
- Where are the largest deviations?
- Are differences concentrated in loops, insertions, termini or the structural core?
- Do the two proteins differ strongly in sequence while remaining structurally similar?
- Are poorly matching regions associated with low-confidence AlphaFold segments?

`super` is a structural-superposition tool. Its reported RMSD is useful for inspecting the chosen pair, but **RMSD is not itself a phylogenetic distance and should not be used as a substitute for the FoldTree analysis**.

For highly divergent structures, PyMOL also provides structure-oriented alternatives such as `cealign`.

---

## 7. Select two structurally distant proteins

Return to the FoldTree tree and choose a pair that is widely separated.

Record:

```text
Protein C: _______________________________
Protein D: _______________________________
```

Load and superpose them in PyMOL:

```text
load structs/PROTEIN_C_domain1_trimmed.pdb, distant_C
load structs/PROTEIN_D_domain1_trimmed.pdb, distant_D

hide everything
show cartoon, distant_C or distant_D

super distant_D, distant_C
orient
```

Compare this pair with the close pair.

Ask:

- Is the same fold still recognizable?
- Which elements remain geometrically conserved?
- Which regions have diverged most?
- Does structural divergence correspond to sequence divergence?
- Are the differences consistent with insertions/deletions seen in the MSA?
- Could differences reflect uncertain AlphaFold regions rather than evolutionary change?

The interesting result is often not that two distant proteins are "different", but **which parts of the fold remain conserved despite deep divergence**.

---

## 8. Return to your original evolutionary hypothesis

State your original question in one sentence:

```text
Hypothesis/question:
____________________________________________________________________
```

Then distinguish four levels.

### Observation

What is directly present in the analysis?

Example:

> Proteins A and B form a strongly supported clade in the sequence tree and are also close in the FoldTree structural tree.

### Inference

What evolutionary scenario is consistent with that observation?

Example:

> The congruent sequence and structural signals are consistent with these proteins sharing a relatively recent common evolutionary history within the sampled family.

### Alternative

What other explanation could still produce the observation?

Example:

> Uneven taxon sampling, hidden paralogy, alignment bias, or structural convergence could alter this interpretation.

### Test

What additional evidence would discriminate between alternatives?

Example:

> Broader taxon sampling, species-tree reconciliation, synteny, domain architecture, biochemical data, or additional structural evidence.

Write your own:

```text
Observation:
____________________________________________________________________

Inference:
____________________________________________________________________

Alternative:
____________________________________________________________________

Next test:
____________________________________________________________________
```

---

## 9. What this workflow allows us to say

This workflow can provide evidence about:

- whether a sampled set of proteins is consistent with a shared evolutionary history;
- which sequence relationships are robust to alignment choices;
- which clades are strongly supported under a specified sequence-evolution model;
- whether deep sequence relationships are congruent with structural similarity;
- which taxa or regions behave as evolutionary outliers;
- whether a proposed evolutionary scenario is consistent with multiple independent observations.

The strongest conclusions are those that remain coherent across:

```text
sampling
+ domain definition
+ sequence alignment
+ alignment sensitivity
+ sequence phylogeny
+ structural phylogeny
+ direct structural inspection
+ biological context
```

---

## 10. What this workflow does NOT allow us to say automatically

A tree alone does **not** prove:

- that two proteins are orthologues rather than paralogues;
- that a duplication occurred at a specific historical time;
- that horizontal gene transfer occurred;
- that a particular protein is ancestral;
- that the longest branch is the oldest lineage;
- that a branch with high support is necessarily correct;
- that structural similarity proves common ancestry;
- that a high-pLDDT AlphaFold model represents the biologically active conformation;
- that the best-fitting substitution model is the biologically true model;
- that one sequence or structural tree should automatically override the other.

These require additional biological evidence and explicit assumptions.

---

## 11. Rooting changes the evolutionary story

A reversible maximum-likelihood sequence model generally infers an **unrooted** tree.

The root must be justified separately, for example using:

- a biologically defensible outgroup;
- midpoint rooting;
- another explicit rooting method.

Do not infer "ancestor → descendant" direction simply from the left-to-right orientation of a displayed tree.

**Rotating branches around a node changes the drawing, not the topology.**

---

## 12. iTOL as an alternative visualization environment

**Interactive Tree Of Life (iTOL)** is a useful alternative to FigTree for displaying and annotating phylogenetic trees.

It is particularly convenient for:

- coloring taxa by lineage;
- adding metadata rings or strips;
- displaying domain architectures;
- showing presence/absence data;
- highlighting experimentally characterized proteins;
- preparing publication-quality annotated trees.

FigTree is convenient for rapid local inspection. iTOL is often more powerful when many metadata layers must be combined.

iTOL is a web-based service. Before uploading unpublished or sensitive datasets, follow the data-handling rules of your institution or project.

Tree visualization does not change the underlying phylogenetic inference.

---

## 13. PhyML as an alternative to IQ-TREE

IQ-TREE is the maximum-likelihood program used in this workshop because it integrates:

- model selection through ModelFinder;
- efficient maximum-likelihood tree search;
- SH-aLRT;
- ultrafast bootstrap.

It is **not the only valid maximum-likelihood phylogenetic program**.

**PhyML** is another established program for maximum-likelihood phylogenetic inference. It can infer trees under standard nucleotide and amino-acid substitution models and provides its own tree-search and branch-support options.

Choosing IQ-TREE rather than PhyML is therefore a workflow choice, not a statement that maximum likelihood is specific to IQ-TREE.

Different software can use different search strategies and defaults. If an important biological conclusion depends on one implementation, reproducing the analysis with another well-established ML program can be informative.

---

## 14. Final interpretation checklist

Before presenting a phylogenetic conclusion, ask:

- Are all sequences genuinely homologous over the region analysed?
- Did we analyse the biologically relevant domain or full-length protein?
- Is taxon sampling balanced enough to test the hypothesis?
- Could paralogy, gene loss or horizontal transfer explain the topology?
- Is the alignment stable to reasonable alternative methods?
- Are important branches supported by both SH-aLRT and UFBoot?
- Is the tree rooted for a defensible biological reason?
- Are structural relationships congruent with the sequence tree?
- Are AlphaFold confidence and domain boundaries acceptable?
- Could structural convergence explain a structural grouping?
- Are conclusions robust across reasonable analytical choices?
- What evidence would falsify the preferred scenario?

---

## Workshop conclusion

The final product of a deep-phylogeny analysis is **not a tree**.

It is an evolutionary hypothesis whose assumptions are visible and whose weak points can be tested.

### Remember

> **A tree is evidence organized into a hypothesis — not a historical photograph.**

> **Support measures robustness under a procedure; it does not certify truth.**

> **Sequence and structure are complementary witnesses, not competing authorities.**

> **When two analyses disagree, investigate the assumptions before choosing a winner.**

> **The best next question is often more valuable than the prettiest final tree.**
