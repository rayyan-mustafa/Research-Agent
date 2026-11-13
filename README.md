# Research Agent

[![Build and Push Docker Image](https://github.com/rayyan-mustafa/Research-Agent/actions/workflows/build-and-push.yml/badge.svg)](https://github.com/rayyan-mustafa/Research-Agent/actions/workflows/build-and-push.yml)

A small AI assistant that explains well-known AI research papers. Pick a paper (GPT-4, Attention Is All You Need, BERT, Chain of Thought, RAG), an explanation style and a length, and it writes a tailored summary with Google Gemini through LangChain. The app is built with Streamlit, packaged as a Docker image, and published to GitHub Container Registry by GitHub Actions.

## Stack

- Python 3.11, Streamlit, LangChain, Google Gemini (`gemini-2.0-flash`)
- Docker (`python:3.11-slim` base)
- GitHub Actions with Docker Buildx, pushing to GHCR
- Dev Container config for a ready-made development environment

## CI/CD

`.github/workflows/build-and-push.yml` runs on every push to `main` (and on demand):

1. Builds the image with Docker Buildx.
2. Logs in to GHCR with the workflow's own `GITHUB_TOKEN` (no stored credentials).
3. Tags the image with the branch name, `branch-<sha>` and `latest` (plus SemVer tags when a version tag is pushed).
4. Pushes it to `ghcr.io/rayyan-mustafa/research-agent`.

## Run it

You need a Google Gemini API key. Streamlit reads it from `.streamlit/secrets.toml`, which is git-ignored:

```toml
# .streamlit/secrets.toml
GOOGLE_API_KEY = "your-key-here"
```

### With Docker (prebuilt image)

```bash
docker run --rm -p 8501:8501 \
  -v "$PWD/.streamlit:/app/.streamlit:ro" \
  ghcr.io/rayyan-mustafa/research-agent:latest
```

### Build locally

```bash
docker build -t research-agent .
docker run --rm -p 8501:8501 -v "$PWD/.streamlit:/app/.streamlit:ro" research-agent
```

### Without Docker

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
streamlit run app.py
```

Then open http://localhost:8501.

## Project layout

```
app.py                         Streamlit app and LangChain prompt
template.json                  prompt template
Dockerfile                     container image
.github/workflows/             build and push to GHCR
.devcontainer/                 dev container setup
requirements.txt               Python dependencies
```

## Security

Never commit `secrets.toml` or `.env` files. Both are listed in `.gitignore`.
