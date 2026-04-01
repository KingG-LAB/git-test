ㅎㅇㅎㅇ
# DevOps 1day Assignment

This repository contains a minimal Flask backend application for the Jenkins/Tekton CI pipeline assignment.

## Run locally

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python app.py
```

## Docker build

```bash
docker build -t sk032-backend:local .
docker run --rm -p 5000:5000 sk032-backend:local
```

## Jenkins pipeline summary

1. Checkout source from GitHub
2. Verify build environment
3. Build Docker image
4. Push the backend image to Harbor Registry
