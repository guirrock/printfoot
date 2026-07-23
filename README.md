# DPWEI: Digestible Protein Water Efficiency Index

An open-source implementation of the **Digestible Protein Water Efficiency Index (DPWEI)**, a comparative indicator that integrates protein digestibility and water footprint to evaluate the nutritional efficiency of water use among protein sources.

The repository provides a reproducible workflow that integrates nutritional composition data from the Brazilian Food Composition Table (TACO), water footprint data from the Water Footprint Network (WFN), and protein quality information based on the Digestible Indispensable Amino Acid Score (DIAAS).

The pipeline automatically prepares heterogeneous datasets, performs semantic food matching, calculates the proposed DPWEI, and generates the final dataset used in the associated scientific publication.

---

# Overview

This project addresses the following research question:

> **Which protein sources provide the greatest amount of digestible protein relative to the water required for their production?**

To answer this question, the workflow integrates three complementary datasets:

- **TACO** – Brazilian Food Composition Table
- **Water Footprint Network (WFN)** – Green, Blue and Grey Water Footprints
- **DIAAS** – Digestible Indispensable Amino Acid Score

The resulting dataset contains:

- Crude protein
- Digestible protein
- Green water footprint
- Blue water footprint
- Grey water footprint
- Total water footprint
- Gross Water-Use Efficiency (GWUE)
- Digestible Protein Water Efficiency Index (DPWEI)

---

# Methodology

The workflow consists of five main stages.

## 1. Water Footprint Processing

Raw Water Footprint Network appendices are cleaned and standardized.

Input files:

- `Report47-Appendix-II.xlsx`
- `Report48-Appendix-V.xlsx`

Generated files:

```text
waterstat_animais_limpo.xlsx
waterstat_vegetais_limpo.xlsx
```

The scripts:

- remove complex headers;
- forward-fill missing product names;
- extract Green, Blue and Grey water values;
- calculate total water footprint.

---

## 2. TACO Processing

The Brazilian Food Composition Table is automatically processed to:

- identify food names and protein columns;
- filter foods containing at least 6 g protein per 100 g;
- translate food names into English.

Input:

```text
taco.xlsx
```

Output:

```text
taco_traduzida.xlsx
```

---

## 3. Food Matching

Food names differ considerably across databases.

To integrate them, the workflow combines:

- rule-based matching;
- semantic similarity using Sentence Transformers (`all-MiniLM-L6-v2`).

The resulting correspondence table is exported as:

```text
1_revisao_hibrida.xlsx
```

This spreadsheet allows manual inspection of uncertain matches before the final integration.

---

## 4. Data Integration

The workflow merges:

```text
TACO
+
Water Footprint
+
DIAAS
```

For each food, the final dataset contains:

- protein content;
- digestible protein;
- water footprint components;
- total water footprint.

---

## 5. DPWEI Calculation

Two complementary indicators are computed.

### Gross Water-Use Efficiency (GWUE)

\[
GWUE=\frac{Protein}{WaterFootprint}
\]

---

### Digestible Protein

\[
DigestibleProtein=Protein\times\frac{DIAAS}{100}
\]

---

### Digestible Protein Water Efficiency Index (DPWEI)

\[
DPWEI=\frac{DigestibleProtein}{WaterFootprint}
\]

For visualization purposes, DPWEI values are multiplied by a constant scaling factor, generating the **DPWEI Score** reported in the associated publication.

The resulting datasets are exported as:

```text
2_eficiencia_proteica_real_hibrida.xlsx

Final 2_eficiencia_proteica_real_hibrida_inglês.xlsx
```

---

# Repository Structure

```text# Repository Structure

.
├── taco.xlsx
├── Tabela DIAAS.xlsx
├── Report47-Appendix-II.xlsx
├── Report48-Appendix-V.xlsx
│
├── waterstat_animais_limpo.xlsx
├── waterstat_vegetais_limpo.xlsx
├── taco_traduzida.xlsx
├── 1_revisao_hibrida.xlsx
├── 2_eficiencia_proteica_real_hibrida.xlsx
├── Final 2_eficiencia_proteica_real_hibrida_inglês.xlsx
│
├── figures/
│   ├── fig1.png
│   ├── fig2.png
│   ├── fig3.png
│   └── fig4.png
│
└── mapeamento_eficiencia_hidrica.ipynb
```

---

# Requirements

```bash
pip install pandas numpy openpyxl
pip install sentence-transformers torch
pip install deep-translator tqdm
pip install rapidfuzz
pip install matplotlib
```

---

# Running the Workflow

1. Download the WFN appendices and place them in the project directory.

2. Add:

```text
taco.xlsx
Tabela DIAAS.xlsx
```

3. Execute the notebook:

```text
mapeamento_eficiencia_hidrica.ipynb
```

The notebook automatically:

- cleans the WFN datasets;
- processes the TACO database;
- translates food names;
- performs semantic food matching;
- integrates all datasets;
- computes GWUE and DPWEI;
- exports the final datasets.

---

# Scientific Contribution

This repository implements the **Digestible Protein Water Efficiency Index (DPWEI)**, a comparative indicator proposed to integrate nutritional quality and environmental impact into a unified assessment of protein production.

Unlike conventional water footprint analyses based solely on crude protein content, the DPWEI incorporates protein digestibility through the Digestible Indispensable Amino Acid Score (DIAAS), enabling more meaningful comparisons among plant- and animal-based protein sources.

The workflow is fully reproducible and can be adapted to integrate alternative food composition databases and regional water footprint inventories, supporting future research within the **Water–Food–Nutrition Nexus**.

---

# Associated Publication

**Integrating Water Footprint and Protein Digestibility: The Digestible Protein Water Efficiency Index (DPWEI) for Assessing Sustainable Protein Production**

---

# Citation

```bibtex
@software{dpwei,
  title={DPWEI: Digestible Protein Water Efficiency Index},
  author={Your Name},
  year={2026},
  url={https://github.com/your-repository}
}
```
