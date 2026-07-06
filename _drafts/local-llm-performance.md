---
title: Performance Testing for Local LLMs
slug: local-llm-performance
---
Question: how many reps to run? 50
Tests:
- model size
- context size
- mixture of experts
- qat
- quantization

Notes:
- All models use Q4_M variant (default for llama.cpp)
- 50 runs for confidence
- Bugginess with Qwen 27B: model fits entirely on GPU but leaves no room for even small context. Explicitly offloading layers to CPU results in a whopping 0.99 tokens per second.
| qwen35 27B Q4_K - Medium       |  15.58 GiB |    26.90 B | CUDA       |  60 |   q4_0 |   q4_0 |           tg128 |          0.99 ± 0.03 |

Starting with two of the most popular small models in GGUF on Hugging Face Gemma 4 and Qwen 3.5. Comparing different sizes of each model
- unsloth/gemma-4-E4B-it-GGUF (600k monthly downloads)
- unsloth/gemma-4-12b-it-GGUF  (> 1 million monthly downloads)
- unsloth/Qwen3.5-4B-GGUF (> 1 million monthly downloads)
- unsloth/Qwen3.5-9B-GGUF (> 1 million monthly downloads)

```
make bench ARGS="-r 50 -hf unsloth/Qwen3.5-4B-GGUF:Q4_K_M -hf unsloth/Qwen3.5-9B-GGUF:Q4_K_M -hf unsloth/gemma-4-E4B-it-GGUF:Q4_K_M -hf unsloth/gemma-4-12b-it-GGUF:Q4_K_M"
```

| model                          |       size |     params | backend    | ngl |            test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | --------------: | -------------------: |
| qwen35 4B Q4_K - Medium        |   2.54 GiB |     4.21 B | CUDA       |  -1 |           pp512 |      4842.34 ± 77.10 |
| qwen35 4B Q4_K - Medium        |   2.54 GiB |     4.21 B | CUDA       |  -1 |           tg128 |        117.53 ± 0.17 |
| qwen35 9B Q4_K - Medium        |   5.28 GiB |     8.95 B | CUDA       |  -1 |           pp512 |      3112.83 ± 14.29 |
| qwen35 9B Q4_K - Medium        |   5.28 GiB |     8.95 B | CUDA       |  -1 |           tg128 |         71.63 ± 0.06 |
| gemma4 E4B Q4_K - Medium       |   4.62 GiB |     7.52 B | CUDA       |  -1 |           pp512 |      5239.35 ± 81.20 |
| gemma4 E4B Q4_K - Medium       |   4.62 GiB |     7.52 B | CUDA       |  -1 |           tg128 |        107.16 ± 0.17 |
| gemma4 12B Q4_K - Medium       |   6.62 GiB |    11.91 B | CUDA       |  -1 |           pp512 |      2308.02 ± 13.44 |
| gemma4 12B Q4_K - Medium       |   6.62 GiB |    11.91 B | CUDA       |  -1 |           tg128 |         52.17 ± 0.03 |

Thoughts:
- Performance decreases as model size increases
- Benefit of "Effective" parameters - 8B model performs closer to 4B model

Next test: benefits of quantization aware training (QAT)
```
make bench ARGS="-r 50 -hf unsloth/gemma-4-E4B-it-qat-GGUF:UD-Q4_K_XL -hf unsloth/gemma-4-E4B-it-GGUF:Q4_K_M -hf unsloth/gemma-4-12B-it-qat-GGUF:UD-Q4_K_XL -hf unsloth/gemma-4-12b-it-GGUF:Q4_K_M"
```

| model                          |       size |     params | backend    | ngl |            test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | --------------: | -------------------: |
| gemma4 E4B QAT Q4_0            |   3.91 GiB |     7.46 B | CUDA       |  -1 |           pp512 |     5837.48 ± 157.27 |
| gemma4 E4B QAT Q4_0            |   3.91 GiB |     7.46 B | CUDA       |  -1 |           tg128 |        120.71 ± 0.21 |
| gemma4 E4B Q4_K - Medium       |   4.62 GiB |     7.52 B | CUDA       |  -1 |           pp512 |      5243.45 ± 75.25 |
| gemma4 E4B Q4_K - Medium       |   4.62 GiB |     7.52 B | CUDA       |  -1 |           tg128 |        105.77 ± 0.16 |
| gemma4 12B QAT Q4_0            |   6.24 GiB |    11.91 B | CUDA       |  -1 |           pp512 |      2606.91 ± 16.60 |
| gemma4 12B QAT Q4_0            |   6.24 GiB |    11.91 B | CUDA       |  -1 |           tg128 |         55.40 ± 0.04 |
| gemma4 12B Q4_K - Medium       |   6.62 GiB |    11.91 B | CUDA       |  -1 |           pp512 |      2304.57 ± 10.37 |
| gemma4 12B Q4_K - Medium       |   6.62 GiB |    11.91 B | CUDA       |  -1 |           tg128 |         52.15 ± 0.04 |

Thoughts:
- QAT increases performance, less so for larger models

Next test: Best of the rest
- Compare performance across less popular models

```
make bench ARGS="-r 50 -hf unsloth/Ministral-3-14B-Reasoning-2512-GGUF:Q4_K_M -hf unsloth/Phi-4-reasoning-plus-GGUF:Q4_K_M -hf unsloth/granite-4.1-8b-GGUF:Q4_K_M"
```

| model                          |       size |     params | backend    | ngl |            test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | --------------: | -------------------: |
| mistral3 14B Q4_K - Medium     |   7.67 GiB |    13.51 B | CUDA       |  -1 |           pp512 |      2248.44 ± 19.75 |
| mistral3 14B Q4_K - Medium     |   7.67 GiB |    13.51 B | CUDA       |  -1 |           tg128 |         48.53 ± 0.05 |
| phi3 14B Q4_K - Medium         |   8.43 GiB |    14.66 B | CUDA       |  -1 |           pp512 |       2050.33 ± 2.83 |
| phi3 14B Q4_K - Medium         |   8.43 GiB |    14.66 B | CUDA       |  -1 |           tg128 |         45.86 ± 0.01 |
| granite 3B Q4_K - Medium       |   4.98 GiB |     8.79 B | CUDA       |  -1 |           pp512 |      3227.85 ± 14.79 |
| granite 3B Q4_K - Medium       |   4.98 GiB |     8.79 B | CUDA       |  -1 |           tg128 |         74.94 ± 0.07 |

Next test: too big?

```
make bench ARGS="-r 50 -hf unsloth/gpt-oss-20b-GGUF:UD-Q4_K_XL -hf unsloth/gemma-4-26B-A4B-it-qat-GGUF:UD-Q4_K_XL -hf unsloth/Mistral-Small-3.2-24B-Instruct-2506-GGUF:UD-Q4_K_XL"
```

| model                          |       size |     params | backend    | ngl |            test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | --------------: | -------------------: |
| gpt-oss 20B Q4_K - Medium      |  11.04 GiB |    20.91 B | CUDA       |  -1 |           pp512 |      5889.88 ± 46.65 |
| gpt-oss 20B Q4_K - Medium      |  11.04 GiB |    20.91 B | CUDA       |  -1 |           tg128 |        152.49 ± 0.25 |
| gemma4 26B.A4B Q4_0            |  13.26 GiB |    25.23 B | CUDA       |  -1 |           pp512 |      3656.47 ± 23.36 |
| gemma4 26B.A4B Q4_0            |  13.26 GiB |    25.23 B | CUDA       |  -1 |           tg128 |        135.36 ± 0.40 |
| llama 13B Q4_K - Medium        |  13.54 GiB |    23.57 B | CUDA       |  -1 |           pp512 |       1293.08 ± 7.41 |
| llama 13B Q4_K - Medium        |  13.54 GiB |    23.57 B | CUDA       |  -1 |           tg128 |         28.54 ± 0.01 |

