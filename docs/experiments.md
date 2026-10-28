# Experiments

## Setup

Python 3.12.7, macOS 14.6.1, Apple M1 Pro (16 GB), batch size 64, momentum 0.9. W&B project `piussunday-dev/FED-NIDS Federated`.

| Item | Run A (`gsunfh9x`) | Run B (`xjbhw3rt`) |
|---|---|---|
| Clients | 20 | 100 |
| Rounds | 7 | 75 |
| Local epochs | 10 | 3 |
| Learning rate | 0.001 | 0.01 |
| Pretrain epochs | 10 | 1 |
| Stored fit / eval fields | 10 / 5 | 15 / 50 |
| Received fit results, final round | 20 | 15 |
| Distributed eval, final round | 20 results | skipped |

Stored participation fields are not the number of participants per round (Run A round 1: 17 fit results, 3 failures).

## Run A (main result)

Accuracy 12.50% (init) → 60.46% (r1) → 83.46% (r2) → **88.09% (r7)**. Aggregate precision 0.9086, recall 0.8809, F1 0.8784. Per-class table: `results/run_A_round7_class_report.csv`.

Weak points: PartOfAHorizontalPortScan recall 0.45 (F1 0.61); Benign precision 0.59 (recall 0.95). Confusion-matrix counts were not retained, so the source of benign false positives is unknown.

## Run B (convergence observation)

Peak **83.45% at round 37**, final **62.10% at round 75** (21.36 pt drop). Cause not isolated (learning rate, partitioning, optimizer behaviour all possible). Selected rounds: `results/run_B_selected_rounds.csv`.

## Timing

Run A: 8,448.50 s (Flower summary), 8,900.49 s (logged training time), 8,912.77 s (run runtime); different instrumentation scopes. Run B: "Total Training Time" 0.912 s is the sum of 75 short callback timings (about 0.0122 s each); run-summary runtime is 15,466.86 s.

## Centralized references (thesis)

Gradient Boosting 98.6%, Random Forest 97.7%, Decision Tree 97.2%, KNN 86.7%; autoencoder-paired neural models 84.9–85.9%. Partitions are not linked to Runs A/B, so these do not measure the cost or benefit of federation. See `results/centralized_reference.csv`.

## Discrepancy to be aware of

The thesis reports learning rate 0.0001 and the Adam optimizer (Table 14). The recorded W&B configurations for the two runs show learning rate 0.001 / 0.01 and no explicit optimizer name. The paper follows the recorded runs. This repo does the same.

## Limitations and future work

Single runs, no seeds or confidence intervals; no ablations of the bio-inspired modules; no measured privacy guarantee; no live-traffic latency, energy, explainability or poisoning-robustness tests; no AUROC (score arrays not retained). Future: matched FL baselines, training-only transformation fitting, validation-based selection, seeded repetitions.
