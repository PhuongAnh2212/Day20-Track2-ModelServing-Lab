# 01 — llama.cpp quickstart

A 30-minute tour of the deck's vocabulary on your own laptop. You'll measure **TTFT, TPOT, P50/P95/P99** and compare two GGUF quantizations of the same model.

## Goals

By the end of this track you should be able to answer:

1. What's the TTFT and TPOT for *your* laptop on a Q4_K_M model? Where does that put you on the latency taxonomy from §0?
2. How much does dropping from Q4_K_M to Q2_K save in RAM, and what does it cost you in latency / quality?
3. What does `n_threads` actually do? (Spoiler: more isn't always faster — physical cores beat logical cores for compute-bound work.)

## Run

```bash
# from repo root, with .venv activated
python 01-llama-cpp-quickstart/benchmark.py
```

This loads the primary GGUF model from `models/active.json`, runs:

- A **single-prompt latency** trace (TTFT + TPOT)
- A **20-request batch** to compute P50 / P95 / P99 over a realistic prompt distribution
- A **quantization comparison** loading both Q4_K_M and Q2_K, same prompts, side-by-side numbers

Output is printed to stdout and written to `benchmarks/01-quickstart-results.md`.

## Reading the output

```
TTFT (Time To First Token):     128 ms     ← prefill latency
TPOT (Time Per Output Token):    34 ms     ← decode latency
Decode rate:                     29 tok/s
P50 / P95 / P99 e2e (32 tok):   1.21 / 1.45 / 1.62 s
```

**TTFT** is dominated by prefill compute — long prompts make this big. **TPOT** is dominated by KV-cache memory bandwidth — small models on fast RAM look great here.

## Knobs to try

Edit `benchmark.py` (or pass via env vars) and observe the change:

| Variable | Default | What it does |
|---|---|---|
| `LAB_N_CTX` | 2048 | Context window. Bigger = more KV-cache memory. |
| `LAB_N_THREADS` | physical-core count | Threads. **More ≠ faster.** Try `cores // 2`, `cores`, `cores * 2`. |
| `LAB_N_BATCH` | 512 | Prompt processing batch size (prefill). |
| `LAB_N_GPU_LAYERS` | 0 (or auto) | Layers offloaded to GPU. `99` = full offload. |
| `LAB_TEMPERATURE` | 0.7 | Sampling temp. |

## Don't be fooled by

- **Cold-start overhead.** First call includes model load (could be 10s+). Throw away the first run when computing P50/P95/P99.
- **OS file cache.** Second run loads the model 5–10× faster because it's already in page cache. Both numbers matter, but make sure you know which one you're reporting.
- **Background processes.** Browser, IDE, Slack — all eat CPU and memory bandwidth. Close them before benchmarking.

## Deck mapping

- §0 Latency Taxonomy → the TTFT/TPOT/P95 numbers you just measured
- §1 Quantization → the Q4_K_M vs Q2_K side-by-side
- §2 KV Cache & Attention → `LAB_N_CTX` controls how much KV cache you allocate
- §3 Production Tuning → `LAB_N_THREADS`, `LAB_N_BATCH`, `LAB_N_GPU_LAYERS` are the knobs


── Loading primary (Q4_K_M): Llama-3.2-3B-Instruct-Q4_K_M.gguf
   n_threads=10  n_ctx=2048  n_batch=512  n_gpu_layers=99
llama_context: n_ctx_seq (2048) < n_ctx_train (131072) -- the full capacity of the model will not be utilized
   model loaded in 765 ms
   [ 1/10] ttft=  66.3ms  tpot= 18.2ms  e2e= 1214.0ms  tok=64
   [ 2/10] ttft=  63.6ms  tpot= 18.2ms  e2e= 1209.8ms  tok=64
   [ 3/10] ttft=  64.7ms  tpot= 18.3ms  e2e= 1214.5ms  tok=64
   [ 4/10] ttft=  64.0ms  tpot= 18.2ms  e2e= 1209.9ms  tok=64
   [ 5/10] ttft=  63.9ms  tpot= 18.2ms  e2e= 1209.9ms  tok=64
   [ 6/10] ttft=  64.0ms  tpot= 18.2ms  e2e= 1209.9ms  tok=64
   [ 7/10] ttft=  63.6ms  tpot= 18.2ms  e2e= 1209.0ms  tok=64
   [ 8/10] ttft=  40.2ms  tpot= 18.2ms  e2e= 1185.0ms  tok=64
   [ 9/10] ttft=  63.8ms  tpot= 18.3ms  e2e= 1217.4ms  tok=64
   [10/10] ttft=  63.8ms  tpot= 18.3ms  e2e= 1215.5ms  tok=64

── Loading compare (Q2_K): Llama-3.2-3B-Instruct-Q4_K_M.gguf
   n_threads=10  n_ctx=2048  n_batch=512  n_gpu_layers=99
llama_context: n_ctx_seq (2048) < n_ctx_train (131072) -- the full capacity of the model will not be utilized
   model loaded in 201 ms
   [ 1/10] ttft=  63.5ms  tpot= 18.2ms  e2e= 1211.5ms  tok=64
   [ 2/10] ttft=  63.4ms  tpot= 18.3ms  e2e= 1216.8ms  tok=64
   [ 3/10] ttft=  63.9ms  tpot= 18.2ms  e2e= 1213.2ms  tok=64
   [ 4/10] ttft=  63.8ms  tpot= 18.3ms  e2e= 1216.7ms  tok=64
   [ 5/10] ttft=  64.6ms  tpot= 18.4ms  e2e= 1225.2ms  tok=64
   [ 6/10] ttft=  64.6ms  tpot= 18.3ms  e2e= 1215.4ms  tok=64
   [ 7/10] ttft=  63.6ms  tpot= 18.2ms  e2e= 1212.3ms  tok=64
   [ 8/10] ttft=  40.1ms  tpot= 18.3ms  e2e= 1190.6ms  tok=64
   [ 9/10] ttft=  63.9ms  tpot= 18.2ms  e2e= 1213.0ms  tok=64
   [10/10] ttft=  63.6ms  tpot= 18.3ms  e2e= 1214.6ms  tok=64

# 01 — Quickstart Results

Settings: `n_threads=10`, `n_ctx=2048`, `n_batch=512`, `n_gpu_layers=99`.

| Model | Load (ms) | TTFT P50/P95 (ms) | TPOT P50/P95 (ms) | E2E P50/P95/P99 (ms) | Decode rate (tok/s) |
|---|---:|---:|---:|---:|---:|
| Llama-3.2-3B-Instruct-Q4_K_M.gguf | 765 | 64 / 66 | 18.2 / 18.3 | 1210 / 1216 / 1217 | 55.0 |
| Llama-3.2-3B-Instruct-Q4_K_M.gguf | 201 | 64 / 65 | 18.3 / 18.4 | 1214 / 1221 / 1224 | 54.8 |

## Observations

- TTFT is the prefill cost. With short prompts this is small; with long prompts it dominates.
- TPOT is per-token decode latency. The decode rate is `1000 / TPOT_p50`.
- The bigger quantization (Q4_K_M) is usually only ~30–60% slower than Q2_K but produces noticeably better text. Q2_K is for *truly* tight RAM.
- `n_threads = physical_cores` is usually best on CPU. Hyperthreading (`logical_cores`) often hurts because the work is bandwidth-bound.
