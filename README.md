# comfy-gogo

> **An advanced, automated SDXL/Illustrious generation pipeline for ComfyUI, featuring multimodal LLM integration (Qwen-VL) and deep multi-tier IP-Adapter orchestration.**

`comfy-gogo` is a heavily optimized, production-ready evolution of the **AdvancedV31** framework. It bridges high-fidelity generative art with cognitive LLM reasoning, enabling automated tag generation, composition control, and self-correcting detailer loops.

![ComfyUI](https://img.shields.io/badge/ComfyUI-Advanced-blue)
![SDXL](https://img.shields.io/badge/SDXL-Illustrious-orange)
![LLM](https://img.shields.io/badge/LLM-Qwen--VL-green)
![License](https://img.shields.io/badge/License-MIT-brightgreen)

---

## ✨ Core Features

* **Qwen-VL Integration:** Vision-LLM nodes for autonomous booru-tag extraction from text prompts and reference images.
* **Multi-Tier IP-Adapter Orchestration:** Layered conditioning for Style, Composition, and FaceID to maintain extreme character consistency across seeds.
* **Fast Groups Bypasser:** Centralized dashboard panel for instantly toggling massive node groups (ControlNet, Detailers, Upscalers).
* **Advanced Detailer Pipeline:** Dynamic masking using YOLO-driven ADetailer for flawless face, hand, and texture reconstruction.
* **Automated Prompt Resolution:** Real-time prompt conflict handling and semantic enhancement powered by local LLM backends.
* **Agentic Ecosystem Ready:** Built to seamlessly interface with external orchestrators (**OpenClaw**) and local long-term memory layers ([agent-memory-core](https://github.com/satawarlock-a11y/agent-memory-core)).

## 📁 Repository Structure

* `gogo.json` — The master ComfyUI workflow layout (ready for drag-and-drop import).
* `agents_config.example.json` — Configuration blueprint for OpenClaw and memory layer integration.
* `scheme.md` — Technical specs of the generation pipeline and node routing.
* `gogo_markmap.md` — Mindmap architecture of active functional groups.

## 🛠 Tech Stack

* **Core Environment:** ComfyUI + Custom Nodes (Impact Pack, ComfyUI-LLM, IPAdapter, ControlNet)
* **Vision/Language Backend:** Qwen3-VL-8B / LM Studio API
* **Base Models:** SDXL / Illustrious architectures
* **Automation Hooks:** Python API & OpenClaw JSON payload routing

---
**Maintainer:** mecswarlock  
**License:** MIT
