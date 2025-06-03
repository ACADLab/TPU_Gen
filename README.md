# TPU-Gen: LLM-Driven Custom Tensor Processing Unit Generator

Accepted at ICLAD 2025

This repository hosts the official implementation, dataset, and experimental results for our paper:

"TPU-Gen: LLM-Driven Custom Tensor Processing Unit Generator" 
Authored by, Deepak Vungarala, Mohammed Elbtity, Kartik Pandit, Shaahin Angizi, Ramtin Zand, Arnob Ghosh, and collaborators.



## 🧠 TPU-Gen Framework Overview

TPU-Gen is the first end-to-end framework that uses fine-tuned LLMs with RAG (Retrieval-Augmented Generation) to generate both **exact and approximate TPU architectures**.

It consists of:

1. Prompt generation from user input.

2. LLMs enhanced with multi-shot learning.

3. RAG module for dependency fetching.

4. Automated design validation and PPA reporting.

### 📌 Motivation

Designing a **custom Tensor Processing Unit (TPU)** typically requires extensive hardware expertise and manual RTL development, which is:
- Time-consuming
- Error-prone
- Non-scalable for diverse DNN workloads

With the rise of **Large Language Models (LLMs)**, there's an opportunity to **automate hardware generation** by leveraging their reasoning and code synthesis capabilities. TPU-Gen bridges this gap by introducing a complete pipeline to automatically design **systolic-array-based TPUs** from high-level textual input.

---

### 🔍 Core Idea

**TPU-Gen** is an **LLM-driven hardware generation framework** that takes as input a *textual description* of a target DNN accelerator (e.g., “4x4 systolic array with 8-bit INT MAC units”) and outputs:
- A complete Verilog RTL header (`.vh`)
- Associated modules (`.v`)
- Synthesized hardware layout and PPA metrics

---

### 🔧 Key Components

#### 1. **Multi-Shot Prompting**
- Uses few-shot prompting with pre-defined examples to guide the LLM in generating accurate Verilog macros.

#### 2. **Retrieval-Augmented Generation (RAG)**
- Compares generated `.vh` macros with a library of existing Verilog modules.
- Fetches only the relevant `.v` files (e.g., `mux.v`, `fifo.v`, `decoder.v`) required to implement the described design.

#### 3. **Formal + Functional Validation**
- Validates generated RTL using simulation and formal verification.

#### 4. **PPA Evaluation**
- Synthesis with **Yosys**
- Layout with **OpenROAD**
- Outputs: **Power**, **Area**, **Delay**

---
# Framework:

<img width="712" alt="Framework" src="https://github.com/user-attachments/assets/b357482f-a1f4-4af8-96b1-ea663593c258" />

