# Measurement-Budget Allocation in Quantum Learning

Code, generated data, and figures for the paper:

> **Measurement-Budget Allocation in Quantum Learning with Finite-Shot Generalization Guarantees**
> F. Ozgur Catak, University of Stavanger.
> arXiv: [2608.24891](https://arxiv.org/abs/2608.24891)

The repository implements the finite-shot generalization bound (Theorem 3.1), the closed-form measurement-budget allocation rule (Theorem 3.2), and the six experiments (Experiments 0–5) reported in the paper.

## Setting

A binary quantum classifier is a measurement operator `M` with `0 ≤ M ≤ I`. Its score on a state `ρ` is the Born probability `p_M(ρ) = Tr(Mρ)`, which is estimated from `S` binary measurement outcomes (shots). With `n` training states and `S` shots per state, the measurement budget is `B = nS`.

For a loss `ℓ: [0,1] × {0,1} → [0,1]` that is `L`-Lipschitz in the score, and for any `M` selected independently of the shot outcomes used to estimate its empirical risk, with probability at least `1 − δ`:

```
R_ℓ(M) ≤ R̂_{n,S,ℓ}(M) + 2L·√(d/n) + L·√(log(4n/δ)/(2S)) + √(log(2/δ)/(2n))
```

`M` may depend on the training states and labels (the sample term holds uniformly over the class). Minimizing a conservative surrogate of the bound under `B = nS` gives

```
n* = 2·√(2dB / log(4B/δ)),   S* = B / n*
```

which does not depend on `L`.

## Protocol

All VQC experiments follow a two-stage protocol:

1. **Training.** The circuit parameters are optimized with gradient descent on a squared-hinge surrogate loss using exact (noiseless) simulator probabilities. No shots are used during training.
2. **Evaluation.** The empirical risk of the trained circuit is estimated from `S` fresh shots on each of the `n` training states. The population risk is approximated by the exact score risk on a held-out test set of 500 states.

The evaluation loss is the absolute score loss `ℓ(p, y) = |p − y|` (`L = 1`), which equals the rescaled hinge loss on `[0, 1]`. Experiment 5 additionally uses the concave score loss `ℓ_cq(p, y) = 1 − (1 − |p − y|)²` (`L = 2`).

Circuits: 2 qubits (`d = 4`) and 4 qubits (`d = 16`), `RY` angle embedding of the two input features on the first two qubits, two `BasicEntanglerLayers`, read-out of qubit 0. Datasets: nine 2D synthetic benchmarks (three two-moons, two concentric-circles, two Gaussian, two blobs).

## Experiments

| Notebook section | Experiment | Paper | Description |
|---|---|---|---|
| 4 | Exp 0 | Fig. 3 | Empirical Rademacher complexity of the full measurement class via the positive-eigenvalue formula; checks that estimates stay below `√(d/n)`. |
| 10 | Exp 1 | Fig. 4, Table 7 | Gap vs. `n` with `S = 100`, `n ∈ {10, 20, 40, 80, 160}`. |
| 11 | Exp 2 | Fig. 5 | Gap vs. `S` with `n = 80`, `S ∈ {10, 25, 50, 100, 250, 500}`. |
| 12 | Exp 3 | Fig. 6 | Fixed budget `B ∈ {500, 1000, 2000}`, `n` on a log grid, `S = ⌊B/n⌋`. |
| 13 | Exp 4 | Table 8 | Bound-to-gap ratios near the predicted allocation `(n*, S*)`. |
| 16 | Exp 5 | Fig. 7, Table 9 | Fixed-budget sweep with the concave loss; shows the U-shaped trade-off in the empirical gap and compares it with the absolute loss on the same circuits. |

Figure and table numbers refer to the revised version of the paper. arXiv version 1 (June 2026) contains Experiments 0–4 only; Experiment 5 and the `log(4B/δ)` form of the allocation rule were added in the revision.

Note: Experiments 1–4 were run with the allocation `n* = 2·√(2dB / log(2B/δ))` (function `n_star_approx`). The paper uses `log(4B/δ)` (function `n_star_revised`), which gives values about 3% smaller. All reported bound values use the exact constants of Theorem 3.1.

## Repository contents

```
quantum_rademacher_finite_shot_experiments.ipynb   all experiments
results/   per-configuration results (CSV) for Experiments 0-5
tables/    summary tables (CSV)
figures/   figures (PDF and PNG)
requirements.txt
```

Main output files:

| File | Content |
|---|---|
| `results/hpovm_rademacher_results.csv` | Experiment 0 |
| `results/exp1_score_gap_vs_n.csv` | Experiment 1 |
| `results/exp2_score_gap_vs_S.csv` | Experiment 2 |
| `results/exp3_fixed_budget.csv` | Experiment 3 |
| `results/exp4_tightness_at_allocation.csv`, `tables/exp4_tightness_summary.csv` | Experiment 4 |
| `results/exp5_concave_ushape_raw.csv` | Experiment 5, one row per (dataset, d, seed, n, B, loss) |
| `results/exp5_concave_ushape_summary.csv` | Experiment 5, averaged over seeds and datasets |
| `tables/exp5_empirical_minimizer.csv` | Experiment 5, location of the empirical minimum vs. `n*` |
| `results/all_vqc_results_combined.csv` | Experiments 1–4 combined |

## Installation

```bash
pip install -r requirements.txt
```

The results were produced with Python 3.10 and the versions pinned in `requirements.txt`. The notebook uses `lightning.qubit` and falls back to `default.qubit` if it is not available.

## Usage

```bash
jupyter notebook quantum_rademacher_finite_shot_experiments.ipynb
```

Run the cells from top to bottom. Run-mode flags are in the first code cell:

- `FAST_MODE = True` (default): reduced seed counts; this mode was used for the paper.
- `SMOKE_TEST = True`: minimal grid for a quick end-to-end check.
- `FAST_MODE = False` and `SMOKE_TEST = False`: larger seed counts and more training steps.

Experiment 5 depends only on the setup, theory, dataset, model, evaluation, and runner cells (sections 1–9), so it can be run without re-running Experiments 0–4. It uses `joblib` with `EXP5_N_JOBS = 6` parallel workers and took about 28 minutes on an Apple M1 machine.

Outputs are written to `figures/`, `tables/`, and `results/`. The global seed is `GLOBAL_SEED = 42` and the confidence parameter is `δ = 0.05`.

## Citation

```bibtex
@misc{catak2026measurementbudget,
  title         = {Measurement-Budget Allocation in Quantum Learning with Finite-Shot Generalization Guarantees},
  author        = {Catak, Ferhat Ozgur},
  year          = {2026},
  eprint        = {2608.24891},
  archivePrefix = {arXiv},
  primaryClass  = {cs.AI},
  url           = {https://arxiv.org/abs/2608.24891}
}
```

## License

Released under the MIT License. See [LICENSE](LICENSE).
