# Deep Phylogeny Workshop — Quick Start

This page contains the few commands you need to start the workshop.

---

## 1. Open a terminal

On the ENS/CBP workstation, open a terminal and activate the workshop environment:

```bash
source /projects/LaboratoireRDP/Deep_Phylogeny_Workshop_2026/activate_workshop.sh
```

Run this command **each time you open a new terminal**.

After activation, you should see the workshop environment and software versions displayed in the terminal.

---

## 2. Find your personal workshop directory

Run:

```bash
/projects/LaboratoireRDP/Deep_Phylogeny_Workshop_2026/scripts/init_participant.sh
```

The script will display the path to your personal workshop directory, for example:

```text
/projects/LaboratoireRDP/Deep_Phylogeny_Workshop_2026/participants/your_username
```

Then enter your directory with the `cd` command shown by the script.

Your workspace contains:

```text
00_start/
01_sequences/
02_domains/
03_alignment/
04_phylogeny/
05_structures/
06_foldtree/
07_final/
```

Use these folders as we progress through the workshop.

---

## 3. Main software

Once the workshop environment is activated, the main tools are available directly.

| Tool | Command | Purpose |
|---|---|---|
| MAFFT | `mafft` | Multiple sequence alignment |
| MUSCLE 5 | `muscle` | Alternative multiple sequence alignment |
| IQ-TREE | `iqtree` | Maximum-likelihood phylogeny |
| Foldseek | `foldseek` | Protein structure comparison |
| FoldTree | `foldtree` | Structural phylogeny |
| ProtDomRetrieverSuite | `protdomretrieversuite` | Sequence/domain retrieval |
| Jalview | `jalview` | Inspect sequence alignments |
| PyMOL | `pymol` | Visualize protein structures |
| FigTree | `figtree` | Visualize phylogenetic trees |
| LibreOffice Calc | `libreoffice --calc` | Open CSV/TSV/XLSX tables |

---

## 4. Shared workshop files

Shared software, scripts, resources and prepared checkpoints are located in:

```text
/projects/LaboratoireRDP/Deep_Phylogeny_Workshop_2026
```

Work only inside your personal directory under:

```text
/projects/LaboratoireRDP/Deep_Phylogeny_Workshop_2026/participants/
```

Do **not** modify files inside the shared `software/`, `scripts/` or `resources/` directories unless instructed.

---

## 5. If a command is not found

The most common reason is that the workshop environment was not activated in the current terminal.

Run again:

```bash
source /projects/LaboratoireRDP/Deep_Phylogeny_Workshop_2026/activate_workshop.sh
```

Then retry the command.

If something still does not work, ask during the workshop rather than reinstalling or modifying the shared software.

---

**Workshop organizer:** Nicolas-Frédéric Lipp
