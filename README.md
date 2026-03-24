# EGFR Inhibitor Binding Analysis & T790M Resistance Mutation Study

> Structural dissection of acquired drug resistance — how a single amino acid substitution (T790M) structurally undermines Gefitinib binding in EGFR kinase

> **Portfolio Note:** This is Part 1 of a two-project portfolio.
> Part 2 → [Progesterone De Novo Design](https://github.com/jhkwin00/Progesterone-de-novo-design)
> — Having analyzed *how small molecules bind existing proteins*, the next project asks the complementary question: *can we design a new protein from scratch to bind a target small molecule?*

---

## Analysis Workflow

```
[Project 1] Molecular Docking
Step 1 - Target Structure Analysis
         Download EGFR + Gefitinib complex from PDB (4WKQ)
         Visualize active site and analyze hydrogen bonds using PyMOL
         |
Step 2 - Docking Preparation
         Convert protein/ligand .pdb to .pdbqt using MGLTools
         Calculate active site center coordinates (Search Box setup)
         |
Step 3 - Molecular Docking
         Perform Gefitinib docking using AutoDock Vina
         Calculate binding energy (ΔG) and analyze binding poses
         |
Step 4 - Resistance Mutation Analysis
         Introduce T790M mutation using PyMOL Mutagenesis Wizard
         Re-dock under identical conditions with mutant protein
         Compare binding energies: wild-type vs T790M mutant

[Project 2] ColabFold Structure Prediction
Step 1 - Sequence Preparation
         Extract T790M mutant sequence from UniProt P00533
         Apply T790M substitution (residue 790: Thr → Met)
         |
Step 2 - Structure Prediction
         Submit sequence to ColabFold (AlphaFold2)
         Select rank_001 model as best prediction
         |
Step 3 - Structural Validation
         Align ColabFold prediction with X-ray structure in PyMOL
         Calculate RMSD and analyze confidence scores (pLDDT)
         |
Step 4 - Comparative Analysis
         Compare T790M residue and A-loop conformation
         Visualize structural differences and save figures
```

---

## Project Overview

### Why EGFR?

EGFR (Epidermal Growth Factor Receptor) is a receptor tyrosine kinase that controls cell proliferation and survival.
In many non-small cell lung cancers (NSCLC), EGFR is constitutively activated by somatic mutations (L858R, exon 19 deletions),
making it one of the most clinically validated oncology targets.

Gefitinib, a first-generation EGFR inhibitor, competitively occupies the ATP-binding pocket of EGFR kinase,
blocking downstream RAS/MAPK and PI3K/AKT signaling that drives tumor growth.
However, roughly 50–60% of patients who initially respond to Gefitinib acquire the T790M resistance mutation within 1–2 years,
rendering the drug ineffective. Understanding the structural basis of this resistance is
a prerequisite for rational design of next-generation inhibitors — which is exactly what this project sets out to explore.

### Biological Context of EGFR

| Property | Detail |
| --- | --- |
| Full name | Epidermal Growth Factor Receptor |
| Gene | EGFR (ErbB1/HER1), chromosome 7p11.2 |
| UniProt | P00533 |
| Protein class | Receptor tyrosine kinase (RTK), ErbB family |
| Domain architecture | Extracellular ligand-binding domain / TM helix / Intracellular kinase domain |
| Kinase domain residues | 694–1188 (UniProt numbering) |
| Activation mechanism | EGF binding → receptor dimerization → trans-autophosphorylation (Y1068, Y1086, Y1173) |
| Key oncogenic mutations | L858R (exon 21), exon 19 del, T790M (resistance) |
| Downstream pathways | RAS–RAF–MEK–ERK (proliferation), PI3K–AKT–mTOR (survival) |
| Clinical relevance | Overexpressed/mutated in NSCLC, colorectal, head & neck, pancreatic cancers |
| Approved inhibitors | Gefitinib (1st gen), Erlotinib (1st gen), Osimertinib (3rd gen, T790M-selective) |

#### T790M Gatekeeper Mutation

Residue 790 (Thr) sits at the entrance to the hydrophobic pocket of the ATP-binding site — a position called the **gatekeeper**.
The hydroxyl group of Thr790 forms a direct hydrogen bond with Gefitinib.
When mutated to Met (T790M):
1. The bulkier methionine side chain sterically occludes the binding space
2. The hydrogen bond donor (–OH) is lost
3. Gefitinib binding energy decreases by ~1.4 kcal/mol (~16%)

This structural insight directly motivated the design of Osimertinib, a covalent 3rd-generation inhibitor
that accommodates the T790M gatekeeper through a different binding geometry.

### Connection to AI-Based Protein Design Research

Understanding atomic-level interactions at a protein–ligand interface —
binding pocket geometry, hydrogen bond networks, gatekeeper residue effects —
is a prerequisite for the design problem that tools like **LigandMPNN**
(Dauparas et al., *Nature Methods* 2025) solve: given a target small molecule,
design a protein sequence whose backbone accommodates that molecule's exact atomic context.

---

## Project 1 — Molecular Docking Analysis

### Why This Project Was Conducted

The EGFR docking project was undertaken for two main purposes.
First, to build hands-on proficiency with the standard computational structure-based drug discovery pipeline
(PyMOL → MGLTools → AutoDock Vina) on a well-characterised, clinically important target.
Second, to structurally rationalise why T790M confers resistance to first-generation inhibitors —
not merely as a biochemical fact, but as a geometric and energetic argument that can be visualised and quantified.
The T790M mutation is an ideal case study because the resistance mechanism is structurally clean and the experimental
X-ray data (PDB: 4WKQ) are of high resolution, allowing ground-truth validation of the computational results.

### Key Results

| Condition | Binding Energy (ΔG) | Interpretation |
| --- | --- |  --- |
| Wild-type EGFR + Gefitinib | -8.755 kcal/mol | Strong binding |
| T790M mutant EGFR + Gefitinib | -7.320 kcal/mol | Reduced binding |
| Difference | 1.435 kcal/mol (↓16%) | Structural basis of resistance |

> The T790M mutation reduces Gefitinib binding affinity by approximately 16%, consistent with clinically observed drug resistance.
> <!-- The ~16% figure is derived from our AutoDock Vina docking results (ΔΔG = 1.435 kcal/mol).
>      The structural and biochemical basis for T790M resistance is documented in:
>      Yun et al. (2008) PNAS 105(6):2070-2075 — https://doi.org/10.1073/pnas.0709662105
>      Note: Yun et al. show resistance is primarily mediated by increased ATP affinity (not steric occlusion alone).
>      Our docking result captures the geometric/steric component of this mechanism. -->

### Key Findings

1. **Gefitinib – EGFR Hydrogen Bond Analysis**
   - Hydrogen bond formation confirmed with key active site residues (Met769, Thr766)
   - Bond distances: 2.8 ~ 3.2 Å (within normal hydrogen bond range)

2. **Docking Validation**
   - Comparison of predicted pose vs. crystallographic position
   - Core scaffold (quinazoline ring) position confirmed to match

3. **T790M Resistance Mechanism**
   - Residue 790: Threonine (small, –OH) → Methionine (bulky, no –OH)
   - Increased gatekeeper residue size reduces Gefitinib binding space
   - Loss of hydrogen bond donor eliminates a key stabilising interaction
   - 1.435 kcal/mol decrease in binding energy → structural basis of resistance

### Visualization

#### Docking Result

![EGFR Docking Result](https://github.com/jhkwin00/EGFR-docking-analysis/raw/master/docking/EGFR_docking_result.png)

```
White  → EGFR protein backbone
Cyan   → Vina predicted docking pose
Yellow → Crystallographic Gefitinib position (4WKQ)
Orange → Active site residues
```

#### T790M Mutation Comparison

![T790M Comparison](https://github.com/jhkwin00/EGFR-docking-analysis/raw/master/docking/EGFR_T790M_comparison.png)

```
Cyan   → Wild-type EGFR docking pose
Red    → T790M mutant docking pose
Green  → Wild-type T790 residue (Threonine)
Orange → Active site residues
Blue   → Mutant M790 residue (Methionine)
```

---

## Project 2 — ColabFold T790M Structure Prediction

### Why This Project Was Conducted

Having established the structural basis of T790M resistance through docking, the next question was:
**how accurately can AI-based structure prediction recapitulate a mutant protein structure that has never been seen before?**
AlphaFold2 was trained on experimental structures, but T790M is a single-point mutation —
the model must generalise from wild-type structure to predict the mutant.
Validating this against the experimental X-ray structure (4WKQ) serves two purposes:

1. **Technical validation** — confirms the ColabFold pipeline produces reliable structures for mutant proteins,
   which is a prerequisite for downstream design tasks (ProteinMPNN, RFdiffusion AA).
2. **Portfolio narrative** — demonstrates command of the full structure prediction workflow:
   sequence → structure prediction → structural validation → design-ready input.

This project directly mirrors the workflow used in protein design labs,
where LigandMPNN and RFdiffusion All-Atom are applied to experimentally validated structures.

### Prediction Details

| Item | Value |
| --- | --- |
| Input sequence | T790M mutant (UniProt P00533, residues 694–1188) |
| Sequence length | 495 residues |
| Best model | rank_001 (model_3, seed_000) |
| Mean pLDDT | 69.09 |
| pTM score | 0.670 |

### pLDDT Confidence Distribution

| Confidence | Color | Residues | Percentage |
| --- | --- | --- | --- |
| Very high (≥90) | Blue | 209 | 42.2% |
| High (70–90) | Cyan | 80 | 16.2% |
| Medium (50–70) | Yellow | 40 | 8.1% |
| Low (<50) | Orange | 166 | 33.5% |

### Structural Validation

| Metric | Value | Interpretation |
| --- | --- | --- |
| RMSD vs X-ray (4WKQ) | 0.682 Å | Near-identical prediction accuracy |
| Atoms aligned | 1704 / 1704 | Full backbone alignment |

> RMSD of 0.682 Å indicates the ColabFold prediction closely matches the experimental X-ray structure.
> The A-loop region (residues 855–875 / ColabFold 162–182) was visually highlighted and confirmed to show no gross deviation — however, no quantitative per-region RMSD analysis was performed. Detailed A-loop analysis remains a future direction.

### Residue Numbering

ColabFold numbering starts from 1; the X-ray structure (4WKQ) starts from residue 694.

| Region | X-ray (4WKQ) | ColabFold | Offset |
| --- | --- | --- | --- |
| Chain start | 694 | 1 | 693 |
| T790M residue | 790 (MET) | 97 (MET) | 693 |
| A-loop | 855–875 | 162–182 | 693 |

### Visualization

#### Full Structure Comparison

![Full Structure Comparison](https://github.com/jhkwin00/EGFR-docking-analysis/raw/master/colabfold_result/T790M_comparison_full.png)

#### T790M Residue Close-up

![T790M Mutation](https://github.com/jhkwin00/EGFR-docking-analysis/raw/master/colabfold_result/T790M_comparison_mutation.png)

#### A-loop Close-up

![A-loop Comparison](https://github.com/jhkwin00/EGFR-docking-analysis/raw/master/colabfold_result/T790M_comparison_aloop.png)

```
Cyan    → T790M X-ray structure (4WKQ)
Orange  → T790M ColabFold prediction
Magenta → T790M residue X-ray (residue 790)
Lime    → T790M residue ColabFold (residue 97)
Red     → A-loop X-ray (residues 855–875)
Yellow  → A-loop ColabFold (residues 162–182)
```

---

## Tools & Environment

| Tool | Version | Purpose |
| --- | --- | --- |
| PyMOL (Open-Source) | 3.1.0 | Protein structure visualization, mutagenesis |
| AutoDock Vina | 1.2.7 | Molecular docking |
| MGLTools | 1.5.7 | .pdb to .pdbqt conversion |
| ColabFold | AlphaFold2 | Protein structure prediction |
| Python | 3.13.1 | Coordinate calculation, pLDDT analysis |
| Anaconda | 26.1.1 | Environment management |
| OS | Windows | Anaconda PowerShell |

---

## Project Structure

```
EGFR_project/
├── receptor/
│   ├── 4WKQ_protein.pdb            # Wild-type EGFR protein structure
│   ├── 4WKQ_protein.pdbqt          # Converted file for docking
│   ├── 4WKQ_T790M.pdb              # T790M mutant protein structure
│   └── 4WKQ_T790M.pdbqt            # Converted file for docking
├── ligand/
│   ├── gefitinib.pdb               # Gefitinib structure
│   └── gefitinib.pdbqt             # Converted file for docking
├── docking/
│   ├── config.txt                  # Wild-type docking config
│   ├── config_T790M.txt            # T790M docking config
│   ├── gefitinib_docked.pdbqt      # Wild-type docking results
│   ├── gefitinib_T790M_docked.pdbqt # T790M docking results
│   ├── EGFR_docking_result.png     # Docking result visualization
│   └── EGFR_T790M_comparison.png   # Mutation comparison visualization
├── colabfold_result/
│   ├── EGFR_T790M_predicted/       # ColabFold raw output
│   ├── T790M_colabfold_comparison.pse # PyMOL session
│   ├── T790M_comparison_full.png   # Full structure comparison
│   ├── T790M_comparison_mutation.png # T790M residue close-up
│   └── T790M_comparison_aloop.png  # A-loop close-up
└── T790M_colabfold.pdb             # T790M ColabFold prediction (rank_001)
```

---

## Conclusion

1. Gefitinib binds strongly to the EGFR active site via hydrogen bonds with Met769 and Thr766, competitively blocking ATP binding.
2. The T790M gatekeeper mutation introduces steric bulk and eliminates a key hydrogen bond donor (–OH), reducing binding energy by ~16%.
3. These findings are consistent with the structural rationale for Osimertinib (3rd-generation, covalent) development.
4. ColabFold successfully predicted the T790M mutant structure with RMSD 0.682 Å vs. X-ray, validating its use in structure-based design workflows.

### Natural Next Step

Analyzing how gefitinib occupies the EGFR binding pocket raised a complementary question:
rather than characterizing how a small molecule fits an existing protein,
*can a new protein be designed from scratch to bind a chosen small molecule?*
This question motivated the second project in this portfolio:
[Progesterone De Novo Design](https://github.com/jhkwin00/Progesterone-de-novo-design),
which applies the RFdiffusionAA → LigandMPNN → ColabFold pipeline
to design a de novo binder for progesterone.

---

## Future Directions

The following projects were planned as natural extensions of this work,
oriented toward the de novo protein design pipeline used in protein design labs:

| Step | Topic | Rationale |
| --- | --- | --- |
| Step 3 | ATP vs. Gefitinib binding comparison | Two ligands competing for the same pocket — understand what structural features differentiate selective vs. promiscuous binding |
| Step 4 | Osimertinib (3rd-gen) vs. Gefitinib docking in T790M pocket | Structural explanation for how 3rd-gen inhibitors overcome T790M resistance |
| Step 5 | A-loop conformation analysis (2GS7 / 4WKQ / 2GS6) | Active vs. inactive kinase state — per-region RMSD and visual comparison; extends the visual-only A-loop check performed in Project 2 |
| Step 6 | ColabFold prediction vs. actual T790M crystal structure (PDB: 2JIV) | Project 2 validated against 4WKQ with manual T790M introduction — a more direct comparison would use 2JIV, the experimentally solved T790M structure |

---

## References

- Yun, C.H. et al. (2008). The T790M mutation in EGFR kinase causes drug resistance by increasing the affinity for ATP. *PNAS*
- Trott, O. & Olson, A.J. (2010). AutoDock Vina: improving the speed and accuracy of docking. *J. Comp. Chem.*
- Mirdita, M. et al. (2022). ColabFold: Making protein folding accessible to all. *Nature Methods*
- Dauparas, J., Lee, G.R. et al. (2025). Atomic context-conditioned protein sequence design using LigandMPNN. *Nature Methods* 22, 717–723. https://doi.org/10.1038/s41592-025-02626-1
- PDB ID: 4WKQ — EGFR kinase domain in complex with Gefitinib

---

## Author

Portfolio project for Protein Design lab application — Janghyun Kim
✉️ jhkwin00@gmail.com · jhkwin00@naver.com
GitHub: [@jhkwin00](https://github.com/jhkwin00) · 2026
