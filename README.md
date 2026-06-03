# comfy-gogo

**Мощный автоматизированный SDXL-воркфлоу для ComfyUI** с интеграцией LLM (Qwen-VL) и продвинутыми IP-Adapter'ами.

Переработанная и оптимизированная версия **AdvancedV31** (Mecsylia) под модель **Illustrious** с акцентом на удобство и автоматизацию.

![ComfyUI](https://img.shields.io/badge/ComfyUI-Advanced-blue)
![SDXL](https://img.shields.io/badge/SDXL-Illustrious-orange)
![LLM](https://img.shields.io/badge/LLM-Qwen--VL-green)

## ✨ Основные возможности

- **Интеграция Qwen3-VL** — генерация качественных booru-тегов по тексту + reference-изображению
- **Многоуровневые IP-Adapter** (Style + Composition + FaceID)
- **Fast Groups Bypasser** — удобное управление группами одной панелью
- **Продвинутый Detailer** (маски + ADetailer с YOLO)
- **ControlNet** (OpenPose, Canny, Depth и др.)
- **HiresFix + Upscale**
- **Автоматическая обработка конфликтов** промптов через LLM
- Поддержка LoRA + Trigger Words

## 📁 Структура проекта

- `gogo.json` — основной воркфлоу (готов к импорту)
- `scheme.md` — подробная схема пайплайна
- `gogo_markmap.md` — mindmap структуры
- `Mecs_05.03.26.md` — описание активных групп
- `MecsAdvancedTIPOALT_V31.md` — исходная база

## 🎯 Цели проекта

Создать **удобный, гибкий и мощный** инструмент для художников и разработчиков, который сочетает:
- Силу ComfyUI
- Понимание изображений через LLM
- Тонкий контроль стиля и персонажей

В будущем планируется:
- Добавление памяти (long-term memory)
- Автоматизация через OpenClaw / агенты
- Создание полноценного agentic workflow

## 🚀 Как использовать

1. Скачай `gogo.json`
2. Импортируй в ComfyUI (Drag & Drop)
3. Загрузи нужный SDXL чекпоинт (рекомендуется Illustrious)
4. Настрой LoRA при необходимости
5. Используй **Fast Groups Bypasser** для быстрого включения/выключения функций

## 🛠 Стек

- **ComfyUI** + Custom Nodes (Impact Pack, ComfyUI-LLM, IPAdapter, etc.)
- **Qwen3-VL-8B** (через LM Studio)
- **Illustrious** / SDXL модели
- YOLO детекторы, ControlNet, Ultimate SD Upscale

---

**Автор:** mecswarlock  
**Статус:** активно развивается
