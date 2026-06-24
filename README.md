# Water-Protein Efficiency Mapping

A data integration and analysis pipeline for evaluating the **water efficiency of protein sources**, combining nutritional composition data from the Brazilian Food Composition Table (TACO), water footprint indicators from the Water Footprint Network (WFN), and protein quality scores (DIAAS).

The project automatically cleans raw datasets, translates food names, performs semantic matching between heterogeneous food databases using NLP models, calculates water-use efficiency indicators, and generates publication-ready visualizations.

---

## Overview

This project aims to answer the following question:

> Which foods deliver the highest amount of digestible protein per unit of water consumed during production?

To achieve this, the workflow integrates:

- **TACO** (Brazilian Food Composition Table) – protein content
- **Water Footprint Network (WFN)** reports – green, blue, and grey water footprints
- **DIAAS** (Digestible Indispensable Amino Acid Score) – protein quality and digestibility

The final output is a ranking of foods according to:

- Protein content
- Digestible protein content
- Water footprint
- Water efficiency of protein production

---

## Methodology

### 1. Water Footprint Data Extraction

Raw Excel appendices from the Water Footprint Network are cleaned and standardized.

**Sources:**

- Report 47 – Crop products
- Report 48 – Animal products

The scripts:

- Remove complex headers
- Forward-fill product names
- Extract Green, Blue, and Grey water components
- Pivot water types into columns
- Compute total water footprint

**Outputs:**

```text
waterstat_animais_limpo.xlsx
waterstat_vegetais_global_limpo.xlsx
```

### 2. TACO Processing

The TACO dataset is processed to:

- Identify food names and protein columns automatically
- Filter foods containing at least 6 g protein per 100 g
- Translate food names from Portuguese to English
- Prepare data for semantic matching

**Output:**

```text
taco_traduzida.xlsx
```

### 3. Food Matching

Since food names differ across databases, a hybrid matching strategy is employed.

#### Rule-Based Matching

A set of manually defined mappings anchors important food categories.

| TACO Food | WFN Category |
|------------|------------|
| Beef | Bovine cuts |
| Chicken | Gallus domesticus meat |
| Rice | Rice |
| Beans | Dry beans |
| Soybean | Soybeans |

#### Semantic Matching

For foods not covered by rules:

- Sentence embeddings are generated using the model:

```python
all-MiniLM-L6-v2
```

- Cosine similarity is calculated against all WFN products
- The highest similarity candidate is selected

**Output:**

```text
1_revisao_hibrida.xlsx
```

This file allows manual review of uncertain matches.

### 4. Data Integration

The pipeline merges:

```text
TACO
+
Water Footprint Database
+
DIAAS Database
```

For each food, the following information becomes available:

- Protein content
- Digestible protein content
- Green water footprint
- Blue water footprint
- Grey water footprint
- Total water footprint

### 5. Efficiency Calculations

#### Raw Protein Efficiency

\[
E_{raw}=\frac{Protein}{WaterFootprint}
\]

#### Digestible Protein

\[
Protein_{digestible}=Protein \times \frac{DIAAS}{100}
\]

#### Real Water Efficiency

\[
E_{real}=\frac{Protein_{digestible}}{WaterFootprint}
\]

**Output:**

```text
2_eficiencia_proteica_real_hibrida.xlsx
```

---

## Generated Visualizations

### 1. Water Footprint Composition

Stacked bars showing:

- Green water (rainfall)
- Blue water (surface and groundwater)
- Grey water (pollution-related)

**Output:**

```text
grafico1_pegada_empilhada.png
```

### 2. Water vs Protein Scatter Plot

Relationship between:

- Total water footprint
- Digestible protein

**Output:**

```text
grafico2_dispersao.png
```

### 3. Raw vs Real Efficiency

Comparison between:

- Protein efficiency without digestibility correction
- Protein efficiency adjusted by DIAAS

**Output:**

```text
grafico3_eficiencia_comparada.png
```

### 4. Highest Protein Foods

Comparison between:

- Total protein
- Digestible protein

**Output:**

```text
grafico4_top_proteinas.png
```

### 5. Category-Level Analysis

Trade-off between:

- Average water footprint
- Average digestible protein

**Output:**

```text
grafico5_categorias_eixos_duplos.png
```

### 6. Sustainability Bubble Chart

Multidimensional visualization:

| Dimension | Variable |
|------------|------------|
| X-axis | Water footprint |
| Y-axis | Digestible protein |
| Bubble size | Water efficiency |
| Color | Food category |

**Output:**

```text
grafico6_bolhas_multidimensionais.png
```

---

## Project Structure

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
├── grafico1_pegada_empilhada.png
├── grafico2_dispersao.png
├── grafico3_eficiencia_comparada.png
├── grafico4_top_proteinas.png
├── grafico5_categorias_eixos_duplos.png
├── grafico6_bolhas_multidimensionais.png
│
└── mapeamento_eficiencia_hidrica.ipynb
```

---

## Requirements

Install dependencies:

```bash
pip install pandas numpy openpyxl
pip install sentence-transformers torch
pip install deep-translator tqdm
pip install rapidfuzz
pip install matplotlib seaborn
```

---

## Running the Pipeline

### Step 1 – Clean Water Footprint Data

```python
limpar_planilha_wfn(...)
limpar_planilha_vegetais(...)
```

Generates:

```text
waterstat_animais_limpo.xlsx
waterstat_vegetais_global_limpo.xlsx
```

### Step 2 – Prepare TACO

```python
preparar_taco(...)
```

Generates:

```text
taco_traduzida.xlsx
```

### Step 3 – Run Semantic Matching

Generates:

```text
1_revisao_hibrida.xlsx
```

Review this file if desired.

### Step 4 – Calculate Water Efficiency

Generates:

```text
2_eficiencia_proteica_real_hibrida.xlsx
```

### Step 5 – Generate Figures

Produces:

```text
grafico1_pegada_empilhada.png
grafico2_dispersao.png
grafico3_eficiencia_comparada.png
grafico4_top_proteinas.png
grafico5_categorias_eixos_duplos.png
grafico6_bolhas_multidimensionais.png
```

---

## Scientific Contribution

This workflow provides a reproducible framework for evaluating food sustainability by integrating:

- Environmental indicators (water footprint)
- Nutritional quantity (protein content)
- Nutritional quality (DIAAS)

The resulting metric allows comparisons that go beyond traditional environmental assessments by incorporating the biological value of the protein delivered by each food source.

---

## Citation

```bibtex
@software{water_protein_efficiency,
  title={Water-Protein Efficiency Mapping},
  author={Your Name},
  year={2026},
  url={https://github.com/anonymous}
}
```
