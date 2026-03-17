# Immune Response Schema — Inter-Stage Contracts

Source: Murphy & Weaver, *Janeway's Immunobiology* (9th ed.); Abbas, Lichtman & Pillai, *Cellular and Molecular Immunology* (10th ed.).

## Roles

| Role | Definition |
|------|-----------|
| **Recognition** | Detect foreign antigen via pattern-matching receptors |
| **Activation** | Antigen-presenting cells trigger the adaptive response |
| **Clonal Expansion** | Selected lymphocytes proliferate rapidly |
| **Effector Response** | Activated cells neutralize or destroy the threat |
| **Contraction** | Excess effector cells are culled after the threat subsides |
| **Memory** | Surviving cells persist as long-lived memory for future encounters |

## Contracts

### Recognition → Activation (Two-Signal Model)

**Recognition must produce:**
- MHC-peptide complex (Class I: 8-10 aa peptides for CD8+; Class II: 13-25 aa for CD4+)
- Sufficient TCR affinity (KD typically 1-100 uM)
- Proper MHC restriction (TCR must match self-MHC allele)

**Activation requires three signals:**
1. TCR engagement with MHC-peptide complex (>100 TCR molecules clustered)
2. Co-stimulation via CD28-B7 interaction
3. Cytokine milieu (IL-12 for Th1, IL-4 for Th2, TGF-beta + IL-6 for Th17)

**Failure mode:** Signal 1 without Signal 2 → anergy (functional unresponsiveness). This is a built-in safety contract.

### Activation → Clonal Expansion

**Activation must produce:**
- CD25 upregulation (50-100 fold within 4-6 hours)
- Autocrine IL-2 secretion (10-100 pg/ml)
- Cell cycle entry (G0 → G1 via cyclin D/CDK4/6)
- Metabolic switch to aerobic glycolysis (Warburg effect)

**Clonal Expansion requires:**
- Sustained IL-2 signaling (48-72 hours minimum)
- mTOR activation (PI3K/AKT/mTOR drives biosynthesis)
- Glucose availability (GLUT1 upregulated 10-20 fold)

**Quantitative contract:** Doubling time 4-6 hours, 7-10 doublings total, peak at 5-7 days.

### Clonal Expansion → Effector Response

**Expansion must produce:**
- Threshold cell count (~1000 antigen-specific cells per clone minimum)
- Terminal differentiation markers (loss of CD62L, CCR7; gain of CD44, CD69)
- Effector molecule expression (perforin/granzymes for CTL; cytokines for Th cells)

**Effector Response requires:**
- Tissue migration capacity (integrins VLA-4, LFA-1)
- Pre-formed effector molecules in granules
- Metabolic fitness (high glycolytic rate)

### Effector Response → Contraction

**Contraction triggered by:**
- Antigen clearance (TCR signal drops below threshold)
- Cytokine withdrawal (IL-2, IL-7, IL-15 decline)
- Pro-apoptotic signaling (Fas-FasL, Bim upregulation, Bcl-2/Bcl-xL loss)

**Quantitative contract:** 90-95% of effector cells die. Contraction begins 7-10 days post-activation.

### Contraction → Memory (Bifurcation)

**Memory fate determined by:**
- IL-7Ralpha (CD127) expression — high = memory precursor
- Bcl-2 expression — high = survival
- KLRG1 expression — low = memory, high = terminal effector
- Metabolic phenotype — fatty acid oxidation (memory) vs glycolysis (effector)

**Quantitative contract:** 5-10% of peak effector population becomes memory. Stabilizes 3-4 weeks post-infection. Persists years to decades.

## Contract Strength

**Very strong.** The immune system has molecularly precise contracts at every handoff — specific receptor affinities, cytokine concentrations, cell counts, metabolic states, and timing constraints. These are biologically mandatory: bypass at any handoff causes immune dysfunction (autoimmunity, immunodeficiency, chronic inflammation). The contracts are about biochemical content at each transition, which is closer to NF's "data content" contracts than Shannon's information-theoretic limits.
