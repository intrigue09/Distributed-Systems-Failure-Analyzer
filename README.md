 # Distributed Systems Failure Analyzer

## Overview

A reproducible environment for diagnosing failures in distributed systems. The project simulates realistic failure scenarios, provides structured inputs (logs, traces, timelines), and evaluates agent diagnoses using a reward-based scoring system.

The objective is to treat debugging as a structured reasoning task that can be measured, compared, and improved.


## Problem Statement

Production distributed systems present significant diagnostic challenges:

* Failures propagate across multiple services and obscure root causes
* Observability data is often noisy, incomplete, or misleading
* Root causes are masked by downstream effects
* Manual debugging is time-consuming and inconsistent

This project provides a controlled framework for evaluating and improving automated failure diagnosis.


## Approach

The system models diagnosis as a structured interaction:

* **Input:** synthetic logs, event timelines, and service dependency graphs
* **Output:** constrained JSON diagnosis following strict schemas
* **Evaluation:** reward-based scoring with partial credit and root-cause emphasis

This enables repeatable evaluation and direct comparison of different agents or strategies.


## System Design

### Environment

The environment simulates a multi-service system with configurable:

* Error rates
* Latency variations
* Noise and misleading signals

Each episode represents a distinct failure scenario.


### Task Levels

Three difficulty tiers increase ambiguity and reasoning complexity:

**Easy**
Single-service failure with a strong error signal and clean logs.
Objective: identify failing service and error code.

**Medium**
Cascading failure with partial or missing logs.
Objective: identify root service and affected service.

**Hard**
Intermittent or low-signal failures with noisy logs and red herrings.
Objective: identify root service, endpoint, failure pattern, and severity.


## Observations

Depending on the task, the agent receives:

* Service logs with timestamps and metrics
* Event timelines showing causal ordering
* Distributed trace graphs showing service interactions


## Action Space

The agent must return structured JSON:

* Easy: `service_name`, `error_code`
* Medium: `root_service`, `affected_service`
* Hard: `root_service`, `endpoint`, `failure_pattern`, `severity`

Strict schemas enforce precise, machine-readable outputs.


## Reward Function

Scoring is normalized to the range `[0.0, 1.0]`.

Key properties:

* Partial credit for partially correct answers
* Higher weight for correctly identifying root causes
* Deterministic scoring for reproducibility
  

## Key Features

* Realistic synthetic log and trace simulation
* Multi-level difficulty with increasing ambiguity
* Structured evaluation with partial-credit scoring
* Designed for integration with LLM-based or programmatic agents
* Supports both interactive UI and automated evaluation


## Live Demo

The project is deployed on Hugging Face:

https://aadilparwez-distributed-failure-analyser.hf.space

The interface supports:

* Task selection
* Log visualization
* Automated failure diagnosis
* Scoring and feedback


## Project Structure

```
failure_analyzer/
├── models.py
├── server/
├── environment.py
│── app.py
├── inference.py
├── requirements.txt
└── README.md
```

## Limitations

* Uses synthetic data rather than production logs
* Limited scenario diversity
* LLM-based inference depends on external models and APIs

## Future Work

* Integration with real observability pipelines
* Expansion of scenario generation
* Learning-based agents instead of static prompting
* Advanced visualization for traces and timelines
* Real-time failure simulation


## Conclusion

This project demonstrates that failure diagnosis in distributed systems can be formalized as a structured, evaluable process. By combining simulation, constrained outputs, and reward-based evaluation, it provides a foundation for building automated debugging systems.



