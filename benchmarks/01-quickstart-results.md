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

(Edit this file with your own observations before submitting.)
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
