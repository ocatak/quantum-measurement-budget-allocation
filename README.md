# Measurement-Budget Allocation in Quantum Learning

Code and experiments accompanying the paper:

> **Measurement-Budget Allocation in Quantum Learning with Finite-Shot Generalization Guarantees**
> F. Ozgur Catak, University of Stavanger.

This repository reproduces the finite-shot Rademacher generalization bound (Theorem 3.1) and the closed-form measurement-budget allocation rule (Theorem 3.2), and runs the five experiments reported in the paper.

## What this implements

For binary quantum classifiers over the full measurement class `H_POVM = {M : 0 ≤ M ≤ I}`, the score is the Born probability `Tr(Mρ)`, estimated from `S` finite shots. Under a fixed total budget `B = nS`, the bound separates a finite-sample term and a finite-shot term:

```
R_ℓ(M) ≤ R̂_{n,S,ℓ}(M) + 2L·√(d/n) + L·√(log(4n/δ)/(2S)) + √(log(2/δ)/(2n))
```

Minimizing a conservative surrogate over `n` gives the allocation rule

```
n* = 2·√(2dB / log(2B/δ)),   S* = B / n*
```

with worst-case rate `O(B^(−1/4))` at the surrogate optimum.

## Experiments

| Notebook section | Experiment | Description |
|---|---|---|
| Exp 0 | Direct H_POVM Rademacher check | Empirical Rademacher complexity via the positive-eigenvalue supremum (proof of Proposition 3.1); verifies estimates stay below `√(d/n)`. |
| Exp 1 | Risk gap vs. `n` | Fixed `S = 100`, sweep `n ∈ {10, 20, 40, 80, 160}`. |
| Exp 2 | Risk gap vs. shots `S` | Fixed `n = 80`, sweep `S ∈ {10, 25, 50, 100, 250, 500}`. |
| Exp 3 | Fixed-budget allocation | `B ∈ {500, 1000, 2000}`, sweep `n` on a log grid with `S = ⌊B/n⌋`. |
| Exp 4 | Tightness / slack | Bound-to-gap ratios at the predicted allocation `(n*, S*)`. |

All VQC experiments use 2-qubit (`d = 4`) and 4-qubit (`d = 16`) circuits with RY angle embedding and `BasicEntanglerLayers`, trained on the rescaled 1-Lipschitz hinge loss across nine 2D synthetic benchmarks (three two-moons, two concentric-circles, two Gaussian, two blobs).

> **Note on scope.** The VQC experiments are consistency checks, not tightness demonstrations: the trained circuits are a restricted subclass of `H_POVM` on structured data, so empirical gaps lie well below the distribution-free worst-case bound. See Section 4 / 5 of the paper.

## Requirements

```
python >= 3.10
numpy
scipy
pandas
scikit-learn
matplotlib
pennylane          # lightning.qubit preferred; falls back to default.qubit
joblib
tqdm
```

Install:

```bash
pip install numpy scipy pandas scikit-learn matplotlib pennylane pennylane-lightning joblib tqdm
```

## Usage

Open and run the notebook top to bottom:

```bash
jupyter notebook quantum_rademacher_finite_shot_experiments.ipynb
```

Run-mode flags are set in the first code cell:

- `FAST_MODE = True` (default): reduced seed counts, suitable for a full reproduction in reasonable time.
- `SMOKE_TEST = True`: minimal grid for a quick end-to-end sanity check.
- `FAST_MODE = False` and `SMOKE_TEST = False`: full settings used for the paper figures.

Outputs are written to `figures/`, `tables/`, and `results/`. The global seed is `GLOBAL_SEED = 42` and the confidence parameter is `δ = 0.05`.

## Reproducibility

The notebook prints the exact versions of numpy, pennylane, pandas, matplotlib, joblib, scikit-learn, tqdm, and scipy at runtime, along with the active PennyLane device. Random seeds are fixed; per-dataset seeds are derived deterministically.

## Citation

```bibtex
@article{catak_measurement_budget_quantum,
  title  = {Measurement-Budget Allocation in Quantum Learning with Finite-Shot Generalization Guarantees},
  author = {Catak, F. Ozgur},
  year   = {2026},
  note   = {Preprint}
}
```

## License

Released under the MIT License. See [LICENSE](LICENSE).
