---
title: llm vllm
date: 2025-03-06 15:25:11
tags: sinppet
categories: llm
---

# route

```shell
INFO llm_engine.py:436] init engine (profile, create kv cache, warmup model) took 135.23 seconds
INFO api_server.py:958] Starting vLLM API server on http://0.0.0.0:8000
Available routes are:
 Route: /openapi.json, Methods: GET, HEAD
 Route: /docs, Methods: GET, HEAD
 Route: /docs/oauth2-redirect, Methods: GET, HEAD
 Route: /redoc, Methods: GET, HEAD
 Route: /health, Methods: GET
 Route: /ping, Methods: GET, POST
 Route: /tokenize, Methods: POST
 Route: /detokenize, Methods: POST
 Route: /v1/models, Methods: GET
 Route: /version, Methods: GET
 Route: /v1/chat/completions, Methods: POST
 Route: /v1/completions, Methods: POST
 Route: /v1/embeddings, Methods: POST
 Route: /pooling, Methods: POST
 Route: /score, Methods: POST
 Route: /v1/score, Methods: POST
 Route: /v1/audio/transcriptions, Methods: POST
 Route: /rerank, Methods: POST
 Route: /v1/rerank, Methods: POST
 Route: /v2/rerank, Methods: POST
 Route: /invocations, Methods: POST
INFO:     Started server process [1]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
```

