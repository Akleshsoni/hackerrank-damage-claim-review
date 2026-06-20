# Multi-Modal Evidence Review System

A damage claim verification system built for the HackerRank Orchestrate Challenge.

## What it does

Verifies damage claims using:
- 📸 Submitted images (primary source of truth)
- 💬 Claim conversation transcript
- 📋 User history and risk patterns
- ✅ Minimum evidence requirements

Supports three object types: **car**, **laptop**, **package**

## Decision Output

For each claim, the system produces:

| Field | Description |
|---|---|
| `claim_status` | `supported`, `contradicted`, or `not_enough_information` |
| `issue_type` | Type of damage visible in images |
| `object_part` | Relevant part of the object |
| `severity` | `none`, `low`, `medium`, `high`, or `unknown` |
| `risk_flags` | Quality, mismatch, authenticity, or history risks |
| `evidence_standard_met` | Whether images meet minimum evidence requirements |

## Project Structure

```
code/
├── main.py                        # CLI entry point
├── requirements.txt               # anthropic>=0.40.0
└── evidence_review/
    ├── schema.py                  # Controlled vocabularies
    ├── conversation.py            # Injection detection
    ├── user_history.py            # Risk scoring
    ├── evidence_requirements.py   # Evidence checklist
    ├── vision_agent.py            # Multimodal model calls
    ├── decision.py                # Deterministic fusion
    ├── pipeline.py                # Orchestration + cache
    ├── batch_runner.py            # Batch API path
    └── tests/
        └── test_offline.py        # 19 tests, zero API calls

evaluation/
├── evaluate.py                    # Scoring harness
└── evaluation_report.md          # Cost/latency analysis

output.csv                         # Predictions for all 44 claims
log.txt                            # Chat transcript
```

## How to Run

```bash
# Install dependencies
pip install -r code/requirements.txt

# Set API key
export ANTHROPIC_API_KEY=your_key_here   # Linux/Mac
set ANTHROPIC_API_KEY=your_key_here      # Windows

# Run evaluation on sample claims
python evaluation/evaluate.py

# Run on test claims
python code/main.py --input dataset/claims.csv --output dataset/output.csv
```

## Key Features

- **Injection detection** — catches prompt injection attempts in claim text including Hinglish patterns
- **User history risk scoring** — flags high-rejection-rate users
- **Disk cache** — SHA-256 keyed cache avoids repeated API calls
- **Thread pool** — 4 parallel workers for faster processing
- **Retry logic** — exponential backoff on rate limits and server errors
- **Schema clamping** — all outputs validated against allowed vocabularies

## Submission Files

| File | Description |
|---|---|
| `code.zip` | Full runnable solution |
| `output.csv` | Predictions for all 44 rows in `claims.csv` |
| `log.txt` | Chat transcript |

## Tech Stack

- Python 3.12+
- Anthropic Claude API (claude-sonnet-4-6)
- Multimodal vision analysis
- Forced tool-use for structured output
