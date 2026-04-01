## VABAM: Variational Autoencoder for Amplitude-based Biosignal Augmentation within Morphological Shape.

This repository contains the official implementation of VABAM along with the Conditional Mutual Information (CMI)-based evaluation metrics introduced in our research. VABAM enables the generative synthesis of pulsatile physiological signals by decoupling waveform shape from amplitude dynamics. The CMI-based metrics offer a principled, information-theoretic assessment of the model's ability to achieve this decoupling, supporting evaluation of both structural preservation and amplitude controllability.



### Research Highlights

- **Development of the VABAM Model:** A model capable of synthesizing pulsatile physiological signals through cascaded filtering effects, namely *amplitude-based* modulation, ensuring the preservation of the signals' waveform shape.
<p align="center">
  <img src="https://github.com/JunetaeKim/VABAM-official/blob/main/Figures/Anim.%201%20VABAM%20(Our%20Model)%20Synthesis%20Results.gif" width="49%" alt="Pass-filter mechanism">
  <img src="https://github.com/JunetaeKim/VABAM-official/blob/main/Figures/Anim.%202%20C-VAE%20Synthesis%20Results.gif" width="49%" alt="Pass-filter mechanism">
  <br>
  <em>Figure 1: Amplitude-Based Modulation of ABP via VABAM (left) vs CVAE (right) </em>  
</p>
Figure 1 shows the results of synthesizing 100 signals from a single original Arterial Blood Pressure (ABP). VABAM excels in maintaining the original morphology of signals during synthesis by avoiding phase alterations and horizontal shifts in the time axes. Conversely, conditional VAEs struggle to maintain waveform shapes when PSD values are incorporated as conditional input.
<br><br>

- **Introduction of Novel Metrics:** We propose three novel metrics to provide a comprehensive evaluation of the model's synthesis and representation capabilities:
  1. **Waveform Shape Factorization in Latent Z:** Assessing whether latent space disentangles waveform shape features along specific dimensions.
  2. **Waveform Shape Preservation under Conditional Inputs :** Assessing the model's ability to maintain waveform structure while adjusting amplitude.
  3. **Amplitude Modulation Controllability within Fixed Morphologies:** Measuring the model's capability to modulate signal amplitude accurately in accordance with the intended input.
<br>

<p align="center">
  <img src="https://github.com/JunetaeKim/VABAM-official/blob/main/Figures/Schematic%20overview%20for%20assessing%20morphology%E2%80%93amplitude%20decoupling%20quality.png" width="75%" alt="Pass-filter mechanism">
  <br>
  <em>Figure 2: Evaluation pipeline for waveform shape–amplitude decoupling </em>  
</p>
Figure 2 shows a schematic overview of how waveform shape–amplitude decoupling quality is assessed.  
Synthetic waveforms generated from latent shape codes <i>z</i> and amplitude codes <i>θ</i> are transformed into power spectral density (PSD) maps, from which permutation distributions are constructed.
Using these representations, three KLD-based metrics—shape factorization (SF), shape distortion/preservation (SD/SP), and amplitude controllability (AC)—quantify how effectively the model separates and controls waveform shape and amplitude.
<br><br>

 ## A Brief Introduction to VABAM
-VABAM is structured around five key components: Feature Extractor, Encoder, Sampler, Feature Generator, and Signal Reconstructor (Figure 3). For detailed information, please refer to our paper.

- **Feature Extractor** $g_x(\cdot)$ applies cascading filters to the raw signal $y$, producing four amplitude-modulated subsets $x \in \\{x_{2^{\lambda}-1}, x_{2^{\lambda}}, \dots, x_{2^{\lambda+1}-3}, x_{2^{\lambda+1}-2}\\}$ that guide the Feature Generator.

- **Encoder** $g_e(\cdot)$ learns parameters for the latent variable $Z$ and cutoff frequency $\Theta$, under two assumptions:
  - $\theta_k \sim \mathcal{U}(0, 1)$ for $k = 1, \ldots, K$, where $K = \sum_{i=1}^{\lambda} 2^i$ denotes the total number of cascading filters, increasing with depth $\zeta$, approximated by Bernoulli distributions.
  - $z_{j} \sim \mathcal{N}(\mu_{z_j}, \sigma_{z_j}^2)$ for each dimension $j$, with $j \in \\{1, 2, \ldots, J\\}$, where $J$ is a hyperparameter defining dimension count.

- **Sampler** $g_z(\cdot)$ and $g_\theta(\cdot)$ utilizes the reparameterization trick for backpropagation, allowing sampling of $z_{j}$ and $\theta_{k}$ for gradient flow.

- **Feature Generator** $g_{x'}(\cdot)$ generates four principal feature signals for the Signal Reconstructor, aligning with the amplitude-modulated subsets from the Feature Extractor.

- **Signal Reconstructor** $g_y(\cdot)$ reconstructs coherent signals from the feature subsets, keeping the original signal's main aspects and adding latent elements influenced by $z_{j}$ and $\theta_{k}$.

<p align="center">
  <img src="https://github.com/JunetaeKim/VABAM-official
/blob/main/Figures/Overview of the framework and key concepts.png" width="75%" alt="Intuitive Illustration of VABAM">
  
  <br>
  <em> Figure 3: Overview of the framework and key concepts </em>  
</p><br>

## Library Dependencies and Test Environment Information
VABAM's training and its post-evaluation were conducted and tested with the following libraries and their respective versions:
- Python == 3.8.16 , 3.9.18
- numpy == 1.19.5 , 1.26.0
- pandas == 1.1.4 , 2.1.1
- tensorflow == 2.4.0 , 2.10.0
- gpu == rtx3090TI , rtx4080 , rtx4090
> **Note:** Typical install time on a "normal" desktop computer: ~20 minutes (including environment setup and dependency installation).
> 
> **Note:** On NVIDIA 50-series GPUs (e.g., RTX 5090), the scripts may fail to run due to CUDA/cuDNN compatibility issues with the GRU implementation. If this occurs, you can switch to the CPU version, which runs without these GPU-specific compatibility problems.
<br>


## Code Overview and Run Procedure Guide
### For Training
To start the training process, use the following scripts:
- `TrainModel.py` or `TrainModel64.py`: Scripts for training the main model (32-bit or 64-bit).
- `TrainBenchmark.py` or `TrainBenchmark64.py`: Scripts for training the benchmark models (32-bit or 64-bit). Refer to the [Benchmarks](https://github.com/JunetaeKim/VABAM-official/tree/main/Benchmarks) folder for details.


### For CMI-Based Metric Computation
To compute the CMI-based metrics, follow these steps:
- `BatchMIEvaluation.py` (with `BatchMIEvaluation.py`): Script for computing metrics.
- `BatchBMMIEvaluation.py` (with `BatchBMMIEvaluation.py`): Script for computing benchmark model metrics. Refer to the [Benchmarks](https://github.com/JunetaeKim/VABAM-official/tree/main/Benchmarks) folder.
- `TabulatingResults.py`: Script for tabulating results from the main model evaluation.
- `TabulatingBMResults.py`: Script for tabulating results from the benchmark model evaluation. Refer to the [Benchmarks](https://github.com/JunetaeKim/VABAM-official/tree/main/Benchmarks) folder.

For visualization and table generation:
- `VisualizationSig.ipynb`: Jupyter Notebook for signal visualization.
- `VisualizationMetrics.ipynb`: Jupyter Notebook for visualizing evaluation metrics.
- `VisualizationGendist.ipynb`: Jupyter Notebook for spectral responsiveness and distribution visualization.
- `TablesVis.ipynb`: Converts pandas tables into LaTeX code for presentation.
- `Tables.ipynb`: Jupyter Notebook for generating summary tables of experimental results.

> **Note:** Some redundancy may remain in the visualization code, and further updates are expected.

### Execution Order and Dependencies
````
TrainModel
├── BatchMIEvaluation
│   └── TabulatingResults
│       └── Tables
│           ├── TablesVis
│           └── VisualizationMetrics
├── VisualizationGendist
└── VisualizationSig

TrainBenchmark
└── BatchBMMIEvaluation
    └── TabulatingBMResults
        └── Tables (merges with above)
            ├── TablesVis
            └── VisualizationMetrics
````
### Configurations
- Configuration files for the main and benchmark models are located in the [Config](https://github.com/JunetaeKim/VABAM-official/tree/main/Config) and [/Benchmarks
/Config/](https://github.com/JunetaeKim/VABAM-official/tree/main/Benchmarks/Config) folders, respectively.
<br><br>


## Demo Guide (Generation + Visualization Only)
A lightweight demo is available for quick testing of signal generation and visualization.
- **Demo Sample:** `Data/Demo` — small synthetic dataset generated with NeuroKit2
- **Demo Notebook:** [DemoVisualizationSig](https://github.com/JunetaeKim/VABAM-official/blob/main/DemoVisualizationSig.ipynb)

This notebook covers loading the demo data, running the VABAM generation pipeline, and visualizing generated physiological signals and their spectral characteristics.

> **Note:** Demo files contain names like `MIMIC` or `VitalDB` for pipeline compatibility only — they are **not** real records from those sources. The demo dataset is a small synthetic sample, so synthesis and visualization quality may be limited.
>
> **Note:** No training, evaluation, or benchmarking is required for this demo, and on a normal desktop computer it typically completes within about 2 minutes on CPU.

<br>

## Data Guide
### Data Sources
The original study used data from:
- **MIMIC-III Waveform Database**: https://physionet.org/content/mimic3wdb/1.0/
- **VitalDB**: https://vitaldb.net/

> **Note:** MIMIC-III Waveform Database is openly available under the ODC Open Database License (ODbL) v1.0.
> 
> **Note:** VitalDB is subject to the provider's terms of use (registration agreement: https://vitaldb.net/registration-agreement/).

### Processed MIMIC-III Dataset
The processed MIMIC-III Waveform dataset used in this study is publicly archived on Zenodo:
- **DOI**: https://doi.org/10.5281/zenodo.19354579

> ⚠️ The dataset is currently under embargo and will be publicly available upon acceptance of the associated paper.

### Synthetic Dataset
For users without access to the original data, synthetic data generated with NeuroKit2 are provided as a substitute.
- [`Data/Demo`](https://github.com/JunetaeKim/VABAM-official/tree/main/Data/Demo) — small sample for the demo above
- A larger synthetic dataset can be generated locally by running the following notebooks **in order** from the [`./Data`](https://github.com/JunetaeKim/VABAM-official/tree/main/Data) folder:
  1. [`Demodataset.ipynb`](https://github.com/JunetaeKim/VABAM-official/blob/main/Data/Demodataset.ipynb) — generates synthetic signals (dataset size is configurable within this notebook)
  2. [`DemoProcessing_mu_law_encode_sampling.ipynb`](https://github.com/JunetaeKim/VABAM-official/blob/main/Data/DemoProcessing_mu_law_encode_sampling.ipynb) — processes and encodes the signals for WaveNet-based benchmark models; **this step can be skipped if you are not running WaveNet-based benchmarks**

  Output files will be saved to [`./Data/ProcessedData`](https://github.com/JunetaeKim/VABAM-official/tree/main/Data/ProcessedData).

  Four reference files (`Mimic3SigMax`, `Mimic3SigMin`, `VitalDBSigMax`, `VitalDBSigMin`) are pre-provided in the repository — these contain **arbitrary placeholder values only and do not represent any real data**.

</p><br>


## Scripts Executed for Our Research
All execution code lists are available in the [ExecutionProcedure.txt](https://github.com/JunetaeKim/VABAM-official/blob/main/ExecutionProcedure.txt) file; please refer to this file for detailed information.

### 1.TrainModel.py 

**VABAM Training Commands:**
python TrainModel.py --Config [model_config] --GPUID [gpu_id]

- **VABAM_ART_Mimic Examples:**
  - `python TrainModel.py --Config FC_ART_1_50_800_Mimic --GPUID 0`
  - `python TrainModel.py --Config SKZ_ART_1_50_800_Mimic --GPUID 0`
  - `python TrainModel.py --Config SKZFC_ART_1_30_500_Mimic --GPUID 0`
  - `python TrainModel.py --Config SKZFC_ART_1_30_800_Mimic --GPUID 0`
  - `python TrainModel.py --Config SKZFC_ART_1_50_800_Mimic --GPUID 0`
  - `python TrainModel.py --Config SKZFC_ART_2_30_500_Mimic --GPUID 0`
  - `python TrainModel.py --Config SKZFC_ART_2_30_800_Mimic --GPUID 0`
  - `python TrainModel.py --Config SKZFC_ART_2_50_800_Mimic --GPUID 0`

- **VABAM_ART_VitalDB:**
  - `python TrainModel.py --Config FC_ART_1_50_800_VitalDB --GPUID 0`
  - ...
  - `python TrainModel.py --Config SKZFC_ART_2_50_800_VitalDB --GPUID 0`

- **VABAM_II_Mimic:**
  - `python TrainModel.py --Config FC_II_1_50_800_Mimic --GPUID 0`
  - ...
  - `python TrainModel.py --Config SKZFC_II_2_50_800_Mimic --GPUID 0`

- **VABAM_II_VitalDB:**
  - `python TrainModel.py --Config FC_II_1_50_800_VitalDB --GPUID 0`
  - ...
  - `python TrainModel.py --Config SKZFC_II_2_50_800_VitalDB --GPUID 0`
<br>

**Benchmark Model Training Commands:**
python TrainBenchmark.py --Config [model_config] --GPUID [gpu_id]

- **Benchmark_ART_Mimic Examples:**
  - `python TrainBenchmark.py --Config Wavenet_ART_Mimic --GPUID 0`
  - ...
  - `python TrainBenchmark.py --Config TCVAE_ART_30_Mimic --GPUID 0`

- **Benchmark_ART_VitalDB Examples:**
  - `python TrainBenchmark.py --Config Wavenet_ART_VitalDB --GPUID 0`
  - ...
  - `python TrainBenchmark.py --Config TCVAE_ART_30_VitalDB --GPUID 0`
 
- **Benchmark_II_Mimic Examples:**
  - `python TrainBenchmark.py --Config Wavenet_II_Mimic --GPUID 0`
  - ...
  - `python TrainBenchmark.py --Config TCVAE_II_30_Mimic --GPUID 0`
 
- **Benchmark_ART_VitalDB Examples:**
  - `python TrainBenchmark.py --Config Wavenet_II_VitalDB --GPUID 0`
  - ...
  - `python TrainBenchmark.py --Config TCVAE_II_30_VitalDB --GPUID 0`
<br><br>

### 2. BatchBMMIEvaluation.py
**VABAM Evaluation Commands:**
python BatchMIEvaluation.py --Config [eval_config] --GPUID [gpu_id] --ConfigSpec [model_spec]

- **VABAM Examples:**
  - `python .\BatchMIEvaluation.py --Config EvalConfigMimic --GPUID 0`
  - `python .\BatchMIEvaluation.py --Config EvalConfigVitalDB --GPUID 0`
<br>

**Benchmark Model Evaluation Commands:**
python BatchBMMIEvaluation.py --Config [eval_config] --GPUID [gpu_id] --ConfigSpec [model_spec] 

- **Benchmark Examples:**
  - `python .\BatchBMMIEvaluation.py --Config EvalConfigART_VAE --GPUID 0`
  - ...
  - `python .\BatchBMMIEvaluation.py --Config EvalConfigII_Other --GPUID 0`
<br><br>

### 3. TabulatingResults.py
**VABAM Evaluation Commands:**
python TabulatingResults.py -CP [config_path] --GPUID [gpu_id]

- **VABAM Example:**
  - `python .\TabulatingResults.py -CP ./Config/ --GPUID 0`
<br>

**Benchmark Model Evaluation Commands:**
python TabulatingBMResults.py -CP [config_path] --GPUID [gpu_id]

- **Benchmark Example:**
  - `python .\TabulatingBMResults.py -CP ./Config/ --GPUID 0`
<br><br>


## License
This project is licensed under the MIT License.  
See the [LICENSE](./LICENSE) file for details.
