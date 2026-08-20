# FPGA-Accelerated ML Inference for Real-Time Vision

Undergraduate research on hardware acceleration of machine learning inference — building an FPGA pipeline in C++ and OpenCL through High-Level Synthesis, then benchmarking the same model across CPU, CUDA GPU, and FPGA backends to characterize where each accelerator wins.

Lehigh University · High-Performance Systems · Feb 2026 – Present

---

## Results

| Metric | Value |
|---|---|
| End-to-end latency | **8.1 ms** |
| Sustained throughput | **125 FPS** |
| Clock frequency | 150 MHz |
| Latency vs. baseline | **35% lower** |
| Energy per inference | **6.4× lower** |
| Energy efficiency gain | **32%** |
| Resource utilization | 18% LUT · 12% FF · 8% BRAM · 15% DSP |

8.1 ms at 125 FPS means the pipeline sustains a full frame rate with latency well inside a real-time control budget — and it does so using under a fifth of the fabric, leaving headroom for other logic on the same device.

---

## The question

Given a trained model and a real-time vision workload, which accelerator should you deploy on — and what does the choice cost you?

That question is usually answered with vendor benchmarks that aren't comparable: different models, different batch sizes, different measurement methodology. This work answers it directly by holding the model fixed and varying only the backend.

---

## Method

### Common evaluation harness

The same model runs on CPU, CUDA GPU, and FPGA through a shared harness that measures latency, throughput, and energy per inference identically across all three. Without that, cross-platform numbers aren't comparable — most published comparisons differ in more variables than they control.

### HLS implementation

The FPGA pipeline is written in C++ and OpenCL and synthesized to RTL through High-Level Synthesis rather than hand-written HDL. The trade is less control over generated hardware in exchange for a much tighter iteration loop — which is the binding constraint when the goal is exploring architectural variants rather than shipping one.

### Optimization

Throughput on an FPGA is governed by the **initiation interval** — how many cycles pass before the pipeline can accept new input. Two transformations reduced it:

**Loop pipelining** overlaps loop iterations so successive inputs enter before earlier ones finish, turning a sequential loop into a hardware pipeline.

**Array partitioning across block RAM** splits arrays across separate BRAM banks. This matters because a single BRAM has limited ports: if a pipeline stage needs several array elements in the same cycle, a monolithic array serializes those accesses and stalls the pipeline regardless of how well it's otherwise structured. Partitioning removes that bottleneck at the cost of more distributed memory.

Both trade area for throughput — hence tracking resource utilization alongside performance.

---

## Why this comparison matters

For deployment on mobile and embedded robotic platforms, energy per inference is a first-class constraint rather than a footnote. A 6.4× reduction translates directly into mission time on a battery-powered system. Latency determines whether a perception pipeline can close a control loop at all; throughput determines whether it keeps up with the camera.

The three-way comparison makes those trade-offs explicit rather than assumed.

---

## Stack

C++ · OpenCL · High-Level Synthesis · FPGA · CUDA · Linux

---

## Author

**Wiesmes Antwi** — B.S. Electrical Engineering, Lehigh University
[LinkedIn](https://linkedin.com/in/wiesmes) · wwa228@lehigh.edu
