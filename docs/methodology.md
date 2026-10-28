# Methodology

## Federated objective

min_θ F(θ) = Σ_k (n_k / Σ_j n_j) · L_k(θ)

With the FedProx option, each client minimises L_k(θ) + (μ/2)‖θ − θ^(t)‖², with μ = 0.1. This is a regulariser, not a hard bound on parameter drift. Implemented as `TimingStrategyFedProx`, an extension of Flower's FedProx that also records aggregation timing.

## Client / server (Flower)

- `NIDSClient` extends `NumPyClient`: `get_parameters`, `set_parameters`, `fit`, `evaluate`.
- `prepare_dataset` partitions training data per client and keeps one shared test set for centralized evaluation.
- Hydra manages configs; Ray simulates clients; W&B tracks metrics.

## Detector

- Encoder: h = σ(Wx + b). Decoder: x̂ = σ(W′h + b′). Loss: reconstruction MSE.
- Stored dimensions: input 15, encoded 32, hidden 64, 8 classes.
- Autoencoder pretraining, then federated classifier training. Autoencoder weights also travel through FL.
- Loss for the classifier: cross-entropy.

## Data pipeline (IoT-23 derivative)

1. Drop non-informative columns (timestamp, unique id).
2. EDA in two stages (full dataset, then subsets).
3. Correlation filtering: removed `ip_bytes_sent_by_source/destination` (redundant with packet counts).
4. Categorical encoding and scaling.
5. Bio-inspired feature selection.
6. SMOTE balancing: 168,627 observations per class in the evaluation report.

Final features: source_port, destination_port, network_protocol, service_type, connection_duration, bytes_sent, bytes_received, connection_state, connection_history, packets_sent, ip_bytes_sent, packets_received, ip_bytes_received (+ label).

Classes: Attack, Benign, C&C, C&C-HeartBeat, DDoS, HeartBeat, Okiru, PartOfAHorizontalPortScan.

## Bio-inspired optimization layer

| Strategy | Inspiration / mechanism | Role |
|---|---|---|
| ASSCO | Social-spider vibration frequency f = (f(x) − f_min)/(f_max − f_min) + C | Feature-subset and parameter search |
| ISSA-HHO | Salp swarm with Harris-hawk exploration, Lévy flight, opposition-based init | Exploration/exploitation balance for parameter search |
| PSO-GA | V ← ωV + c1·r·(Pbest − X) + c2·r·(Gbest − X); X ← X + V; GA crossover/mutation for diversity | Parameter search |
| GWO-ABC | α/β/δ-guided positions X(t+1) = (X1+X2+X3)/3, plus ABC employed/onlooker/scout bees | Architecture and parameter search |

Scope: configuration search only. It does not redefine the aggregation objective. No per-strategy accuracy gain is estimated.

## Non-IID strategies (design, from thesis Sec. 5.3.9)

EMD and Jensen-Shannon divergence for heterogeneity, FedProx, attentive aggregation, clustered FL, personalized FL (MAML), adaptive learning rates, multi-task learning, continual learning (EWC), DP-aware federated SMOTE. Only FedProx is part of the recorded runs.
