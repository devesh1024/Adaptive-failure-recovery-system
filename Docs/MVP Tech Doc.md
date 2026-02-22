## 1. MVP Objective

The MVP (Minimum Viable Product) aims to demonstrate:

> A working prototype that learns to decide whether to execute recovery actions during recurring application failures in a controlled environment.

The MVP will:

* Simulate application failures
* Extract contextual state from logs/metrics
* Implement a baseline rule-based policy
* Implement a learning-based decision policy (Q-learning or DQN)
* Compare outcomes between the two approaches

The MVP will NOT:

* Integrate into production Kubernetes clusters
* Handle multi-service distributed environments
* Perform real-time deployment
* Replace enterprise monitoring tools

---

## 2. MVP Scope Definition

### In Scope

* Single application simulation
* Controlled fault injection
* Limited action space (max 5 actions)
* Single-agent learning
* Simulated reward function
* Offline and semi-online learning

### Out of Scope

* Multi-node orchestration
* Production-level deployment
* Advanced deep RL architectures
* Cross-service correlation
* Large-scale log ingestion pipelines

---

## 3. MVP Architecture (Practical Implementation View)

```id="mvp_arch"
Failure Simulator
        ↓
Metric & Log Generator
        ↓
Feature Extractor
        ↓
State Builder
        ↓
Decision Engine (Baseline + Learning)
        ↓
Action Executor (Simulated)
        ↓
Feedback Evaluator
        ↓
Policy Update
```

---

## 4. Core MVP Components

---

### 4.1 Failure Simulator

Purpose:
Simulate realistic failure scenarios.

Implementation:

* Python-based environment
* Time-stepped simulation
* Inject:

  * Intermittent latency spikes
  * Error bursts
  * Load fluctuations
  * Cascading failure simulation

Example failure modes:

1. Transient failure (self-resolving)
2. Escalating failure
3. Load-induced failure
4. Recovery-sensitive failure

---

### 4.2 Metric & Log Generator

Simulates:

* Error rate
* Latency trend
* CPU usage
* Restart count
* Failure pattern ID

Output format (JSON):

```json
{
  "timestamp": 1023,
  "error_rate": 0.32,
  "latency": 240,
  "cpu": 78,
  "restart_count": 1,
  "failure_type": "transient"
}
```

---

### 4.3 Feature Extraction Module

Transforms raw signals into:

* Rolling window error average
* Latency slope
* Pattern classification
* Recovery history score
* Load classification (low/medium/high)

This reduces state dimensionality.

---

### 4.4 State Builder

Constructs structured state vector:

```python
state = [
    error_rate_window,
    latency_trend,
    failure_pattern_cluster,
    restart_history_score,
    load_level,
    time_since_last_action
]
```

State must remain compact and stable.

---

### 4.5 Decision Engine

Two versions will be implemented:

---

#### 4.5.1 Baseline Rule-Based Policy

Simple threshold logic:

* If error_rate > threshold → restart
* If restart_count > limit → delay
* If latency low → ignore

Purpose:

* Provide comparison benchmark

---

#### 4.5.2 Learning-Based Policy

Option A: Tabular Q-Learning (if state discrete)
Option B: DQN (if state continuous)

Action Space:

1. Ignore
2. Delay
3. Reduce Load
4. Soft Restart
5. Hard Restart

Policy goal:
Maximize cumulative reward.

---

### 4.6 Action Executor (Simulated)

Simulates consequences:

* Restart → resets error rate but increases disruption penalty
* Ignore → may stabilize or escalate
* Reduce load → moderate improvement
* Delay → observe system trend

No real infrastructure calls.

---

### 4.7 Feedback Evaluator

Computes reward:

```python
reward = (
    stability_gain
    - recovery_cost
    - disruption_penalty
    - over_recovery_penalty
)
```

Reward components:

* Stability Gain (+)
* Failure Escalation (-)
* Restart Cost (-)
* Frequent Restart Penalty (-)

---

## 5. Technology Stack (MVP)

### Programming Language

* Python 3.x

### Libraries

* NumPy
* Pandas
* Matplotlib (evaluation plots)
* PyTorch (if DQN implemented)

### Optional

* OpenAI Gym-style environment abstraction
* Scikit-learn (for clustering failure patterns)

---

## 6. Training Strategy

Phase 1:

* Random policy interaction
* Collect experiences

Phase 2:

* Train Q-table or DQN

Phase 3:

* Evaluate against baseline

---

## 7. Evaluation Metrics

The MVP must quantitatively compare:

1. Number of unnecessary restarts
2. Total recovery cost
3. Time to stability
4. Recurrence frequency
5. Cumulative reward

Graphs required:

* Reward vs Episode
* Restart frequency comparison
* Stability time comparison

---

## 8. Experimental Design

Each policy tested on:

* 50 simulated episodes
* Multiple failure modes
* Same random seed (fair comparison)

---

## 9. Deliverables for MVP

* Failure simulator
* Feature extraction module
* Baseline decision policy
* Learning-based policy
* Comparative evaluation report
* Graphical performance comparison

---

## 10. Risks & Mitigation

Risk 1: State explosion
Mitigation: Discretize features.

Risk 2: RL instability
Mitigation: Start with tabular Q-learning.

Risk 3: Reward misdesign
Mitigation: Manual sanity-check simulations.

---

## 11. Success Criteria

MVP considered successful if:

* Learning policy reduces unnecessary recovery by measurable margin
* System demonstrates memory-based improvement
* Sequential decision effect observable
* Baseline vs learning comparison defensible

---

## 12. Future Expansion (Post-MVP)

* Real log ingestion
* Kubernetes API integration
* Multi-service simulation
* Multi-agent policy
* Adaptive reward tuning

---

## 13. Summary

The MVP establishes:

* Controlled experimental environment
* Stateful decision modeling
* Baseline vs learning comparison
* Demonstrable reduction in over-recovery

This ensures feasibility within academic minor project scope while maintaining research depth.
