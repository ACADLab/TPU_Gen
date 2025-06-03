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


### 🧪 Supported CNN Models

The framework has been evaluated on several convolutional neural networks, showing generality and scalability:

- **LeNet** – Small, shallow network
- **ResNet18** – Medium complexity
- **ResNet56** – Deep residual model
- **VGG16** – Large, parameter-heavy CNN

Each model was mapped to a corresponding systolic array design using textual descriptions, which were then successfully synthesized and laid out using TPU-Gen.

---
# Benchmark Results:

| Architecture | Design Size | Power (W) | Area (µm²) | Delay (ns) | Outperforms Handcrafted |
|--------------|--------------|-----------|-------------|-------------|---------------------------|
| LeNet        | 4×4 INT8     | 0.036     | 32208       | 2.51        | ✅                        |
| ResNet18     | 8×8 INT8     | 0.158     | 56795       | 2.84        | ✅                        |
| ResNet56     | 16×16 INT8   | 0.631     | 193348      | 3.08        | ✅                        |
| VGG16        | 32×32 INT8   | 2.412     | 737172      | 3.38        | ✅                        |

> All synthesized using Yosys + OpenROAD on the Nangate45 library.


### 💡 Summary

TPU-Gen is the **first open-source framework** to enable:

- Natural language → Verilog generation
- LLM + RAG-based design validation
- RTL → GDS flow for real-world synthesis
- Automated PPA evaluation across DNN workloads

This positions TPU-Gen as a critical tool for hardware researchers, ML system designers, and EDA practitioners aiming to rapidly prototype and benchmark new accelerator designs.

## 📄 Paper Access

📝 This work was accepted at **ICLAD 2025**. You can read the full paper here:

📥 [Download PDF](https://arxiv.org/pdf/2503.05951)

or

🔗 [View on ICLAD Conference Website](https://iclad.ai/accepted-papers)
