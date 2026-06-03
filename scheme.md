# Technical Specification: comfy-gogo Generation Pipeline

This document details the internal execution layers of the `comfy-gogo` automated ComfyUI pipeline, its node routing logic, and external API hooks.

---

## 1. Core Workflow Pipeline

The processing lifecycle of a single generation event follows a structured multimodal layout:

```text
  [Raw User Input] + [Reference Images]
             │
             ▼
   ┌───────────────────┐
   │ Qwen-VL Vision    │ ──► Extracts high-fidelity Booru-tags
   │ Analytics Node    │ ──► Resolves prompt syntax conflicts
   └─────────┬─────────┘
             │
             ▼
   ┌───────────────────┐
   │ Conditioning Layer│ ◄── Managed via Fast Groups Bypasser Panel
   │ (IP-Adapter / CN) │ ──► Style, Composition, FaceID, and OpenPose
   └─────────┬─────────┘
             │
             ▼
   ┌───────────────────┐
   │  Advanced KSampler│ ──► Primary latent generation
   │ (SDXL/Illustrious)│ ──► Optimized scheduling & steps
   └─────────┬─────────┘
             │
             ▼
   ┌───────────────────┐
   │ Ultimate SD       │ ──► High-resolution HiresFix scaling
   │ Upscale Pipeline  │
   └─────────┬─────────┘
             │
             ▼
   ┌───────────────────┐
   │ YOLO Face/Hand    │ ──► Impact Pack automated detailer loops
   │ Detailer Nodes    │ ──► Dynamic masking for anatomy correction
   └─────────┬─────────┘
             │
             ▼
     [Final Enhanced Art]

2. Deep Dive: Subsystem Architecture
A. Automated Prompt Resolution (Vision LLM)

    Backend: Integrated with local model environments (like LM Studio hosting Qwen3-VL-8B).

    Logic: The system intercepts dirty or short user prompts, passes any reference image to the Vision model, and automatically builds optimized negative and positive tag structures specialized for SDXL Illustrious.

B. Multi-Tier Conditioning

    IP-Adapter: Uses separate weights for composition tracking, facial structure extraction (FaceID), and aesthetic style transfer.

    ControlNet: Includes parallel pipelines for OpenPose, Depth, and Canny, which can be dynamically bypassed instantly via the control group dashboard.

C. Anatomy Correction (Detailer)

    Uses automated YOLO v8 models to track facial geometry and hands in the post-sampling phase, running a localized sub-sampling loop inside masks to eliminate typical generative flaws.

3. Agentic Ecosystem Hooks

The repository exposes an agents_config.example.json schema. This structure allows autonomous orchestrators like OpenClaw to dynamically inject payload data directly into the ComfyUI API, turning this localized art pipeline into a stateful tool for multi-agent autonomous execution.
