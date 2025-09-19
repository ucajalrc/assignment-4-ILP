# Assignment 4: Exploring Instruction-Level Parallelism (ILP) in Modern Processors

## Objective

This assignment explores how **Instruction-Level Parallelism (ILP)** techniques—such as pipelining, branch prediction, superscalar execution, and simultaneous multithreading (SMT)—affect processor performance. Using the **gem5 simulator**, we run controlled experiments with a simple workload (`hello` program) and collect pipeline traces, performance statistics, and visualizations.

## 📂 Repository Layout

```
assignment4/
│
├── results/          # Raw simulation output files (.txt) with stats
│   ├── base_pipeline_simulation_result.txt
│   ├── base_pipeline_simulation_trace.txt
│   ├── multi_threading_result.txt
│   ├── super_scalar_result.txt
│   └── tournament_predictor_result.txt
│
├── screenshots/      # Screenshots of simulation runs & visualizations
│   ├── pipeline_simulation_visual.png
│   ├── pipeline_simulation_run.png
│   ├── pipeline_simulation_result.png
│   ├── tournament_predictor_run.png
│   ├── tournament_predictor_result.png
│   ├── multithreading_run.png
│   ├── multithreading_result.png
│   └── super_scalar_result.png
│
├── tables/           # Processed CSV tables for easier reporting
│   ├── branch_prediction_baseline.csv
│   └── multiple_issue_smt.csv
│
└── README.md         # This file
```

## ⚙️ How to Reproduce

### 1. Baseline Pipeline Simulation

```bash
./build/X86/gem5.opt configs/deprecated/example/se.py \
  --cpu-type=X86MinorCPU --caches \
  -c tests/test-progs/hello/bin/x86/linux/hello
```

Generates baseline pipeline stats (IPC, cycles, latency).

### 2. Branch Prediction

Static Predictor:

```bash
./build/X86/gem5.opt configs/deprecated/example/se.py \
  --cpu-type=X86MinorCPU --caches --bp-type=StaticBP \
  -c tests/test-progs/hello/bin/x86/linux/hello
```

Tournament Predictor:

```bash
./build/X86/gem5.opt configs/deprecated/example/se.py \
  --cpu-type=X86MinorCPU --caches --bp-type=TournamentBP \
  -c tests/test-progs/hello/bin/x86/linux/hello
```

### 3. Superscalar (Multiple Issue)

```bash
./build/X86/gem5.opt configs/deprecated/example/se.py \
  --cpu-type=X86O3CPU --caches \
  -c tests/test-progs/hello/bin/x86/linux/hello
```

*(By default O3 supports wider issue; measured IPC increase recorded in results.)*

### 4. Simultaneous Multithreading (SMT)

```bash
./build/X86/gem5.opt configs/deprecated/example/se.py \
  --cpu-type=X86O3CPU --caches --smt --num-cpus=2 \
  -c tests/test-progs/hello/bin/x86/linux/hello
```

Runs two threads sharing a single core.


## 📊 Results

* **Tables:** See `tables/branch_prediction_baseline.csv` and `tables/multiple_issue_smt.csv` for collected IPC, cycles, and misprediction counts.
* **Screenshots:** Pipeline traces, predictor runs, and SMT runs are in `screenshots/`.
* **Raw Stats:** Full stats dumps are stored in `results/`.


## 🔍 Key Takeaways

* **Baseline Pipeline:** Establishes reference IPC (\~0.1 with `hello`, due to its small footprint).
* **Branch Prediction:** No visible difference on `hello` (too few branches), but real workloads benefit.
* **Superscalar:** Wider issue width shows modest IPC gain if independent instructions exist.
* **SMT:** Improves throughput by overlapping threads, though per-thread IPC remains limited.

## 📖 References

* Hennessy, J. L., & Patterson, D. A. (2019). *Computer Architecture: A Quantitative Approach* (6th ed.). Morgan Kaufmann.
* gem5 Simulator. (2025). *gem5 Documentation*. Retrieved from [https://www.gem5.org](https://www.gem5.org)
