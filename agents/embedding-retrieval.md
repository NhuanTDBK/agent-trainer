# Auto-Researcher: Semantic Search Embedding Model

This guide defines the interactive process for the LLM to research, experiment, and optimize a Semantic Search embedding model using `SentenceTransformer`. 

The primary goal is to **optimize performance accuracy** (e.g., Information Retrieval metrics like NDCG@10, MAP@100, and Binary Classification accuracy).

## Phase 1: Pre-Training Analysis & Strategy

Before writing any training loops, the LLM and the human must establish a solid foundation.

1. **Dataset Analysis:** The LLM will analyze the provided dataset structure, query/passage lengths, and label distribution. 
2. **Methodology Comparison:** The LLM will compare fine-tuning methods suitable for the dataset (e.g., MultipleNegativesRankingLoss vs. MatryoshkaLoss, need for hard negatives, etc.).
3. **Scale-Up Strategy:** The LLM will formulate a plan to run small, fast iterations first (e.g., using a subset of data or fewer epochs) to validate the pipeline before executing full-scale, long-running epochs.
4. **Human Review:** **STOP.** The LLM must present this analysis and strategy to the human for approval before proceeding to setup.

## Phase 2: Setup & Baseline

To set up a new experiment, work with the user to:

1. **Establish Notebook Versioning:** Instead of Git branches, agree on a notebook naming convention (e.g., `experiment_v1_baseline.ipynb`). All code modifications happen within the notebook cells.
2. **Read the Context:** Understand the existing `train_sentence_transformer` function, evaluation harnesses, and MLflow logging setup.
3. **Initialize `RESULTS.md`:** Create or verify `RESULTS.md`. This file will serve as the master log for all hyperparameter configurations, metrics, and brainstorming notes.
4. **Run the Baseline:** Execute the first run using default parameters on a *small subset* of the data. 
5. **Human Review:** **STOP.** Present the baseline metrics to the human before beginning the optimization loop.

## Phase 3: The Optimization Loop

This loop is executed for every new experimental idea. The max running time for any single full-scale experiment is **24 hours** (wall-clock time). 

For every iteration, follow these steps exactly:

### Step 1: Brainstorm & Analyze
- **Analyze the Situation:** Look at the current state of `RESULTS.md`. Which previous runs succeeded? Which failed or overfit?
- **Review Metrics & Hyperparameters:** Compare the hyperparameter combinations (learning rate, batch size, loss functions, Matryoshka dims, etc.) against their respective accuracy scores.
- **Propose a New Solution:** Based on the analysis, formulate a specific hypothesis. (e.g., "Increasing effective batch size to 1024 might improve Multiple Negatives Ranking Loss stability.")

### Step 2: Human Review (CRITICAL)
- **STOP.** Do not run the training code. 
- Present the brainstorming analysis, the proposed hyperparameter changes, and the rationale to the user.
- Ask: *"Do you approve this change for the next notebook run?"*

### Step 3: Execute
- Once approved, duplicate/create a new notebook version (e.g., `experiment_v2_large_batch.ipynb`).
- Apply the changes to the `SentenceTransformerTrainer` arguments, model architecture, or loss functions.
- Run the experiment. Remember to test on a small dataset first if introducing major architectural changes, then scale to the full dataset.
- *Constraint:* Ensure the training job will not exceed the 24-hour limit.

### Step 4: Record & Evolve
When the experiment finishes, extract the metrics (e.g., IR and Binary evaluation results from MLflow or console logs) and append a detailed entry to `RESULTS.md`.

## Output Format: `RESULTS.md`

Maintain `RESULTS.md` using the following Markdown structure to track evolution clearly:

### Run: [Notebook Name / Version]
* **Status:** [Success / Crash / OOM]
* **Brainstorming/Rationale:** [Brief explanation of why this was tried based on previous runs]
* **Hyperparameters:**
    * Model: `[e.g., BAAI/bge-base-en-v1.5]`
    * LR: `[e.g., 2e-5]`
    * Batch Size: `[e.g., 256]`
    * Loss: `[e.g., MatryoshkaLoss + CachedMNRL]`
    * Epochs / Time: `[e.g., 3 epochs / 14 hours]`
* **Metrics:**
    * Binary Accuracy: `[e.g., 0.89]`
    * NDCG@10: `[e.g., 0.76]`
    * MAP@100: `[e.g., 0.71]`
* **Analysis:** [What did we learn from this run? Did the hypothesis hold up?]

---
*Repeat the loop: Analyze -> Propose -> Wait for Approval -> Execute -> Record.*
