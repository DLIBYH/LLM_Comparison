# LLM Medical Communication Quality Assessment Pipeline

A reproducible pipeline for scoring LLM responses to patient-facing medical questions across three communication dimensions: **Safety**, **Empathy**, and **Explanation**.

## 📋 Overview

This notebook implements a systematic evaluation framework for assessing Large Language Model (LLM) responses to patient medical questions. The pipeline uses literature-grounded communication frameworks to score responses on a 1-5 scale, with rigorous data splitting and calibration procedures.

### Key Features

- **Three-Dimensional Scoring**: Evaluates responses on Safety, Empathy, and Explanation dimensions
- **Literature-Grounded Framework**: Constructs mapping to validated instruments (CARE Measure, RIAS, Epstein & Street 2007, WHO/FDA guidance)
- **Controlled Semantic Expansion**: Expands seed terms exclusively from development data with full audit trail
- **Question-Level Splitting**: Prevents data leakage between development and evaluation sets
- **Human Doctor Reference**: Uses doctor responses as reference for agreement metrics without influencing scoring
- **Comprehensive Metrics**: Includes lexical, semantic, and claim-coverage agreement measures
- **Statistical Validation**: Friedman tests and Wilcoxon signed-rank tests with Holm correction

## 🚀 Quick Start

### Prerequisites

```bash
pip install sentence-transformers textblob scikit-learn pandas numpy matplotlib seaborn scipy statsmodels
```

### Usage

1. **Prepare your dataset**: Ensure your CSV file contains columns for:
   - `Question`: The medical question asked
   - `Human Doctor Response`: Reference doctor response
   - LLM response columns (e.g., `Chatgpt`, `Grok 3`, `Gemini`, `DeepSeek`)

2. **Run the notebook**: Execute cells sequentially in a Jupyter environment or Google Colab

3. **Upload dataset**: When prompted, upload your CSV file containing the medical QA data

## 🏗️ Pipeline Architecture

### 1. Data Loading & Validation
- Supports multiple encodings (UTF-8, CP1252, Latin-1)
- Auto-detects LLM response columns
- Validates required columns presence

### 2. Development/Evaluation Split
- **Question-level split**: 70% development / 30% evaluation
- Prevents leakage of evaluation questions into vocabulary construction
- Only LLM responses from development set used for calibration

### 3. Semantic Expansion
- Literature-grounded seed terms for each dimension:
  - **Safety**: emergency, urgent, warning, risk, harm, safety, etc.
  - **Empathy**: understand, concern, support, compassion, reassure, etc.
  - **Explanation**: because, evidence, research, demonstrate, clinical, etc.
- Controlled expansion with thresholds:
  - Minimum semantic similarity: 0.60
  - Minimum term frequency: 2
  - Maximum expansions per seed: 12
- Full audit trail of all expansion decisions

### 4. Rubric Construction
Maps to established frameworks:

| Dimension | Framework |
|-----------|-----------|
| Safety | WHO Emergency Triage, FDA Medication Safety, CARE Item 10 |
| Empathy | CARE Items 5,6,9,10, RIAS Socioemotional Category |
| Explanation | Epstein & Street, Doak, Doak & Root (1996) |

### 5. Calibration (Development Set Only)
- Group weights via inverse-document-frequency style rarity
- Sentiment thresholds (25th/75th percentiles)
- Length percentiles (P50/P85 for explanation bonus)
- 1-5 score boundaries (P20/P40/P60/P80)

### 6. Evaluation
- Applies frozen parameters to held-out questions
- Critical overrides for safety violations, dismissiveness, bare answers
- Overall score = average of three dimensions

### 7. Human Agreement Metrics
- **Lexical TF-IDF**: Cosine similarity of TF-IDF vectors
- **Jaccard**: Word-set overlap
- **Semantic Document**: Whole-response embedding similarity
- **Claim Coverage**: Sentence-level semantic matching with human doctor

### 8. Statistical Analysis
- Friedman test for overall model differences
- Wilcoxon signed-rank tests for pairwise comparisons
- Holm correction for multiple comparisons

## 📊 Outputs

The pipeline produces:

- **Rubric Scores**: 1-5 scores for each LLM on Safety, Empathy, and Explanation
- **Ranking**: Comparative ranking of LLM models
- **Agreement Metrics**: Multiple measures of alignment with human doctor responses
- **Correlation Analysis**: Spearman correlations between rubric scores and human agreement
- **Visualizations**: Box plots and bar charts for all metrics
- **Audit Tables**: Complete records of semantic expansion decisions

## 📁 Dataset Format

Your CSV file should have the following structure:

| Column | Required | Description |
|--------|----------|-------------|
| Serial No. | No | Question identifier |
| Question | Yes | Patient medical question |
| Human Doctor Response | Yes | Reference response from medical professional |
| [LLM_Name] | At least 2 | Responses from LLMs to evaluate |

## 🔬 Planned Extensions

- [ ] External clinician review of expanded term lists and automated scores
- [ ] Expanded question set (additional specialties, difficulty levels, demographic framings)
- [ ] LLM-as-judge baseline with careful prompt design
- [ ] Additional validation against established clinical communication scales

## 📝 Citation

If you use this pipeline in your research, please cite:

```
[Citation information to be added]
```
