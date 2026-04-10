Distributed Systems Failure Analyzer
An OpenEnv RL environment where an AI agent analyzes logs from distributed microservices and identifies failures, root causes, and degradation patterns. Built for the Meta × PyTorch × Scaler OpenEnv Hackathon 2026.

🎯 Problem Statement
Real-world distributed systems fail in complex ways. This environment simulates realistic service logs at three difficulty levels — from an obvious single-service crash to a subtle intermittent failure buried across six noisy services. The agent must act like a real Site Reliability Engineer (SRE) and diagnose what went wrong.

📋 Three Tasks
✅ Task 1 — Easy
Services: 1

Error rate: 99% (obvious spike)

Log quality: Clean logs

Agent output: {"service_name": "...", "error_code": "..."}

Grader: service_name=0.5 + error_code=0.5 = 1.0

✅ Task 2 — Medium
Services: 3

Error rate: ~40% (subtle)

Log quality: Partial logs

Agent output: {"root_service": "...", "affected_service": "..."}

Grader: root_service=0.6 + affected_service=0.4 = 1.0

✅ Task 3 — Hard
Services: 6

Error rate: ~8% (intermittent)

Log quality: Noisy with red herrings

Agent output: {"root_service":"...","endpoint":"...","failure_pattern":"...","severity":"..."}

Grader: root=0.4 + endpoint=0.2 + pattern=0.2 + severity=0.2 = 1.0

🏆 Reward Function
All fields correct: 1.0

Most fields correct: 0.6 – 0.8

Some fields correct: 0.2 – 0.5

All fields wrong: 0.0

Partial credit is awarded per field, ensuring RL agents always receive a meaningful learning signal.

🚀 Quick Start
Install

bash
pip install -r requirements.txt
Run server locally

bash
uvicorn server.app:app --host 0.0.0.0 --port 7860
Test the API

bash
# Reset to easy task
curl -X POST http://localhost:7860/reset -H "Content-Type: application/json" -d '{"task": "easy"}'

# Submit an answer
curl -X POST http://localhost:7860/step -H "Content-Type: application/json" -d '{"service_name": "payment-api", "error_code": "HTTP_500"}'

# Check current state
curl http://localhost:7860/state
Run inference

bash
export HF_TOKEN=hf_your_token_here
export MODEL_NAME=mistralai/Mistral-7B-Instruct-v0.3
export API_BASE_URL=https://api-inference.huggingface.co/v1

python inference.py
🐳 Docker
bash
# Build the image
docker build -t failure-analyzer .

# Run it
docker run -p 7860:7860 failure-analyzer
📁 Project Structure
Code
failure_analyzer/
├── models.py              ← Pydantic models
├── environment.py         ← Game logic, log generators, reward graders
├── app.py                 ← FastAPI server (/reset /step /state)
├── inference.py           ← LLM agent with required log format
├── requirements.txt       ← Python dependencies
├── Dockerfile             ← Container for HF Spaces
├── openenv.yaml           ← OpenEnv spec config
└── README.md              ← Documentation
🔌 API Reference
GET / → Health check

POST /reset → Start new episode ({"task": "easy"})

POST /step → Submit task-specific JSON

GET /state → Current environment state

📐 Action Spaces
Easy: {"service_name": "payment-api", "error_code": "HTTP_500"}

Medium: {"root_service": "auth-service", "affected_service": "checkout-api"}

Hard:

json
{
  "root_service": "user-db",
  "endpoint": "/api/v1/query",
  "failure_pattern": "intermittent_timeout",
  "severity": "high"
}
Valid failure_pattern: intermittent_timeout, error_spike, memory_leak, connection_pool_exhausted, cascading_crash  
Valid severity: low, medium, high, critical

📊 Example Inference Log Output
Code
[START] task=easy env=failure_analyzer model=mistralai/Mistral-7B-Instruct-v0.3
[STEP] step=1 action={"service_name":"payment-api","error_code":"HTTP_500"} reward=1.00 done=true
[END] success=true steps=1 score=1.00

[START] task=medium env=failure_analyzer model=mistralai/Mistral-7B-Instruct-v0.3
[STEP] step=1 action={"root_service":"auth-service","affected_service":"checkout-api"} reward=1.00 done=true
[END] success=true steps=1 score=1.00

[START] task=hard env=failure_analyzer model=mistralai/Mistral-7B-Instruct-v0.3
[STEP] step=1 action={"root_service":"user-db","endpoint":"/api/v1/query","failure_pattern":"intermittent_timeout","severity":"high"} reward=1.00 done=true
[END] success=true steps=1 score=1.00
