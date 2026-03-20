# Skill: `analyze-dataset-alignment-diversity`

## Description

Analyzes the dataset and the baseline model's embeddings to evaluate two critical properties of contrastive representation learning: **Alignment** (closeness of paired/relevant samples) and **Diversity/Uniformity** (scattering of random samples across the hypersphere). This skill uses principles established in academic literature to diagnose dataset quality and model initialization before full-scale training, helping to prevent representation collapse.

## Theoretical Grounding

This analysis is heavily inspired by the paper _"Understanding Contrastive Representation Learning through Alignment and Uniformity on the Hypersphere"_ (Wang & Isola, 2020), as well as dense retrieval literature like SimCSE (Gao et al., 2021).

- **Alignment:** Measures how closely paired samples (e.g., a query and its positive passage) are mapped in the embedding space. Better alignment means lower distance:
  $\ell_{align} \triangleq \mathbb{E}_{(x, y) \sim p_{pos}} \| f(x) - f(y) \|^2_2$
- **Uniformity (Diversity):** Measures how well embeddings are distributed. If all embeddings cluster into a narrow cone (anisotropy), the model struggles to distinguish distinct concepts. High diversity/uniformity minimizes:
  $\ell_{uniform} \triangleq \log \mathbb{E}_{x, y \sim p_{data}} e^{-t \| f(x) - f(y) \|^2_2}$

## Trigger

Execute this skill when:

- Entering **Phase 1: Pre-Training Analysis & Strategy**.
- The human requests a deep dive into data quality or baseline embedding distributions.
- The model suffers from representation collapse (e.g., retrieving the same passages for entirely different queries) during Phase 3.

## Execution Steps

1. **Sample the Dataset:** Load a representative subset of the data (e.g., 1,000 to 5,000 query-passage pairs) to keep computation fast.
2. **Extract Baseline Embeddings:** Run the baseline SentenceTransformer model (without fine-tuning) to generate embeddings for queries, positive passages, and random passages.
3. **Calculate Alignment Metric:** Compute the average cosine similarity or $L_2$ distance between the queries and their corresponding _positive_ passages.
4. **Calculate Diversity/Uniformity Metric:** Compute the average cosine similarity between queries and _randomly sampled_ passages (or between random passages themselves) to check for embedding space collapse.
5. **Analyze the Gap:**
   - _High Alignment, Low Diversity:_ The model clusters everything too closely (anisotropy). _Recommendation:_ Propose aggressive hard negative mining or larger batch sizes with Multiple Negatives Ranking Loss (MNRL).
   - _Low Alignment, High Diversity:_ The model spreads concepts well but fails to connect relevant pairs. _Recommendation:_ Check the dataset for noisy labels, or propose lowering the learning rate to carefully pull positive pairs together.
6. **Formulate Report:** Document the calculated metrics, relate them back to the Wang & Isola / SimCSE papers, and propose specific data-centric or hyperparameter interventions.
7. **Stop and Request Human Review:** **STOP.** Present the findings and proposed strategy to the human for approval before proceeding to the training setup.
