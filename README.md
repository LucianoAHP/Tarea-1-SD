# Tarea 1 – Sistemas Distribuidos (Q&A con LLM, Caché y Métricas)

Pipeline dockerizado para simular un sistema de preguntas y respuestas con:
- **Ollama** (LLM local) – generación y evaluación por rúbrica 1–10.
- **API (FastAPI)** – endpoints `/query`, `/cache/check`, `/history/check`.
- **Redis** – caché distribuida con TTL y política (LRU/LFU).
- **Postgres + Adminer** – persistencia de eventos (hit/miss, score) y preguntas.
- **Loadgen** – generador de tráfico (Poisson/Lognormal).

## Arquitectura
[Loadgen] --> [API FastAPI] --> [Redis cache] --HIT--> responde
| |
| '--MISS--> [Ollama LLM] --responde-->
| |
'--> [Postgres] (response_event, question_counter)
^
| '-- [Ingestor] (opcional desde storage.jsonl)
[Adminer] (UI DB) <-----'
## Requisitos

- Ubuntu/Linux con **Docker** y **Docker Compose v2**
- **Ollama** 0.12+ en el host, escuchando en `0.0.0.0:11434`
- Modelos recomendados:
  - Generador: `llama3.2:3b`
  - Evaluador: `llama3.2:1b`

Comprobar modelos:
```bash
curl -s http://127.0.0.1:11434/api/tags | jq .
