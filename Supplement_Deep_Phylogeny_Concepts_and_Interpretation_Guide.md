# Supplement — How Deep Phylogeny Actually Works

## Purpose

This guide explains the mathematical and conceptual machinery behind the practical workshop.

It focuses on the questions that are easy to lose behind software commands:

- What is a substitution matrix?
- Why are BLOSUM62, PAM250, LG and WAG not the same kind of "matrix"?
- What is the difference between local and global alignment?
- What does Foldseek actually compare?
- What does maximum likelihood maximize?
- What exactly is resampled in a bootstrap?
- What does UFBoot approximate?
- What does SH-aLRT test?
- What can a support value tell us — and what can it never tell us?

The objective is not to memorize algorithms. It is to know what claim each algorithm permits.

---

# 1. Two very different things are called "substitution matrices"

This is one of the most important distinctions in the workshop.

## 1.1 Alignment scoring matrices: BLOSUM and PAM

When aligning two protein sequences, the algorithm must decide whether aligning residue `i` with residue `j` is plausible.

A scoring matrix assigns a score to each amino-acid pair.

Example:

```text
A aligned with A  → favorable score
W aligned with W  → strongly favorable score
I aligned with L  → often favorable because they are chemically similar
W aligned with D  → generally unfavorable
```

These scores are usually **log-odds scores**.

Conceptually:

```text
score(i,j) ∝ log [
    frequency of i↔j in homologous proteins
    -----------------------------------------
    frequency expected by chance
]
```

A positive score means that the pairing occurs more often among homologous proteins than expected by chance.

A negative score means it is less compatible with the empirical substitution patterns represented by that matrix.

### BLOSUM

BLOSUM matrices were derived from conserved blocks of protein families.

The number roughly reflects the sequence-identity clustering threshold used during construction.

A useful intuition:

```text
BLOSUM80 → relatively close relationships
BLOSUM62 → general-purpose compromise
lower-number BLOSUM matrices → increasingly divergent comparisons
```

### PAM

PAM matrices originate from an evolutionary model estimated from relatively close homologues and extrapolated to larger evolutionary distances.

The numbering behaves in the opposite intuitive direction from BLOSUM:

```text
PAM30  → relatively close
PAM250 → much more divergent
```

### Catch phrase

> **BLOSUM number down = deeper. PAM number up = deeper.**

This is a mnemonic, not a law determining which matrix is optimal for every dataset.

---

# 2. A phylogenetic rate matrix is not an alignment scoring matrix

In maximum-likelihood phylogenetics, matrices such as:

```text
JTT
WAG
LG
DAYHOFF
```

describe a **continuous-time substitution process**.

The central object is the instantaneous rate matrix:

```text
Q
```

For amino acids `i ≠ j`, `qij` describes the instantaneous rate at which amino acid `i` changes to amino acid `j`.

The diagonal entries are defined so that each row sums to zero.

For a branch of length `t`, the instantaneous rates are converted into transition probabilities:

```text
P(t) = exp(Qt)
```

This answers a different question from BLOSUM:

```text
Alignment scoring matrix:
"How plausible is it to align these residues?"

Phylogenetic rate matrix:
"Given evolutionary change along a branch, how probable are substitutions between states?"
```

### Catch phrase

> **A scoring matrix helps propose positional homology. A rate matrix models change after positional homology has been proposed.**

Confusing these two layers is a conceptual error.

---

# 3. Branch length is not chronological time

In standard molecular phylogenetics, a branch length is usually measured approximately as:

```text
expected substitutions per site
```

A branch length of `0.2` therefore does **not** mean `20 million years` unless an explicit molecular-clock calibration has been added.

A long branch can reflect:

- faster sequence evolution;
- more elapsed time;
- both;
- model mismatch;
- alignment problems;
- poor sampling.

### Catch phrase

> **Long branch ≠ old lineage.**

---

# 4. Global and local sequence alignment

## 4.1 Global alignment

A global alignment attempts to align sequences across their full extent.

The classical pairwise algorithm is Needleman-Wunsch.

This is appropriate when the sequences are expected to be homologous across most of their length.

## 4.2 Local alignment

A local alignment searches for the best matching region without requiring the entire sequences to correspond.

The classical pairwise algorithm is Smith-Waterman.

BLAST is fundamentally a local sequence-similarity search.

### Catch phrase

> **Global asks whether the whole object corresponds. Local asks where correspondence is strongest.**

---

# 5. G-INS-i and L-INS-i are MSA strategies

For multiple sequence alignment, the terminology is related but not identical to simply running Needleman-Wunsch or Smith-Waterman.

## G-INS-i

MAFFT G-INS-i uses **global pairwise alignment information** plus iterative refinement.

It is appropriate when the sequences — or extracted domains — are expected to be globally alignable over most of their length.

```bash
mafft --globalpair --maxiterate 1000 input.fasta > alignment_ginsi.fasta
```

## L-INS-i

MAFFT L-INS-i uses **local pairwise alignment information** plus iterative refinement.

It can be useful when conserved regions are separated by:

- long insertions;
- variable loops;
- poorly conserved segments.

```bash
mafft --localpair --maxiterate 1000 input.fasta > alignment_linsi.fasta
```

---

# 6. Gaps are hypotheses too

A gap is not "nothing".

A gap proposes that an insertion or deletion occurred relative to another sequence.

Gap penalties therefore influence the evolutionary hypothesis represented by an alignment.

If gap opening is too cheap, too many gaps may be introduced.

If gap opening is too expensive, non-homologous residues may be forced into the same columns.

This is one reason we perturb alignment parameters rather than treating one alignment as unquestionable truth.

### Catch phrase

> **Every alignment column is a claim about ancestry.**

---

# 7. Why alignment uncertainty matters downstream

A phylogenetic program assumes that residues in the same alignment column are comparable evolutionary characters.

It does **not** normally re-decide positional homology during the tree search.

Therefore:

```text
wrong positional homology
        ↓
wrong character matrix
        ↓
potentially strongly supported wrong tree
```

### Catch phrase

> **Garbage aligned confidently is still garbage.**

---

# 8. MUSCLE and alignment ensembles

Different reasonable alignment procedures can produce different positional-homology hypotheses.

If an important clade exists only under one alignment but disappears under several plausible alternatives, the phylogenetic uncertainty begins **before** the tree-building step.

### Catch phrase

> **Alignment uncertainty is phylogenetic uncertainty.**

---

# 9. What Foldseek actually does

Foldseek is designed for fast protein-structure comparison.

Foldseek introduces the **3Di structural alphabet**.

For each residue, Foldseek identifies a spatially informative neighboring residue and describes their local tertiary relationship.

That local geometry is encoded as one of a finite set of structural states.

A three-dimensional structure can therefore be represented partly as a string:

```text
3D coordinates
      ↓
local tertiary interactions
      ↓
3Di structural-state string
```

This allows fast sequence-search-like machinery to identify promising structural matches. Candidates can then be evaluated with richer structural alignment information.

### Catch phrase

> **Foldseek turns local 3D geometry into a searchable structural language.**

---

# 10. Foldseek similarity is not sequence similarity

Foldseek can find proteins whose amino-acid sequences are highly diverged but whose folds remain related.

The structural comparison can include metrics such as:

- structural alignment length;
- sequence identity within the structure-informed alignment;
- lDDT-related similarity;
- alignment TM-score.

These quantities measure different aspects of pairwise similarity.

No single one should automatically be interpreted as "evolutionary distance".

---

# 11. TM-score, lDDT and RMSD answer different questions

## RMSD

RMSD measures average coordinate deviation after a superposition.

It is sensitive to protein size, which atoms are included, flexible regions, outlier rejection, and alignment choice.

## TM-score

TM-score is normalized to reduce some of the length dependence of RMSD and emphasizes overall fold similarity.

## lDDT

lDDT compares local interatomic distance patterns without requiring a global superposition.

### Catch phrase

> **There is no universal single number called "structural similarity".**

---

# 12. FoldTree is not IQ-TREE with structures

FoldTree and IQ-TREE infer trees using fundamentally different frameworks.

The workshop FoldTree workflow performs:

```text
all-vs-all structural comparisons
        ↓
pairwise structural/structure-informed distances
        ↓
distance matrix
        ↓
distance-based tree
```

IQ-TREE performs:

```text
multiple sequence alignment
        ↓
explicit substitution model
        ↓
likelihood calculation
        ↓
heuristic search over tree topologies
```

The trees are therefore complementary analyses. Their branch lengths and statistical interpretation are not interchangeable.

### Catch phrase

> **Same shape of tree does not mean same statistical model.**

---

# 13. What maximum likelihood means

Suppose we have:

```text
A = alignment
T = tree topology
θ = branch lengths + model parameters
```

Maximum likelihood evaluates:

```text
L(T, θ | A) = P(A | T, θ)
```

In words:

> Given this tree and this evolutionary model, how probable is the alignment we actually observed?

The preferred solution is the tree and parameter set giving the highest likelihood among those explored.

Because probabilities across many sites become extremely small, programs work with log-likelihood.

---

# 14. Likelihood is evaluated site by site

For each alignment column, we do not know the amino acid present at ancestral internal nodes.

The likelihood calculation therefore sums over the possible ancestral states.

The classical pruning algorithm efficiently reuses partial calculations while moving from the tips toward internal nodes.

### Catch phrase

> **Maximum likelihood does not reconstruct one ancestor first and then score the tree; it sums over possible ancestral states while evaluating the tree.**

---

# 15. Why tree search is heuristic

The number of possible unrooted bifurcating trees grows explosively.

For `n` taxa:

```text
number of unrooted binary trees = (2n - 5)!!
```

Therefore IQ-TREE, PhyML and similar programs use heuristic searches rather than evaluating every possible tree.

### Catch phrase

> **The ML tree is the best tree found under the model and search — not a proof that every possible tree was exhaustively rejected.**

---

# 16. ModelFinder chooses among candidates

ModelFinder compares candidate evolutionary models using criteria such as AIC, AICc and BIC.

If BIC selects `LG+F+R5`, this means that among the candidate models evaluated, `LG+F+R5` achieved the preferred BIC trade-off.

It does **not** mean that `LG+F+R5` is the true biochemical mechanism of protein evolution.

### Catch phrase

> **Best-fitting model ≠ true model.**


---

# 17. What is a bootstrap?

Imagine an alignment with 1000 columns:

```text
1 2 3 4 5 ... 1000
```

A standard non-parametric bootstrap replicate samples **1000 columns with replacement**.

One replicate might contain:

```text
4, 81, 81, 302, 9, 742, 4, 991, ...
```

Some original columns appear several times. Some do not appear at all.

For each replicate, a phylogenetic analysis is performed.

Suppose the clade `(A,B)` appears in:

```text
930 of 1000 bootstrap trees
```

Its bootstrap support is:

```text
93%
```

## What does 93% tell us?

It tells us that this split is highly reproducible when alignment columns are resampled under that analysis procedure.

## What does 93% NOT tell us?

It does **not** mean:

```text
"There is a 93% probability that this clade is historically true."
```

Bootstrap support is not a posterior probability.

It also does not protect against a systematic error present in most or all columns.

### Catch phrase

> **Bootstrap measures stability to resampling, not probability of truth.**

---

# 18. Why resample columns?

The observed alignment is treated as a finite sample of evolutionary characters.

If a relationship is driven by only a tiny subset of columns, resampling may omit or down-weight those columns and the clade may disappear.

If the signal is distributed broadly across the alignment, the clade tends to reappear.

Thus bootstrap support asks approximately:

> If we had sampled comparable characters from the same underlying process, how stable would this split be?

This interpretation still assumes that the alignment and evolutionary model are reasonable.

---

# 19. Conventional bootstrap versus ultrafast bootstrap

## Conventional bootstrap

In the classical workflow:

```text
resample sites
        ↓
infer a tree for replicate 1
resample sites
        ↓
infer a tree for replicate 2
...
```

Repeated full phylogenetic searches are computationally expensive.

## Ultrafast bootstrap — UFBoot

UFBoot retains the logic of bootstrap site resampling but uses computational shortcuts and approximations to avoid performing an entirely independent expensive ML search from scratch for every replicate.

UFBoot2 improves this approach and is designed to provide useful branch-support estimates at dramatically lower computational cost.

In IQ-TREE:

```bash
-B 1000
```

means:

```text
1000 ultrafast bootstrap replicates
```

whereas lowercase:

```bash
-b
```

requests the conventional bootstrap workflow.

### Example

A value of:

```text
98 UFBoot
```

means that the split is extremely stable under the UFBoot resampling procedure.

It still does **not** mean:

```text
98% probability that evolution really happened that way
```

### Catch phrase

> **Ultrafast refers to the algorithm, not to lower scientific standards. But UFBoot is still a support statistic, not a truth meter.**

---

# 20. What is SH-aLRT?

SH-aLRT is a local branch-support test.

For a particular internal branch, the method asks whether the topology containing that branch is substantially better supported by the likelihood than nearby alternative arrangements.

Conceptually:

```text
          current split
              |
      compare likelihood
       /             nearby alternative 1  nearby alternative 2
```

It therefore addresses a different question from bootstrap resampling.

Bootstrap asks about **stability under site resampling**.

SH-aLRT asks about **local likelihood support relative to alternative resolutions of that branch**.

### Example

If IQ-TREE reports:

```text
92/98
```

with:

```text
--alrt 1000 -B 1000
```

the label means:

```text
SH-aLRT = 92
UFBoot  = 98
```

This is encouraging because two different support procedures favor the branch.

It does not establish historical certainty.

### Catch phrase

> **SH-aLRT challenges a branch locally; UFBoot challenges the dataset by resampling sites.**

---

# 21. Practical support heuristics

For single-gene analyses, commonly used IQ-TREE guidance treats approximately:

```text
SH-aLRT ≥ 80
UFBoot  ≥ 95
```

as strong support.

These are **heuristics**, not laws of nature.

A branch with:

```text
99/100
```

can still be wrong if:

- the alignment is systematically wrong;
- non-homologous sequences were included;
- paralogues are being mistaken for orthologues;
- the substitution model is seriously misspecified;
- compositional bias drives the topology;
- taxon sampling creates long-branch artifacts.

### Catch phrase

> **High support means "the analysis strongly prefers this branch", not "biology has certified this branch".**

---

# 22. Support and branch length are different quantities

A strongly supported branch can be extremely short.

A long branch can be poorly supported.

Branch length asks:

```text
how much modeled evolutionary change?
```

Support asks:

```text
how stable or preferred is this split under a support procedure?
```

Never treat them as the same quantity.

---

# 23. Rooting is an additional hypothesis

Under standard reversible substitution models, maximum-likelihood inference produces an **unrooted** tree.

The root requires external information or an additional assumption.

Common choices include:

- explicit outgroup rooting;
- midpoint rooting;
- more specialized rooting methods.

Without a justified root, statements such as:

```text
"protein A evolved before protein B"
```

are not supported merely by the drawing.

### Catch phrase

> **Topology tells you who groups with whom. Rooting tells you the direction of the story.**

---

# 24. Rotating a tree does not change the topology

These two drawings can represent the same tree:

```text
      ┌─A
  ┌───┤
  │   └─B
──┤
  │   ┌─C
  └───┤
      └─D
```

and the same clades can be displayed in a different visual order by rotating nodes.

### Catch phrase

> **Left, right, top and bottom are graphics — not evolution.**

---

# 25. Gene tree is not automatically species tree

A protein-family tree can differ from the species tree because of:

- gene duplication;
- gene loss;
- horizontal transfer;
- incomplete lineage sorting;
- hidden paralogy;
- annotation errors.

Thus a protein from species X grouping with one from species Y does not automatically mean that species X and Y are closest relatives.

### Catch phrase

> **Genes have histories inside species histories.**

---

# 26. Orthology cannot be read from sequence identity alone

The most similar sequence in another species is not automatically the orthologue.

Orthology and paralogy are defined by evolutionary events:

```text
speciation  → orthology
duplication → paralogy
```

To distinguish them reliably, the gene tree may need to be reconciled with species relationships, genomic context, duplication/loss patterns and broader taxon sampling.

### Catch phrase

> **Similarity is a measurement. Orthology is an evolutionary relationship.**

---

# 27. Taxon sampling can change a tree

Adding taxa can:

- break long branches;
- reveal hidden paralogues;
- distinguish duplication from speciation;
- improve rooting;
- change which alignment regions are interpretable.

More sequences are not automatically better.

A balanced set that distinguishes competing hypotheses is often more informative than hundreds of redundant sequences.

### Catch phrase

> **Sample to test hypotheses, not to maximize sequence count.**

---

# 28. Long-branch attraction is not only a parsimony problem

Long-branch attraction is classically associated with parsimony, but deep phylogenetic inference can still be biased under likelihood when:

- the model is inadequate;
- composition varies strongly among lineages;
- rates are highly heterogeneous;
- taxon sampling is poor;
- alignment errors accumulate.

Maximum likelihood is powerful, not invulnerable.

---

# 29. Compositional heterogeneity can imitate ancestry

Standard amino-acid models often assume an equilibrium composition shared across the tree.

If unrelated lineages independently become enriched in similar amino acids, a homogeneous model can mistake compositional similarity for shared ancestry.

For difficult deep phylogenies, inspect whether unusual sequences also have unusual amino-acid composition.

---

# 30. Missing data and gaps are not all equivalent

A gap can represent:

- a genuine insertion/deletion;
- an unsequenced region;
- a fragmented gene model;
- a domain boundary;
- an alignment decision.

A phylogenetic program may treat many gap characters as missing information, but the pattern of gaps can still influence which residues are aligned and therefore indirectly influence the tree.

---

# 31. Domain boundaries are part of the model

For multidomain proteins, the full-length protein may contain evolutionary histories from multiple modules.

If the biological question concerns one homologous domain, extracting that domain can prevent unrelated regions from dominating the alignment or structural comparison.

But extraction is not automatically superior.

If the question concerns the evolution of the complete architecture, full-length proteins may be the correct unit.

### Catch phrase

> **Choose the evolutionary unit before choosing the software.**

---

# 32. AlphaFold confidence is not evolutionary support

pLDDT estimates confidence in **local predicted geometry**.

PAE estimates confidence in **relative positioning of regions**.

Neither measures:

- homology;
- phylogenetic support;
- functional relevance;
- probability that a clade is correct.

A high-pLDDT structure can still be used in a biologically inappropriate comparison.

### Catch phrase

> **pLDDT asks "is this geometry predicted confidently?", not "is this evolutionary story correct?"**

---

# 33. Structural conservation can outlast sequence conservation

Protein folds are constrained by packing, stability, catalytic geometry, ligand binding and interaction surfaces.

Sequence can therefore change substantially while the overall fold remains recognizable.

This is why structural comparisons can remain informative for remote homologues.

But structural similarity is not immune to convergence.

### Catch phrase

> **Structure can preserve ancestry longer — but physics can also produce convergence.**

---

# 34. Sequence tree and structural tree can disagree for legitimate reasons

Possible causes include:

- sequence alignment uncertainty;
- structural convergence;
- different evolutionary rates of sequence and structure;
- incorrect domain boundaries;
- low-confidence structural regions;
- paralogy;
- taxon sampling;
- different statistical frameworks.

The correct response is not:

```text
"choose the tree I prefer"
```

The correct response is:

```text
identify which assumptions differ
        ↓
design a test that distinguishes explanations
```

---

# 35. PyMOL superposition is an inspection tool, not a phylogenetic method

PyMOL is excellent for asking:

- Which helices are conserved?
- Which loops diverge?
- Is the core fold preserved?
- Where are insertions located?
- Does a structural outlier have an obvious explanation?

But a pairwise superposition does not reconstruct evolutionary history.

RMSD depends strongly on what is aligned and which atoms remain after outlier rejection.

### Catch phrase

> **Superposition shows geometry. Phylogeny proposes history.**

---

# 36. Visualization can create false confidence

Tree software can make uncertain results look definitive.

Avoid:

- hiding weakly supported branches without explanation;
- presenting an arbitrary root as biological fact;
- using colors that imply categories not supported by data;
- collapsing discordant taxa merely to simplify a figure.

Use visualization to expose assumptions, not to conceal them.

---

# 37. FigTree and iTOL do not infer the tree

FigTree and iTOL visualize and annotate trees.

They do not change the underlying inference unless you explicitly edit or reroot the tree.

iTOL is especially useful for integrating taxonomy, domain architectures, experimental annotations, presence/absence matrices and multiple metadata layers.

### Catch phrase

> **A prettier tree is not a better-supported tree.**

---

# 38. IQ-TREE and PhyML are implementations of the same broad inference principle

Both can perform maximum-likelihood phylogenetic inference.

They differ in search implementations, available models, defaults, integrated model-selection/support tools and computational strategy.

IQ-TREE is used in the workshop because it conveniently integrates ModelFinder, SH-aLRT and UFBoot.

PhyML remains an established alternative ML program.

### Catch phrase

> **Software is the implementation. Maximum likelihood is the inference principle.**

---

# 39. The most dangerous interpretation errors

## Wrong

> "These proteins are 70% homologous."

## Better

> "These proteins share 70% sequence identity over the aligned region and are inferred to be homologous."

## Wrong

> "Bootstrap 98 means there is a 98% probability that this clade is correct."

## Better

> "The clade is recovered very consistently under the bootstrap procedure."

## Wrong

> "This long branch is the oldest protein."

## Better

> "This branch contains a large amount of modeled sequence change."

## Wrong

> "The AlphaFold model has pLDDT 95, so the evolutionary placement is reliable."

## Better

> "The local structure is predicted confidently; phylogenetic reliability must be evaluated independently."

## Wrong

> "FoldTree agrees with IQ-TREE, so the topology is proven."

## Better

> "Two methodologically different sources of evidence recover a congruent relationship."

## Wrong

> "ModelFinder selected LG+F+R5, therefore LG+F+R5 is the true evolutionary process."

## Better

> "LG+F+R5 had the preferred model-selection criterion among the candidate models evaluated."

---

# 40. A scientific interpretation template

For every major conclusion, write four lines.

## Observation

What did the analysis directly show?

```text
A and B form a clade with SH-aLRT 94 and UFBoot 99.
```

## Inference

What evolutionary explanation is consistent with it?

```text
A and B may derive from a lineage-specific duplication.
```

## Alternative

What other process could generate the same pattern?

```text
Hidden paralogy or horizontal transfer followed by differential loss.
```

## Test

What evidence would discriminate between them?

```text
Broader sampling, species-tree reconciliation, genomic context and functional data.
```

### Catch phrase

> **Observation → inference → alternative → test.**

That four-step chain is one of the safest habits in evolutionary biology.

---

# 41. Final rules to remember

> **Homology is not a percentage.**

> **Every alignment column is a hypothesis of positional homology.**

> **A scoring matrix is not a phylogenetic rate matrix.**

> **Best-fitting model does not mean true model.**

> **Branch length is not time unless a clock has been calibrated.**

> **Bootstrap is stability under resampling, not probability of truth.**

> **SH-aLRT and UFBoot support the same branch in different ways.**

> **High support cannot rescue systematic bias.**

> **Rooting is an evolutionary assumption, not a cosmetic operation.**

> **Gene tree is not automatically species tree.**

> **pLDDT is structural confidence, not phylogenetic support.**

> **Structural similarity can reflect ancestry or convergence.**

> **Superposition shows geometry; phylogeny proposes history.**

> **Sequence and structure are complementary evidence.**

> **If methods disagree, investigate assumptions before choosing a winner.**

> **A reproducible uncertainty is more scientific than an unexplained certainty.**
