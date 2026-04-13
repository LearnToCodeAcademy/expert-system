# Rule-Based Expert System for Network Troubleshooting (Hybrid ML)

A production-style Python expert system that combines:

- **Rule-based diagnosis** (knowledge base + forward chaining inference engine)
- **Machine learning classifier** (RandomForest)
- **Hybrid decision layer** (use strong rule conclusions first, then fallback to ML)

## Project Structure

```text
project/
│
├── data/
│   ├── raw/
│   ├── processed/
│
├── models/
│   ├── trained_model.pkl
│
├── src/
│   ├── data_preprocessing.py
│   ├── feature_engineering.py
│   ├── rules.py
│   ├── inference_engine.py
│   ├── ml_model.py
│   ├── evaluator.py
│   ├── utils.py
│
├── app/
│   ├── main.py
│   ├── dashboard.py
│
├── notebooks/
│   ├── experimentation.ipynb
│
├── requirements.txt
└── README.md
```

## Supported Labels

- `normal`
- `slow_network`
- `packet_loss_issue`
- `congestion_issue`
- `security_attack`

## Dataset Format

Input CSV should contain the following columns:

- `latency`
- `packet_loss`
- `jitter`
- `bandwidth_usage`
- `congestion`
- `traffic_anomaly`
- `label`

An example file is provided at:

- `data/raw/network_metrics_sample.csv`

## How It Works

1. **Data Processing**
   - Load CSV
   - Impute missing values (median)
   - Normalize features (standard scaling)
   - Encode labels
   - Split train/test
2. **Rule-Based Core**
   - Rules defined as structured objects in `src/rules.py`
   - Forward chaining in `src/inference_engine.py`
   - Priority and confidence determine the best rule outcome
3. **ML Component**
   - Train RandomForest classifier
   - Save model to `models/trained_model.pkl`
4. **Hybrid Layer**
   - If rule confidence is above threshold (default `0.9`) use rules
   - Else fallback to ML prediction
5. **Evaluation**
   - Computes Accuracy, Precision, Recall, F1
   - Compares rule-based, ML, and hybrid

## Installation

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

## Train + Evaluate

```bash
python app/main.py train --data data/raw/network_metrics_sample.csv
```

This command:
- Trains the model
- Saves model/preprocessor/label encoder in `models/`
- Prints metrics for ML, rule-based, and hybrid systems

## Run CLI Diagnosis

```bash
python app/main.py diagnose \
  --latency 110 \
  --packet_loss 1.2 \
  --jitter 20 \
  --bandwidth_usage 70 \
  --congestion 0.55 \
  --traffic_anomaly 0.3
```

Example output:

```json
{
  "diagnosis": "slow_network",
  "confidence": 0.88,
  "source": "rule-based",
  "recommendation": "Investigate routing path, DNS delays, and overloaded transit links.",
  "explanation": "Triggered rules..."
}
```

## Optional Dashboard (Streamlit)

```bash
streamlit run app/dashboard.py
```

Use sliders to set symptoms and click **Diagnose** for hybrid output.

## Notes for Production Hardening

- Replace sample dataset with real telemetry pipelines
- Add model versioning and drift monitoring
- Add rule management UI and rule validation tests
- Add API service layer (FastAPI) and authentication
- Integrate with SIEM/NMS tools for automated remediation
