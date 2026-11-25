# Explainable Drug–Drug Interaction (DDI) Prediction

An Explainable Deep Learning Framework for Predicting Drug–Drug Interactions Using Graph Embeddings, Molecular Fingerprints, and Physicochemical Descriptors.

## Overview

Drug–Drug Interactions (DDIs) occur when one drug influences the effect, effectiveness, or safety of another drug. Identifying these interactions is essential for improving drug safety and reducing adverse drug reactions.

This project implements an explainable deep learning framework to predict potential interactions between pairs of drugs by integrating three complementary representations:

- **Graph Embeddings:** Capture relational information from the drug interaction network.
- **Morgan Fingerprints:** Represent molecular substructures and chemical patterns.
- **Physicochemical Descriptors:** Capture important chemical properties of drugs.

The proposed Pairwise Neural Network combines these representations to predict whether a given pair of drugs interacts while providing feature-level explanations for individual predictions.

## Key Features

- Drug–Drug Interaction prediction using deep learning.
- Multi-view drug representation learning.
- Graph-based feature extraction using Random Walks and Word2Vec.
- Molecular fingerprint generation using RDKit.
- Integration of physicochemical descriptors.
- Shared neural network encoder for pairwise learning.
- Explainable predictions using Gradient × Input attribution.
- Molecular substructure visualization.
- Coverage-aware model evaluation.

## Proposed Architecture

Each drug is represented using a 358-dimensional feature vector.

| Feature Representation | Dimensions |
|---|---:|
| Graph Embeddings (Word2Vec) | 96 |
| Morgan Fingerprints | 256 |
| Physicochemical Descriptors | 6 |
| **Total** | **358** |

The two drug representations are passed through a shared neural network encoder. Their encoded representations are combined using element-wise absolute difference and concatenation to generate a pairwise representation.

A binary classifier then predicts the probability of an interaction.

**Classification Threshold:** 0.50

## Methodology

The complete workflow consists of five major stages.

### 1. Dataset Preparation

- Obtain drug information and known interaction records from DrugBank.
- Extract positive drug pairs.
- Prepare negative drug pairs.
- Create a balanced binary classification dataset.
- Split the dataset into training, validation, and testing sets.

### 2. Graph Representation Learning

- Construct a drug interaction graph using positive training interactions.
- Represent drugs as graph nodes and interactions as edges.
- Generate random walks over the graph.
- Train Word2Vec to obtain 96-dimensional drug embeddings.

### 3. Molecular Feature Extraction

- Process molecular SMILES representations using RDKit.
- Generate 256-bit Morgan fingerprints.
- Calculate six physicochemical descriptors.
- Combine the extracted features into a 358-dimensional representation per drug.

### 4. Pairwise Neural Network

- Encode both drug representations using a shared neural network.
- Calculate the absolute difference between encoded representations.
- Concatenate the pairwise features.
- Predict interaction probabilities using a binary classifier.

### 5. Explainability and Evaluation

- Evaluate predictions using classification metrics.
- Apply Gradient × Input attribution.
- Identify influential molecular fingerprint bits.
- Map important fingerprint bits to molecular substructures.
- Analyze prediction coverage and limitations.

## Dataset Information

The project uses DrugBank drug information and drug–drug interaction records.

| Dataset Property | Value |
|---|---:|
| Positive Pairs | 1,334,100 |
| Negative Pairs | 1,334,100 |
| Total Samples | 2,668,200 |
| Positive Class Label | 1 |
| Negative Class Label | 0 |

### Dataset Split

| Subset | Percentage |
|---|---:|
| Training | 80% |
| Validation | 10% |
| Testing | 10% |

**Negative Sampling:** Negative examples are pairs not listed among the known positive interactions. The dataset contains an equal number of positive and negative pairs. The exact candidate-pair generation, sampling algorithm, random seed, and duplicate-handling procedure should be documented from the preprocessing notebook.

**Data Licensing:** DrugBank is a separately licensed database. Users must comply with the applicable DrugBank licence and usage restrictions. Raw DrugBank data should not be redistributed without appropriate permission.

## Model Training

The proposed Pairwise Neural Network is implemented using PyTorch.

| Parameter | Configuration |
|---|---|
| Optimizer | AdamW |
| Learning Rate | 0.0008 |
| Weight Decay | 0.0001 |
| Batch Size | 8,192 |
| Maximum Epochs | 30 |
| Early Stopping Patience | 6 |
| Learning Rate Scheduler | ReduceLROnPlateau |
| Loss Function | Binary Cross Entropy with Logits |
| Classification Threshold | 0.50 |
| Training Environment | Google Colab |

The best model checkpoint is selected using validation ROC-AUC. The test set is not used for model selection.

## Results

The proposed model demonstrates strong performance on the evaluated dataset.

### Validation Performance

| Metric | Result |
|---|---:|
| Accuracy | 99.44% |
| ROC-AUC | 99.96% |

### Test Performance

| Metric | Result |
|---|---:|
| Accuracy | 98.95% |
| True Positives | 133,171 |
| True Negatives | 10,318 |
| False Positives | 1,295 |
| False Negatives | 222 |

**Evaluation Note:** The reported test confusion matrix covers 145,006 pairs, approximately 54.35% of the complete test set. Therefore, the reported test metrics describe only the evaluated subset and should not be interpreted as performance over all test pairs.

## Explainability

The framework uses Gradient × Input attribution to understand which input features contribute to individual predictions.

The explainability module provides:

- Feature-level attribution scores.
- Relative contribution of graph embeddings, Morgan fingerprints, and molecular descriptors.
- Identification of influential Morgan fingerprint bits.
- Mapping of important fingerprint bits to molecular substructures.
- Molecular visualization of influential structural regions.

These explanations describe the learned behavior of the model and should not be interpreted as experimentally validated biological mechanisms.

## Technology Stack

| Technology | Purpose |
|---|---|
| Python | Core Programming Language |
| PyTorch | Deep Learning |
| RDKit | Molecular Processing |
| Gensim | Word2Vec Embeddings |
| NumPy | Numerical Computation |
| Pandas | Data Processing |
| Scikit-learn | Model Evaluation |
| LightGBM | Comparative Prediction Models |
| Google Colab | Training Environment |

## Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/MuskanMehta-2006/DDI_Prediction.git

cd DDI_Prediction
```

### 2. Install Dependencies

```bash
pip install numpy pandas scikit-learn
pip install torch gensim rdkit lightgbm
```

### 3. Prepare the Dataset

Place the processed dataset in the expected directory:

```text
data/
└── processed/
    └── ddi_full.csv
```

The dataset should contain the drug identifiers and binary interaction labels required by the training pipeline.

### 4. Run the Notebook

Open the validation notebook in Google Colab:

[Open DDI Prediction Validation Notebook](https://colab.research.google.com/drive/1YtmB5LdQFSH3GaGE-8gxzu6pb8l_wa9H)

Run the notebook cells sequentially after configuring the required dataset and feature-file paths.

## Repository Structure

```text
DDI_Prediction/
│
├── 99_4_validation.ipynb
├── data/
│   └── processed/
│       └── ddi_full.csv
├── README.md
└── requirements.txt
```

Additional preprocessing notebooks, feature-generation scripts, model checkpoints, and evaluation artifacts can be organized into dedicated directories as the project develops.

## Limitations and Future Work

- Improve prediction coverage for drugs without available graph embeddings.
- Investigate molecular-feature fallback strategies for uncovered drug pairs.
- Perform drug-disjoint evaluation to assess generalization to unseen drugs.
- Validate predictions using independent external datasets.
- Investigate more robust negative sampling strategies.
- Conduct additional ablation studies to measure the contribution of individual feature representations.
- Improve the interpretability and biological validation of molecular explanations.

## Author

**Muskan Mehta**

Computer Science and Engineering  
National Institute of Technology, Kurukshetra

GitHub: [@MuskanMehta-2006](https://github.com/MuskanMehta-2006)

## Acknowledgements

We acknowledge DrugBank as the source of drug information and interaction records used in this research.

This project is intended for research and educational purposes. Its predictions are not a substitute for professional medical advice or clinical validation.
