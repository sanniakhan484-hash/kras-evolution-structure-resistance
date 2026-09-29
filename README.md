# Evolutionary and Structural Analysis of Oncogenic KRAS Mutations and Inhibitor Resistance

An integrated computational biology project linking **sequence evolution**, **protein structure**, **cancer genomics**, and **drug resistance** in KRAS — one of the most frequently mutated oncogenes in human cancer.

> 📄 **Full report:** [`KRAS_Project_Report.docx`](./KRAS_Project_Report.docx) — read this for complete methods, results, and discussion.
> 📓 **Full analysis notebook:** [`notebooks/01_sequence_and_blast.ipynb`](./notebooks/01_sequence_and_blast.ipynb) — every step below is reproducible from this single notebook.

---

## Project summary

KRAS acts as a molecular on/off switch for cell growth. Mutations that lock it "on" drive roughly a quarter of all human cancers, and after decades of being considered undruggable, it is now targeted by approved inhibitors (sotorasib, adagrasib) — against which tumors reliably develop resistance.

This project asks: **why do cancer mutations cluster where they do, and how does that relate to drug targeting and resistance?** It answers this by combining four independent lines of evidence into one pipeline:

1. **Evolutionary conservation** — how invariant is each KRAS residue across species and related proteins?
2. **3D structure** — where do conserved residues sit in the folded protein?
3. **Real cancer mutation data** — do actual tumor mutations concentrate at conserved positions?
4. **Drug pocket & resistance** — does the structural drug-binding site overlap with known resistance mutations?
5. **Machine learning** — can these features predict clinical variant pathogenicity?

## Key findings

- KRAS's catalytic G-domain is highly conserved (mean entropy ≈ 0–0.2 across all functional motifs); the C-terminal hypervariable tail is ~10–50× more variable (entropy 1.67).
- **278 real pan-cancer mutations** (PCAWG cohort) concentrate at just 13 positions, all within the conserved G-domain — 82% at G12 alone.
- The structurally defined sotorasib-binding pocket (21 residues, 4.5 Å cutoff) independently recovers every position reported in the clinical literature as a site of acquired drug resistance (G13, A59, R68, H95, Y96, Q99, C12).
- A simple 4-feature model (conservation, structural pocket membership, hydrophobicity change, glycine loss) distinguishes pathogenic from uncertain-significance ClinVar variants with **AUROC = 0.723**, with conservation as the strongest predictor.

## Repository structure

```
kras-evolution-structure-resistance/
├── data/                          # Raw and processed data files
│   ├── kras_blast_swissprot.xml       # Raw BLAST XML output
│   ├── kras_blast_hits.csv            # Parsed BLAST hit table
│   ├── ras_subfamily.fasta            # 23 close homologs (≥80% identity)
│   ├── ras_subfamily_aligned.fasta    # MAFFT alignment
│   ├── ras_superfamily.fasta          # 95 non-viral homologs
│   ├── ras_superfamily_aligned.fasta  # MAFFT alignment
│   ├── conservation_subfamily.csv     # Per-residue entropy (subfamily)
│   ├── conservation_superfamily.csv   # Per-residue entropy (superfamily)
│   ├── kras_cbioportal_mutations_*.csv  # PCAWG cancer mutation data
│   ├── kras_integrated_table.csv      # Conservation + mutation + pocket merge
│   └── kras_ml_dataset.csv            # ClinVar variants with engineered features
├── notebooks/
│   └── 01_sequence_and_blast.ipynb    # Full, annotated, reproducible analysis
├── results/figures/                # All figures referenced in the report
│   ├── identity_histogram.png
│   ├── conservation_plot.png
│   ├── kras_structure_conservation.png
│   ├── kras_structure_ploop_highlighted.png
│   └── mutation_vs_conservation.png
├── KRAS_Project_Report.docx        # Full written report
├── README.md
└── LICENSE
```

## Methods and tools

| Step | Tool / Source |
|---|---|
| Sequence retrieval | UniProt REST API |
| Homology search | NCBI BLAST (blastp vs. Swiss-Prot) |
| Multiple sequence alignment | MAFFT v7.505 |
| Conservation scoring | Shannon entropy (custom Python/Biopython) |
| Structure prediction | AlphaFold DB |
| Experimental structures | RCSB PDB (4OBE, 6OIM) |
| Structure visualization | py3Dmol, Mol* |
| Cancer mutation data | cBioPortal API (PCAWG pan-cancer study) |
| Drug pocket analysis | Biopython `NeighborSearch` (4.5 Å cutoff) |
| Clinical variant data | NCBI ClinVar (E-utilities API) |
| Machine learning | scikit-learn (Logistic Regression, Random Forest) |

All tools are free and publicly accessible; the entire pipeline runs in Google Colab with no local installation required.

## Reproducing this analysis

1. Open `notebooks/01_sequence_and_blast.ipynb` in Google Colab.
2. Run cells in order — each phase installs its own dependencies (`biopython`, `mafft`, etc.) at the point it's needed.
3. Data pulled from live APIs (UniProt, NCBI, cBioPortal, ClinVar, AlphaFold DB) may return slightly updated results if run at a later date, since these databases are continuously updated.

## Limitations

This project is transparent about its constraints — see the **Limitations** section of the full report for details, including: the mutation-vs-conservation statistical test not reaching significance (small n), the use of "uncertain significance" as a proxy negative class for the ML model (due to near-absent confirmed-benign KRAS variants in ClinVar), and scope limited to sotorasib rather than all approved KRAS inhibitors.

## Future directions

- Benchmark the ML classifier against AlphaMissense
- Extend drug-pocket analysis to adagrasib (PDB 6UT0) and newer inhibitors
- Molecular docking (AutoDock Vina) for a computational drug-design extension
- Apply the same pipeline to other oncogenes (EGFR, BRAF) to test generality

## Author

Saniya Khan — completed as part of the CodeAlpha Bioinformatics Internship, September 2026.

## License

MIT License — see [`LICENSE`](./LICENSE).
