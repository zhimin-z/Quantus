# Supported Evaluation Strategies in Quantus

This document identifies which strategies from the **Unified Evaluation Workflow** are natively supported by Quantus in its full installation. A strategy is considered "supported" only if Quantus provides it out-of-the-box once fully installed, without requiring custom module implementation or external library integration beyond what's included in the full installation.

## Methodology

This analysis was conducted by:
1. Reviewing the official Quantus documentation (README, installation guides, getting started guides)
2. Examining the source code in the `quantus` package
3. Analyzing the `pyproject.toml` to understand the full installation scope (`pip install "quantus[full]"`)
4. Verifying capabilities against the package's stated purpose: "A metrics toolkit to evaluate neural network explanations"

The classification follows the strict criterion that a strategy is "supported" only if it's available through the standard full installation without requiring:
- Custom user implementation of core functionality
- Integration with external monitoring tools not included in the installation
- Additional libraries beyond those in the `[full]` installation option

## Phase 0: Provisioning (The Runtime)

### Step A: Harness Installation

**Supported Strategies:**

- ✅ **Strategy 1: Git Clone** - Quantus can be installed from source by cloning the Git repository.
- ✅ **Strategy 2: PyPI Packages** - Quantus is available on PyPI and can be installed via `pip install quantus` or `pip install "quantus[full]"` for the complete installation with all optional dependencies (PyTorch, TensorFlow, Captum, tf-explain, Zennit, Transformers).

**Unsupported Strategies:**

- ❌ **Strategy 3: Node Package** - Quantus is not a JavaScript/Node.js package.
- ❌ **Strategy 4: Binary Packages** - Quantus does not provide standalone executable binaries.
- ❌ **Strategy 5: Container Images** - Quantus does not provide prebuilt Docker or OCI container images.

### Step B: Credential Configuration

**Supported Strategies:**

- ✅ **Strategy 2: Artifact Repository Authentication** - Quantus relies on dependency libraries (PyTorch, TensorFlow, Hugging Face libraries) that support authentication with artifact repositories (Hugging Face Hub, etc.) to download model weights and datasets. This is available through the underlying frameworks when users load models and data.

**Unsupported Strategies:**

- ❌ **Strategy 1: Model API Authentication** - Quantus does not provide native integration for remote model inference via commercial API providers. It focuses on local model evaluation.
- ❌ **Strategy 3: Evaluation Platform Authentication** - Quantus does not provide native integration with evaluation platforms or leaderboard submission APIs.

---

## Phase I: Specification (The Contract)

### Step A: SUT Preparation

**Supported Strategies:**

- ✅ **Strategy 2: Model-in-Process (Local Inference)** - This is Quantus's primary mode of operation. It evaluates parametric models with learned weights running on local or user-controlled infrastructure. Quantus supports:
  - PyTorch models (via `PyTorchModel` interface)
  - TensorFlow models (via `TensorFlowModel` interface)
  - Access to model internals (activations, logits, hidden states)
  - Local inference with models loaded into memory

**Unsupported Strategies:**

- ❌ **Strategy 1: Model-as-a-Service (Remote Inference)** - Quantus does not support evaluating models via remote API endpoints or commercial model providers.
- ❌ **Strategy 3: Non-Parametric Algorithms (Deterministic Computation)** - Quantus is focused on neural network explanations, not on evaluating non-parametric algorithms like ANN indexes or BM25 retrievers.
- ❌ **Strategy 4: Interactive Agents (Sequential Decision-Making)** - Quantus does not support evaluating stateful agents that make sequential decisions over multiple timesteps.

### Step B: Benchmark Preparation (Inputs)

**Supported Strategies:**

- ✅ **Strategy 1: Benchmark Data Preparation (Offline)** - Quantus accepts predefined datasets as NumPy arrays for evaluation. Users can load and prepare their own benchmark datasets, and Quantus processes them in batch mode.

**Unsupported Strategies:**

- ❌ **Strategy 2: Synthetic Data Generation (Generative)** - While Quantus uses perturbation functions internally for metric computation, it does not provide a dedicated test data generation framework for creating synthetic evaluation datasets.
- ❌ **Strategy 3: Simulation Environment Setup (Simulated)** - Quantus does not support initializing interactive simulation environments for agent evaluation.
- ❌ **Strategy 4: Production Traffic Sampling (Online)** - Quantus does not support sampling or processing real-world production inference traffic.

### Step C: Benchmark Preparation (References)

**Supported Strategies:**

- ✅ **Strategy 1: Ground Truth Preparation** - Quantus accepts and processes ground truth reference materials for evaluation:
  - Segmentation masks for localization metrics
  - Model attribution saliency maps (explanations)
  - Reference explanations for comparison
  - Human annotations (when provided as input)

**Unsupported Strategies:**

- ❌ **Strategy 2: Judge Preparation** - Quantus does not provide functionality for training or loading specialized judge models (LLM-as-a-judge, reward models, etc.) for subjective evaluation.

---

## Phase II: Execution (The Run)

### Step A: SUT Invocation

**Supported Strategies:**

- ✅ **Strategy 1: Batch Inference** - This is Quantus's core execution mode. It processes multiple input samples through a single model instance, computing explanations and evaluating them using various metrics.

**Unsupported Strategies:**

- ❌ **Strategy 2: Arena Battle** - Quantus does not support executing the same input across multiple models simultaneously for paired comparison.
- ❌ **Strategy 3: Interactive Loop** - Quantus does not support stateful stepping through state transitions or interactive agent evaluation.
- ❌ **Strategy 4: Production Streaming** - Quantus does not support continuous processing of live production traffic.

---

## Phase III: Assessment (The Score)

### Step A: Individual Scoring

**Supported Strategies:**

- ✅ **Strategy 1: Deterministic Measurement** - Quantus provides extensive support for deterministic metrics including:
  - Distance metrics (edit distance, geometric distance)
  - Correlation metrics (Spearman, Pearson, Kendall)
  - Complexity metrics (entropy, sparseness)
  - Faithfulness metrics (pixel flipping, region perturbation, ROAD, etc.)
  - Axiomatic metrics (completeness, input invariance)

**Unsupported Strategies:**

- ❌ **Strategy 2: Embedding Measurement** - While Quantus can work with models that produce embeddings, it does not provide native embedding-based similarity metrics (e.g., semantic similarity using sentence embeddings, BERTScore). Users would need to compute these externally.
- ❌ **Strategy 3: Subjective Measurement** - Quantus does not provide LLM-based or model-based judgment capabilities for subjective quality assessment.
- ❌ **Strategy 4: Performance Measurement** - Quantus does not measure resource consumption metrics like latency, throughput, memory usage, FLOPs, or energy consumption.

### Step B: Aggregate Scoring

**Supported Strategies:**

- ✅ **Strategy 1: Distributional Statistics** - Quantus supports aggregating instance-level scores into benchmark-level metrics through:
  - Statistical aggregation (mean, median, quantiles)
  - Custom aggregation functions (user-provided `agg_func`)
  - The `quantus.evaluate()` function for large-scale evaluation
  - Per-metric aggregation options

**Unsupported Strategies:**

- ❌ **Strategy 2: Uncertainty Quantification** - Quantus does not provide built-in functionality for estimating confidence bounds around aggregate metrics using bootstrap resampling or Prediction-Powered Inference (PPI).

---

## Phase IV: Reporting (The Output)

### Step A: Insight Presentation

**Supported Strategies:**

None of the reporting strategies are natively supported by Quantus in its full installation.

**Partially Supported Strategies:**

- ⚠️ **Strategy 4: Chart Generation** - Quantus provides limited plotting utilities through `quantus.helpers.plotting` for specific metric visualizations (pixel flipping curves, selectivity plots, model parameter randomisation plots, etc.). However, these are experiment-specific helpers rather than a comprehensive chart generation system for multi-dimensional quality profiles, drift histograms, or general performance trend plots as described in the unified workflow.

**Unsupported Strategies:**

- ❌ **Strategy 1: Execution Tracing** - Quantus does not capture or display detailed step-by-step execution logs or intermediate computational states.
- ❌ **Strategy 2: Subgroup Analysis** - Quantus does not provide built-in functionality for stratified performance analysis by demographic groups, domains, or task categories.
- ❌ **Strategy 3: Regression Alerting** - Quantus does not provide automated comparison against historical baselines or performance degradation alerts.
- ❌ **Strategy 5: Dashboard Creation** - Quantus does not provide interactive web interfaces or dashboards.
- ❌ **Strategy 6: Leaderboard Publication** - Quantus does not provide integration with public or private leaderboard systems.

---

## Summary

Quantus is a specialized evaluation harness focused on **quantitative evaluation of neural network explanations**. Its core strength lies in:

1. **Local model evaluation** (PyTorch and TensorFlow)
2. **Batch processing** of explanation quality metrics
3. **Deterministic measurement** of explanation properties across six categories (Faithfulness, Robustness, Localization, Complexity, Randomisation, Axiomatic)
4. **Ground truth-based evaluation** using pre-computed reference materials

**Total Supported Strategies: 8 fully supported, 1 partially supported (9 total out of 38)**

### By Phase:
- **Phase 0 (Provisioning):** 3/8 strategies supported
- **Phase I (Specification):** 3/6 strategies supported
- **Phase II (Execution):** 1/4 strategies supported
- **Phase III (Assessment):** 2/4 strategies supported
- **Phase IV (Reporting):** 0/6 fully supported, 1/6 partially supported

Quantus is **not** a general-purpose evaluation harness for all ML modalities. It is specifically designed for evaluating explanation methods (XAI) applied to neural networks, with a focus on computer vision models, though it also supports tabular and time-series data.
