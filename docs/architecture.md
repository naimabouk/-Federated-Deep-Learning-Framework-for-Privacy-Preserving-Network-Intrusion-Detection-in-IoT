# Architecture

Five layers (see `assets/architecture/fed-nids-detailed.png`):

1. **IoT & edge layer**: devices (cameras, sensors, medical, vehicles, drones) behind edge gateways. Each client keeps raw data, preprocessed data and its local model in private storage.
2. **Data processing layer**: packet/flow capture, cleaning, transformation, feature selection, sliding window. Protocols considered: MQTT, Modbus, gRPC.
3. **Local bio-inspired NIDS**: autoencoder (encoder, latent space, decoder) feeding a deep-learning classifier. Bio-inspired optimization tunes feature subsets, hyperparameters and architecture.
4. **Privacy-preserving federated learning layer**: central server with a federated aggregator (update validation, client management) and the global model. Only model updates (red) go up; the global model (blue) goes down.
5. **Detection & explainability layer**: edge deployment of the global model; benign/attack classification, attack type, risk indicator, alert generation, and a feedback loop into training.

## Operation modes

- **Training mode**: local preprocessing, local training from the global model, send updates only, aggregate, redistribute, optimize.
- **Evaluation mode**: capture live traffic at the gateway, extract features as in training, infer with the global model, alert, feed confirmed intrusions back.

## Privacy interfaces (design intent)

TLS 1.3 transport, differential privacy, secure aggregation, PKI key management. These are architectural interfaces. The reported experiments measure none of them (see README).

## Design objectives

Privacy-preserving collaboration, scalability, resource efficiency, non-IID handling, bio-inspired optimization, real-time detection, threat coverage, low communication overhead, interpretability, fault tolerance.
