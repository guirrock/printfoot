# Water-Protein Efficiency Mapping

A reproducible data integration and analysis pipeline for evaluating the **nutritional efficiency of water use among protein sources**. The workflow combines nutritional composition data from the Brazilian Food Composition Table (TACO), water footprint indicators from the Water Footprint Network (WFN), and protein quality scores based on the Digestible Indispensable Amino Acid Score (DIAAS).

The project automatically cleans raw datasets, translates food names, performs semantic matching between heterogeneous food databases, calculates water-use efficiency indicators, computes the **Digestible Protein Water Efficiency Index (DPWEI)**, and generates publication-ready visualizations.

---

# Overview

This project aims to answer the following question:

> **Which protein sources provide the greatest amount of digestible protein relative to the water required for their production?**

To achieve this, the workflow integrates:

- **TACO** (Brazilian Food Composition Table) – protein composition
- **Water Footprint Network (WFN)** reports – green, blue, and grey water footprints
- **DIAAS** (Digestible Indispensable Amino Acid Score) – protein digestibility and quality

The final output is a ranked dataset containing:

- Crude protein content
- Digestible protein content
- Green water footprint
- Blue water footprint
- Grey water footprint
- Total water footprint
- Gross Water-Use Efficiency (GWUE)
- Digestible Protein Water Efficiency Index (DPWEI)

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

Food names differ substantially across nutritional and environmental databases.

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

- Computes cosine similarity against every WFN product
- Selects the highest similarity candidate

**Output**

```text
1_revisao_hibrida.xlsx
```

This file allows manual review of uncertain matches before data integration.

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

For each food, the integrated dataset contains:

- Crude protein
- Digestible protein
- Green water footprint
- Blue water footprint
- Grey water footprint
- Total water footprint

---

## 5. Water-Use Efficiency Indicators

Two complementary indicators are calculated.

### Gross Water-Use Efficiency (GWUE)

\[
GWUE=\frac{Protein}{WaterFootprint}
\]

where:

- Protein = crude protein (g/100 g)
- WaterFootprint = total water footprint (m³/t)

---

### Digestible Protein

\[
DigestibleProtein = Protein \times \frac{DIAAS}{100}
\]

---

### Digestible Protein Water Efficiency Index (DPWEI)

\[
DPWEI=\frac{DigestibleProtein}{WaterFootprint}
\]

For presentation purposes, DPWEI values are multiplied by a constant scaling factor (1000), producing the **DPWEI Score** used in the associated publication.

Higher DPWEI Scores indicate more favorable combinations of protein digestibility and water footprint.

**Output**

```text
2_eficiencia_proteica_real_hibrida.xlsx
```

---

# Generated Visualizations

## 1. Top Foods Ranked by DPWEI Score

Horizontal ranking of the highest-performing protein sources according to the proposed indicator.

**Output**

```text
grafico1_top_dpwei.png
```

---

## 2. Digestible Protein × Water Footprint

Scatter plot showing:

- Digestible protein
- Total water footprint
- DPWEI Score (color scale)

**Output**

```text
grafico2_dispersao.png
```

---

## 3. Water Footprint Composition

Horizontal stacked bars showing:

- Green water
- Blue water
- Grey water

for the highest-ranked foods according to DPWEI.

**Output**

```text
grafico3_pegada_empilhada.png
```

---

## 4. Gray Water Footprint Ranking

Ranking of all evaluated foods according to their grey water footprint.

**Output**

```text
grafico4_agua_cinza.png
```

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
├── grafico1_top_dpwei.png
├── grafico2_dispersao.png
├── grafico3_pegada_empilhada.png
├── grafico4_agua_cinza.png
│
└── mapeamento_eficiencia_hidrica.ipynb
```

---

# Requirements

Install the required dependencies:

```bash
pip install pandas numpy openpyxl
pip install sentence-transformers torch
pip install deep-translator tqdm
pip install rapidfuzz
pip install matplotlib seaborn
```

---

# Running the Pipeline

## Step 1 – Clean Water Footprint Data

```python
limpar_planilha_wfn(...)
limpar_planilha_vegetais(...)
```

Generates

```text
waterstat_animais_limpo.xlsx
waterstat_vegetais_global_limpo.xlsx
```

---

## Step 2 – Prepare TACO

```python
preparar_taco(...)
```

Generates

```text
taco_traduzida.xlsx
```

---

## Step 3 – Run Food Matching

Generates

```text
1_revisao_hibrida.xlsx
```

Review the generated file if necessary.

---

## Step 4 – Calculate Efficiency Indicators

Generates

```text
2_eficiencia_proteica_real_hibrida.xlsx
```

including:

- GWUE
- DPWEI
- DPWEI Score

---

## Step 5 – Generate Figures

Produces

```text
grafico1_top_dpwei.png
grafico2_dispersao.png
grafico3_pegada_empilhada.png
grafico4_agua_cinza.png
```

---

# Scientific Contribution

This workflow provides a reproducible framework for integrating environmental and nutritional datasets within the **Water–Food–Nutrition Nexus**.

Its primary contribution is the implementation of the **Digestible Protein Water Efficiency Index (DPWEI)**, a comparative indicator that integrates:

- Environmental impact (water footprint)
- Nutritional quantity (protein content)
- Nutritional quality (digestibility through DIAAS)

Unlike conventional water footprint assessments based solely on crude protein, the DPWEI incorporates digestible protein, allowing more meaningful comparisons among plant- and animal-based protein sources.

The workflow can be adapted to integrate other national food composition databases with regional or global water footprint datasets, facilitating future sustainability assessments and supporting evidence-based decision making in agriculture, nutrition, and food policy.

---

# Citation

```bibtex
@software{water_protein_efficiency,
  title={Water-Protein Efficiency Mapping},
  author={Your Name},
  year={2026},
  url={https://github.com/anonymous}
}
```
