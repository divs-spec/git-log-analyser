# git-log-analyser

**git-log-analyser** is a CLI tool that uses local AI models to analyze Git commit history and extract meaningful insights—without manually reading through hundreds or thousands of commits.

All processing is **fully offline**. There are no API keys, no external services, and no vendor lock-in. The tool integrates **Ollama**, **SentenceTransformers**, and **ChromaDB** to provide fast, local, AI-assisted analysis of commit history.

---

## Motivation

Understanding the evolution of a codebase often requires digging through long and noisy commit logs. This process is tedious, error-prone, and doesn’t scale well for large repositories.

**git-log-analyser solves this problem** by turning commit history into a searchable knowledge base that can answer natural-language questions about how and why the code evolved.

---

## Key Features

* **100% offline** — runs entirely on your machine
* **No configuration churn** — configure once, then reuse across repositories
* **Simple CLI interface** — automation- and CI/CD-friendly
* **Modular architecture** — small, focused components following Unix design principles
* **LLM-agnostic** — works with any Ollama-supported local model

---

## How It Works

1. Connects to a Git repository
2. Extracts commit history using Git
3. Embeds commit messages using `SentenceTransformers`
4. Stores embeddings in a local **ChromaDB** vector store
5. Answers natural-language questions using a local LLM (via Ollama)

This architecture enables Retrieval-Augmented Generation (RAG) over commit history while keeping all data local.

---

## Architecture Overview

```
Git Repo
   ↓
Commit Parser (GitPython)
   ↓
Embedding Model (SentenceTransformers)
   ↓
Vector Store (ChromaDB)
   ↓
Local LLM (Ollama)
   ↓
CLI Output
```

---

## Components

### `populate_commits_into_chromadb.py`

* Parses Git commit history using `GitPython`
* Embeds commit messages into vector representations
* Stores vectors in ChromaDB for later retrieval
* Supports:

  * CLI usage via `Typer`
  * Fully configuration-driven execution via `settings.toml`

---

### `analyse_commits.py`

* Loads predefined or user-supplied questions
* Retrieves the most relevant commits from ChromaDB
* Sends the question and retrieved context to a local LLM
* Outputs concise, human-readable insights

---

## Limitations & Design Notes

This project uses **RAG** to analyze Git commit history. It works well for questions about:

* Specific changes
* Related groups of commits
* Patterns within a bounded context

However, some questions **cannot be answered reliably by an LLM alone**, such as:

* “What is the largest commit?”
* “Which commit was the riskiest?”
* “Which change touched the most lines of code?”

These questions require **global reasoning or explicit computation**, which exceeds an LLM’s context window.

### Key Insight

Effective AI tooling is not about replacing logic with LLMs.
It’s about **combining deterministic computation with probabilistic reasoning**.

A more robust approach would:

* Let the LLM identify *what* needs to be computed
* Perform that computation in code (e.g., diff size, churn metrics)
* Feed the results back to the model for interpretation

This project reflects that learning and is intentionally designed to evolve in that direction.

---

## Philosophy

> LLMs are powerful assistants — not replacements for engineering.

**git-log-analyser** treats AI as a reasoning layer on top of well-structured data, enabling insight without sacrificing correctness, transparency, or control.
