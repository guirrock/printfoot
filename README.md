# DPWEI: Digestible Protein Water Efficiency Index

An open-source implementation of the **Digestible Protein Water Efficiency Index (DPWEI)**, a comparative indicator that integrates protein digestibility and water footprint to evaluate the nutritional efficiency of water use among protein sources.

The project provides a fully reproducible computational workflow for integrating nutritional composition data from the Brazilian Food Composition Table (TACO), water footprint indicators from the Water Footprint Network (WFN), and protein quality scores based on the Digestible Indispensable Amino Acid Score (DIAAS). The pipeline automatically prepares heterogeneous datasets, performs food matching, calculates the proposed DPWEI, and generates publication-ready tables and figures.

---

# Overview

This project was developed to answer the following question:

> **Which protein sources provide the greatest amount of digestible protein relative to the water required for their production?**

To answer this question, the workflow integrates three complementary sources of information:

- **TACO** (Brazilian Food Composition Table) — nutritional composition
- **Water Footprint Network (WFN)** — green, blue, and grey water footprints
- **DIAAS** (Digestible Indispensable Amino Acid Score) — protein digestibility and quality

The resulting dataset enables the calculation of two complementary indicators:

- **Gross Water-Use Efficiency (GWUE)**
- **Digestible Protein Water Efficiency Index (DPWEI)**

The pipeline also generates publication-ready tables and visualizations used in the accompanying scientific publication.

---

# Methodology

## 1. Water Footprint Data Extraction

Raw Excel appendices from the Water Footprint Network are cleaned and standardized.

**Sources**

- Report 47 – Crop Products
- Report 48 – Animal Products

The scripts:

- Remove complex headers
- Forward-fill product names
- Extract Green, Blue, and Grey water components
- Pivot water types into columns
- Compute total water footprint

**Outputs**

```text
waterstat_animais_limpo.xlsx
waterstat_vegetais_global_limpo.xlsx
```

---

## 2. TACO Processing

The TACO dataset is processed to:

- Identify food names and protein columns automatically
- Filter foods containing at least 6 g protein per 100 g
- Translate food names from Portuguese to English
- Prepare the dataset for semantic matching

**Output**

```text
taco_traduzida.xlsx
```

---

## 3. Food Matching

Food names differ considerably between nutritional and environmental databases.

To integrate both datasets, the workflow employs a hybrid matching strategy.

### Rule-Based Matching

A manually curated mapping anchors important food categories.

| TACO Food | WFN Category |
|------------|--------------|
| Beef | Bovine cuts |
| Chicken | Gallus domesticus meat |
| Rice | Rice |
| Beans | Dry beans |
| Soybean | Soybeans |

### Semantic Matching

Foods not covered by manual rules are matched automatically.

The workflow:

- Generates sentence embeddings using

```python
all-MiniLM-L6-v2
```

- Computes cosine similarity
- Selects the highest similarity candidate

**Output**

```text
1_revisao_hibrida.xlsx
```

The resulting spreadsheet allows manual validation before the final integration.

---

## 4. Data Integration

The pipeline merges:

```text
TACO
+
Water Footprint Database
+
DIAAS Database
```

The integrated dataset contains:

- Crude protein
- Digestible protein
- Green water footprint
- Blue water footprint
- Grey water footprint
- Total water footprint

---

## 5. Water-Use Efficiency Indicators

### Gross Water-Use Efficiency (GWUE)

Measures the amount of crude protein available per unit of water footprint.

\[
GWUE=\frac{Protein}{WaterFootprint}
\]

---

### Digestible Protein

Digestible protein is estimated as

\[
DigestibleProtein = Protein \times \frac{DIAAS}{100}
\]

---

### Digestible Protein Water Efficiency Index (DPWEI)

The proposed DPWEI integrates digestible protein and total water footprint into a single comparative indicator.

\[
DPWEI=\frac{DigestibleProtein}{WaterFootprint}
\]

For presentation purposes, DPWEI values are multiplied by a constant factor of 1000, producing the **DPWEI Score** reported in the publication.

Higher DPWEI Scores indicate more favorable combinations of digestible protein availability and water footprint.

**Output**

```text
2_eficiencia_proteica_real_hibrida.xlsx
```

---

# Generated Visualizations

The pipeline automatically produces publication-ready figures, including:

- Ranking of foods according to DPWEI Score
- Digestible protein versus total water footprint
- Water footprint composition (green, blue, and grey)
- Grey water footprint ranking

---

# Project Structure

```text
.
├── taco.xlsx
├── Tabela DIAAS.xlsx
├── Report47-Appendix-II.xlsx
├── Report48-Appendix-V.xlsx
│
├── waterstat_animais_limpo.xlsx
├── waterstat_vegetais_global_limpo.xlsx
├── taco_traduzida.xlsx
├── 1_revisao_hibrida.xlsx
├── 2_eficiencia_proteica_real_hibrida.xlsx
│
├── figures/
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
pip install matplotlib seaborn
```

---

# Running the Pipeline

1. Clean WFN datasets

```python
limpar_planilha_wfn(...)
limpar_planilha_vegetais(...)
```

2. Prepare the TACO database

```python
preparar_taco(...)
```

3. Execute semantic matching

4. Review the generated matching file (optional)

5. Calculate GWUE and DPWEI

6. Generate publication-ready figures

---

# Scientific Contribution

This repository implements the **Digestible Protein Water Efficiency Index (DPWEI)**, a novel comparative indicator proposed to integrate nutritional quality and environmental impact into a unified assessment of protein production.

Unlike conventional water footprint analyses based solely on crude protein content, the DPWEI incorporates protein digestibility through the Digestible Indispensable Amino Acid Score (DIAAS), enabling more meaningful comparisons among plant- and animal-based protein sources.

Beyond the case study presented in the associated publication, the workflow provides a reproducible methodology that can be adapted to combine national food composition databases with regional or global water footprint inventories, supporting future research within the **Water–Food–Nutrition Nexus**.

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
  url={https://github.com/anonymous}
}
```
