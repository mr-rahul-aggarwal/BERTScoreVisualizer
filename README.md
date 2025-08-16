# BERTScoreVisualizer

![alt text](docs/img1.png)

BERTScoreVisualizer is a React Web App that visualizes how the BERTScore metric scores generated text against a reference. It shows token-level matchings and overall precision/recall/F1.

For a deeper dive, see our [paper on arXiv](https://arxiv.org/abs/2409.17160) and the [demo video](https://drive.google.com/file/d/1X0SjDpe928YHPZhadwaPLFO55gVIlWeP/view).

## Quick start (local, macOS/Linux)

The app has two parts: a Flask backend and a React frontend.

1) Backend (Flask + Transformers)

```zsh
cd backend
python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade pip wheel setuptools
pip install -r requirements.txt

# Run from the backend directory; set FLASK_APP to module name "app"
export FLASK_APP=app
python -m flask run --host 127.0.0.1 --port 5000
```

Notes:
- First run will download a BERT model (~440MB). This can take a minute; keep the backend running until it finishes.
- If you see “Could not import 'app'”, ensure you are in the `backend` folder and used `export FLASK_APP=app`.

2) Frontend (React)

```zsh
cd frontend

# Tell the UI where your backend runs (create this once)
printf "REACT_APP_BACKEND_URL=http://127.0.0.1:5000\n" > .env.local

npm install
npm start
```

Open the app at:
- http://localhost:3000 (typical CRA dev server)

If you host under a subpath (e.g., GitHub Pages at /BERTScoreVisualizer), the `homepage` field in `frontend/package.json` is already set for builds. For local dev, use the plain http://localhost:3000.

## API (backend)

Base URL (local): http://127.0.0.1:5000

- GET `/available_models`
  - Response: JSON array of available model IDs (e.g., `["bert-base-uncased"]`).

- GET `/bertscore` with query params:
  - `reference_text`: string
  - `candidate_text`: string
  - `pretrained_model_name`: string (defaults to `bert-base-uncased`)
  - Example:
    - `/bertscore?reference_text=Hello%20world&candidate_text=Hello&pretrained_model_name=bert-base-uncased`
  - Response: JSON object with
    - precision, recall, f1_score (floats rounded to 4 decimals)
    - reference_tokens, candidate_tokens (arrays)
    - recall_matchings, recall_matching_values (arrays)
    - precision_matchings, precision_matching_values (arrays)
    - unmatched_reference_tokens, unmatched_candidate_tokens (arrays)

## Troubleshooting

- “zsh: command not found: flask”
  - Activate your venv and install deps:
    - `source backend/.venv/bin/activate && pip install -r backend/requirements.txt`
  - Or run via Python module: `python -m flask ...`.

- “Error: Could not import 'app'”
  - Run from the `backend` directory and set `export FLASK_APP=app` (without `.py`).

- Frontend shows “Failed to fetch”
  - Ensure backend is running at http://127.0.0.1:5000.
  - Set `frontend/.env.local` to `REACT_APP_BACKEND_URL=http://127.0.0.1:5000` and restart `npm start`.
  - Verify the endpoint works: `curl http://127.0.0.1:5000/available_models`.
  - First request may be slow while the model downloads.

- Port already in use
  - Change ports (e.g., `--port 5050`) or stop the conflicting process.




## Contact

If you have any questions or concerns regarding this work, please contact Sebastian Jaskowski (sjaskowski3@gatech.edu)

## Acknowledgements

This project provides a visualization for the BERTScore Automatic Evaluation Metric, building on the work of the original BERTScore paper. You can check out the original BERTScore paper [here](https://arxiv.org/abs/1904.09675), as well as their [GitHub Repository](https://github.com/Tiiiger/bert_score?tab=readme-ov-file) for the official python package.
