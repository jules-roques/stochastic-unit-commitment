# Stochastic Unit Commitment

This repository contains a Julia Jupyter notebook (`.ipynb`) for a stochastic optimization model that addresses a unit commitment problem in power generation.

The primary goal of this project is to improve upon a simple deterministic model by incorporating demand uncertainty. It formulates a **two-stage stochastic optimization model** to minimize the total expected operational costs.

## How to Run This Notebook

This project is packaged as a Julia environment, which guarantees you will use the exact package versions from when the code was written.

**1. Clone the Repository**

```bash
> git clone https://github.com/your-username/your-repo.git
> cd your-repo
```

**2. Install the Environment**

- Start Julia in the repository folder:
```bash
> julia
```

- Press `]` to enter the package manager. Then, **activate** the local environment and **instantiate** it. This will automatically install all the correct packages listed in the `Manifest.toml` file.
```julia
(@v1.10) pkg> activate .
(your-repo) pkg> instantiate
```

**3. Run Jupyter**
Once instantiation is complete, press `Backspace` to exit the package manager. Then, load `IJulia` and run the notebook server:

```julia
julia> using IJulia
julia> notebook()
```

Jupyter will open in your web browser. You can then select `notebook.ipynb` from the list to open and run the notebook. The kernel will automatically have access to all the installed packages.

## Project Overview

For visuals, one may refer to the [presentation slides](/presentation_slides.pdf).

This project tackles a unit commitment problem, which involves deciding how many power generators to turn on in advance (a "here-and-now" decision) to meet a fluctuating and uncertain energy demand at the minimum cost.

A **two-stage stochastic optimization model** is used for this purpose. This is a framework for making decisions under uncertainty, broken into two steps:

1.  **First Stage:** Make initial decisions *before* the uncertainty is revealed. Here, we decide the number of generators to turn on ($n_{i,t}$) and start up ($s_{i,t}$) for each time period.
2.  **Second Stage:** After the uncertain outcome (the specific demand scenario $\xi$) is known, make "recourse" decisions. Here, we set the actual power output ($x_{i,t}^{\xi}$) for the generators to meet that specific demand.

We use this model because it is more realistic than a simple deterministic model. It finds a single first-stage plan that is cost-effective on average across *all* possible demand scenarios, rather than just optimizing for a single, average-demand forecast.

### 1. Methods Used

  * **Dataset:** A synthetic dataset of 500 weeks was generated. Demand is sampled from a Normal distribution $D_{t}\sim\mathcal{N}(\mu(t),\sigma^{2})$, where the mean demand $\mu(t)$ follows a daily sinusoidal pattern.
  * **Scenario Reduction:** To make the problem computationally tractable, the large dataset was reduced using **K-Means** and **Hierarchical Clustering (with Ward's distance)**.
  * **Analysis:** The Stochastic Program (SP) solution was benchmarked against the **Wait-and-See (WS)** and **Expected Value (EV)** solutions. We also ran **in-sample** and **out-of-sample** stability tests to find an optimal number of scenarios ($k$).

### 2. Main Results

  * **Optimal Scenario Count:** Stability analysis pointed to **$k=20$ scenarios** as a good balance between accuracy and computational load.
  * **Clustering Comparison:** Hierarchical clustering produced slightly better (lower cost) objective values for all solutions (SP, WS, and EV) compared to K-Means.
  * **Value of Stochastic Solution (VSS):** The stochastic (SP) model showed clear economic benefit, saving **between €500 and €2,500 per day** compared to the simpler deterministic (EV) model.
  * **First-Stage Decisions:** Both clustering methods resulted in very similar first-stage decisions for which generators to turn on.

### 3. Limits & Future Work

  * **Model Sensitivity:** The solution's stability was found to be sensitive to changes in input parameters, such as the total number of available generators.
  * **Future Work:** Key areas for improvement include testing the model on **real-world datasets**, making it more **tolerant of infeasible solutions**, and exploring **Sample Average Approximation (SAA)** more formally.