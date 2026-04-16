# Distributed Systems Failure Analyzer (Backend)

## Overview

A structured backend system for diagnosing failures in distributed systems. This project simulates realistic failure scenarios and exposes them through an API interface, enabling programmatic interaction with logs, timelines, and trace data.

The system formalizes failure diagnosis as a reasoning task, where inputs are analyzed and evaluated using a reward-based scoring mechanism.


## Problem Statement

Diagnosing failures in distributed systems presents several challenges:

* Failures propagate across multiple services and obscure root causes
* Observability data is often noisy, incomplete, or misleading
* Root causes are masked by downstream effects
* Manual debugging is slow and inconsistent

This backend provides a controlled and reproducible environment to evaluate automated failure diagnosis approaches.


## Approach

The system models failure diagnosis as a structured interaction:

* **Input:** logs, timelines, and service dependency signals
* **Output:** structured JSON diagnosis
* **Evaluation:** reward-based scoring with partial credit

This enables consistent evaluation and comparison of diagnostic strategies.


## System Design

### Environment

The core environment simulates a multi-service distributed system with:

* Synthetic but realistic log generation
* Configurable error patterns and latency
* Noise injection and misleading signals
* Deterministic scenario execution

Each episode represents a distinct failure scenario.


### Task Levels

The environment supports three levels of difficulty:

**Easy**
Single-service failure with clear error signals.
Objective: identify failing service and error code.

**Medium**
Cascading failures with partial observability.
Objective: identify root service and affected service.

**Hard**
Intermittent or low-signal failures with noisy logs and indirect indicators.
Objective: identify root service, endpoint, failure pattern, and severity.


### Observations

Depending on the task, the system provides:

* Service logs with structured fields
* Event timelines representing causal sequences
* Distributed trace graphs representing service interactions


### Action Space

Clients must submit structured JSON matching the task schema:

* Easy: `service_name`, `error_code`
* Medium: `root_service`, `affected_service`
* Hard: `root_service`, `endpoint`, `failure_pattern`, `severity`

Strict validation ensures consistent and precise inputs.


### Reward Function

Responses are evaluated using a normalized scoring system `[0.0, 1.0]`.

Key properties:

* Partial credit for partially correct answers
* Higher weight for identifying root causes
* Deterministic scoring for reproducibility


## API Access (Hugging Face Deployment)

The backend is deployed on Hugging Face.

Access the live API:

```text
https://aadilparwez-distributed-failure-analyser.hf.space
```

Interactive API documentation:

```text
https://aadilparwez-distributed-failure-analyser.hf.space/docs
```


## API Endpoints

### `GET /`

Returns system metadata, available tasks, and status.

---

### `POST /reset`

Initializes a new failure scenario.

**Request:**

```json
{
  "task": "easy"
}
```

**Response:**
Returns the initial observation (logs, and optionally timeline/trace data).

---

### `POST /step`

Submits a diagnosis and receives evaluation.

**Example (hard task):**

```json
{
  "root_service": "search-api",
  "endpoint": "/api/v1/query",
  "failure_pattern": "connection_pool_exhausted",
  "severity": "high"
}
```

**Response:**

* Observation
* Score (0.0 – 1.0)
* Feedback
* Completion status

---

### `GET /state`

Returns current environment state:

* current task
* step count
* score
* completion status
* episode ID

---

## Project Structure

```
failure_analyzer_backend/
├── models.py
├── environment.py
│── app.py
├── inference.py
├── requirements.txt
└── README.md
```

---

## Key Features

* Simulation of realistic distributed system failures
* Multi-level difficulty with increasing ambiguity
* Deterministic and reproducible evaluation
* Strict schema validation for inputs
* API-first design for integration

---

## Use Cases

* Benchmarking automated failure diagnosis systems
* Integration with LLM-based agents
* Programmatic testing of debugging strategies
* Building intelligent observability tools

---

## Limitations

* Uses synthetic data instead of production logs
* Limited predefined scenarios
* LLM integration depends on external models

---

## Future Work

* Integration with real observability pipelines
* Expanded failure scenario generation
* Learning-based diagnostic agents
* Real-time streaming simulation

---

## Conclusion

This backend provides a structured, reproducible framework for evaluating failure diagnosis in distributed systems. By combining simulation, constrained outputs, and reward-based evaluation, it enables systematic development of automated debugging systems.
