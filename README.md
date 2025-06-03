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

---

## 📄 Citation

If you use TPU-Gen in your research, please cite the following paper:

> **TPU-Gen: LLM-Driven Custom Tensor Processing Unit Generator**  
> Deepak Vungarala, Mohammed Elbtity, Kartik Pandit, Sumiya Syed, Sakila Alam, Arnob Ghosh, Ramtin Zand, Shaahin Angizi  
> *International Conference on LLM-Aided Design (ICLAD), 2025*

📄 [Read on arXiv](https://arxiv.org/abs/2503.05951)

```bibtex
@inproceedings{vungarala2025tpugen,
  title     = {TPU-Gen: LLM-Driven Custom Tensor Processing Unit Generator},
  author    = {Vungarala, Deepak and Elbtity, Mohammed and Pandit, Kartik, and Syed, Sumiya and Alam, Sakila and Ghosh, Arnob and Zand, Ramtin and Angizi, Shaahin},
  booktitle = {Proceedings of the International Conference on LLM-Aided Design (ICLAD)},
  year      = {2025},
  url       = {https://arxiv.org/abs/2503.05951}
}
```

### 🚀 Experiment Workflow

This section provides a **step-by-step guide** to run the full TPU-Gen pipeline, from environment setup to Verilog generation and validation.


### 🔁 Step 1: Clone the Repository

Start by cloning the repository to your local machine:

```bash
git clone https://github.com/ACADLab/TPU_Gen.git
cd TPU_Gen
```

### 🐍 Step 2: Create and Activate Conda Environment

We recommend using a Conda environment to manage dependencies cleanly:

```bash
conda create -n tpu_gen_env python=3.10 -y
conda activate tpu_gen_env
```

---

### 📦 Step 3: Install Required Python Dependencies

Install all required libraries using the provided `requirements.txt` file:

```bash
pip install -r requirements.txt
```

> ✅ If any versions in `requirements.txt` are outdated, feel free to upgrade them manually using `pip install --upgrade`.

---

### 💬 Step 4: Format the Input Prompt

Use our prompt template to generate a well-structured input for the LLM:

```text
Generate Verilog header file based on the following description and metrics:
Description: This is the Verilog Header file that contains the design of 4x4 systolic array implementation. It contains the following BAM approximate multiplier, with the following LZTA approximate adder. It has the support of the following 32 dataflow as input, and supports input weights 7 bits of integer. With a low precision multiplier coefficient of 2 employed in this device.
Metrics: Area: 95194, WNS: -55.212, Total Power: 1.36e-02
```

This prompt format ensures that the model receives all necessary context for code generation.

---

### 🧠 Step 5: Train the Model (Optional)

You can fine-tune one of our supported LLMs (e.g., CodeLlama-7B, CodeQwen1.5-7B, StarCoder2-7B) on our curated dataset.

#### Dataset Versions Provided:

* `train.json` / `test.json`: Full version for high-resource systems.
* `beta_train.json` / `beta_test.json`: Smaller version for limited-resource setups.

> The data is already preprocessed and structured in the required format. Simply plug it into your training loop.

---

### 🏗️ Step 6: Run Inference

Once trained, run inference using a prompt like the one above. The model should generate a structured Verilog header output like:

```verilog
`define DRUM_APTPU //APMUL
`define ROUN_WIDTH 1
`define NIBBLE_WIDTH 4
`define DW 16
`define WW 5
`define M 6
`define N 6
`define MULT_DW 8
`define ADDER_PARAM 16
`define VBL 16
`define MHERLOA  //APADDER
`ifdef NORMAL_TPU
    `define ACCURATE_ACCUMULATE
`endif
```

This is a partial configuration file describing a custom TPU design.

---

### 🔗 Step 7: Pass Output to RAG Pipeline

Feed the generated Verilog header file into the RAG (Retrieval-Augmented Generation) pipeline:

#### What the RAG Pipeline Does:

1. **Converts** the output into a `.vh` file.
2. **Identifies** both default and non-default modules.
3. **Matches** module names against our complete RTL design repository.
4. **Fetches** the required `.v` files.
5. **Builds** the complete final design inside `final.vh/`.

---

### ✅ Step 8: Design Validation

Once your full design is assembled:

* You can run **simulation or synthesis** using tools like ModelSim, Vivado, or Yosys.
* If errors are found, modify the prompt or fine-tune the model further and **re-run the pipeline**.

---

This completes the **TPU-Gen end-to-end flow**: from input prompt to validated Verilog design.

> 📘 For more details, refer to our paper: [TPU-Gen on arXiv](https://arxiv.org/pdf/2503.05951)


---

### 🧰 Hardware Requirements

To reproduce the full training pipeline, we recommend the following setup:

* ✅ **GPU**: 4× NVIDIA A100 (80 GB each)
* ✅ **VRAM**: Minimum 80 GB per GPU (for full dataset training)
* ✅ **RAM**: At least 128 GB system memory
* ✅ **Storage**: Minimum 300 GB free (for datasets, checkpoints, and model weights)
* ✅ **CUDA**: Compatible with PyTorch ≥ 2.0 (CUDA 11.8 or newer)

> ⚠️ **Note**: If you have limited resources, use our `beta_train.json` and `beta_test.json` datasets. These are designed for training on single-GPU or lower-memory environments.

---


