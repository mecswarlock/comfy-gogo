# gogo.json — Полная схема пайплайна

> **Основа:** [AdvancedV31 by Mecsylia](https://civitai.com/articles/17339) — расширенный SDXL воркфлоу.
> gogo — его упрощённая версия с акцентом на Qwen VL как "текстовый IP Adapter".
>
> **Для ChatGPT / Grok / Gemini:** ComfyUI — это визуальный редактор нейросетевых пайплайнов.
> Нода = один блок обработки (например, загрузка модели, генерация, сохранение).
> Группа = набор нод для одной задачи (детейлер, upscaler и т.д.), можно включить/выключить одной кнопкой.
> Bypass = нода/группа пропускается, данные проходят насквозь без обработки.
>
> Связанный файл: [gogo_markmap.md](gogo_markmap.md)

---

## Модели, используемые в воркфлоу

| Категория | Модель | Назначение |
|-----------|--------|-----------|
| **Checkpoint (основная)** | любой SDXL / Illustrious чекпойнт | база для генерации (стиль, качество) |
| **LoRA** | добавляется вручную через UI | персонажи, стили, NSFW (Frieren и др.) |
| **LLM (Qwen)** | qwen3-vl-8b-nsfw-caption-v4.5 | генерирует booru теги по тексту + картинке |
| **IP Adapter** | noobIPAMARK1_mark1.safetensors | перенос стиля/композиции с reference image |
| **IP Adapter FaceID** | ip-adapter-faceid-plusv2_sdxl.bin | сохранение идентичности лица |
| **CLIP Vision (IPA)** | CLIP-ViT-H-14-laion2B-s32B-b79K | энкодер для IP Adapter (style) |
| **CLIP Vision (big)** | CLIP-ViT-bigG-14-laion2B-39B-b160k | энкодер для IP Adapter (advanced) |
| **YOLO (лица)** | bbox/face_yolov9c.pt | детектор лиц для ADetailer |
| **YOLO (руки)** | bbox/hand_yolov9c.pt | детектор рук |
| **YOLO (тело)** | segm/yolo11m-seg.pt | сегментация тела |
| **YOLO (глаза)** | segm/Eyeful_v2-Paired.pt | детектор глаз |
| **YOLO (NSFW)** | segm/ntd11_anime_nsfw_segm_v5 | NSFW сегментация |
| **Upscale model** | любой совместимый (4x и др.) | тайловый апскейл |
| **ControlNet** | OpenPose / AIO по задаче | контроль позы/контуров |

---

## Группы воркфлоу (Fast Groups Bypasser)

**Fast Groups Bypasser** — специальная нода-панель с кнопками. Каждая кнопка включает/выключает целую группу.
Цвет `#444` = фиксированно активна. Цвет `#3f789e` = управляется через Fast Groups Bypasser.

| Группа | Назначение | Статус |
|--------|-----------|--------|
| **Control Center** | Ядро пайплайна: ввод промпта, модель, LoRA, KSampler, сохранение | ✅ всегда |
| **Detailer Settings** | Общие параметры детейлеров: denoise, guide size, crop factor | ✅ всегда |
| **Image Saver** | Финальное сохранение на диск | ✅ всегда |
| **Comfyui_LLM group** | Qwen3-VL: читает ref image, генерирует теги с конфликт-резолюцией | ⬜ |
| **IP-Adapter Style & Composition** | Переносит стиль и/или композицию с reference image на генерацию | ⬜ |
| **IP-Adapter Advanced** | Расширенный IPA с тонкой настройкой весов | ⬜ |
| **IP-Adapter FaceID** | Сохраняет идентичность лица с фото | ⬜ |
| **Clip Vision** | Альтернатива IPA: unCLIP conditioning (стиль через CLIP эмбеддинги) | ⬜ |
| **OpenPose** | ControlNet для контроля позы персонажа | ⬜ |
| **Any ControlNet** | Произвольный ControlNet (Canny контуры, Depth глубина, Tile и др.) | ⬜ |
| **Regional Prompting** | Разные промпты для разных зон (небо=одно, персонаж=другое) | ⬜ |
| **Epsilon Scaling** | Улучшает контраст и детализацию при генерации | ⬜ |
| **CFGZeroStar** | Стабилизирует генерацию при высоком CFG scale | ⬜ |
| **CLIP Skip** | Пропускает последние слои CLIP (-2 стандарт для аниме моделей) | ⬜ |
| **VPred Model?** | V-prediction режим сэмплинга (нужен для некоторых моделей типа WD) | ⬜ |
| **Hires PreDetailer** | HiresFix перед детейлерами: апскейл + пересэмплинг для чёткости | ⬜ |
| **Hires PostDetailer** | HiresFix финальный после всех детейлеров | ⬜ |
| **2ndSampler** | Второй проход KSampler с низким denoise (~0.3) для рефайнмента | ⬜ |
| **Face ADetailer** | Перегенерация зон лиц через YOLO детектор (улучшает лица) | ⬜ |
| **Hand ADetailer** | Перегенерация зон рук (исправляет пальцы) | ⬜ |
| **Body ADetailer** | Перегенерация тела через сегментацию | ⬜ |
| **Eyes ADetailer** | Перегенерация глаз (детализация, блики) | ⬜ |
| **NSFW ADetailer** | Перегенерация NSFW зон | ⬜ |
| **Detailer** | Mask-based детейлер: берёт всё изображение как маску и детализирует | ⬜ |
| **Upscaler** | Ultimate SD Upscale — тайловый апскейл (режет на тайлы, генерирует каждый) | ⬜ |
| **Color Match** | Цветокоррекция output относительно input | ⬜ |
| **Compression Removal** | FBCNN: убирает JPEG артефакты сжатия | ⬜ |
| **Remove Background** | RemBg: удаляет фон, делает прозрачный PNG | ⬜ |
| **Apply Signature** | Накладывает вотермарку/подпись поверх картинки | ⬜ |
| **Load Image** | Загружает входное изображение для img2img режима | ⬜ |
| **Seperate VAE** | Отдельный VAE энкодер/декодер если модель идёт без VAE | ⬜ |
| **Preview Images** | rgthree Image Comparer: before/after для каждого детейлера | ⬜ |
| **QwenVL Enhancer** | Альтернативный Qwen через GGUF (требует llama_cpp — не установлен) | ⬜ |

---

## Полная схема — последовательный пайплайн

### Блок 1: Ввод пользователя → подготовка текста

```
[Prompt Input / DPRandomGenerator]
  Нода для ввода текста. Поддерживает wildcards — шаблоны вида {вариант1|вариант2},
  из которых случайно выбирается одно значение при каждой генерации.
  Пример: "фрирен в {юбке|платье}" → каждый раз разный предмет одежды.
       ↓ текст

[StringConcatenate — склейка текста]
  Принимает два текста (string_a и string_b) и соединяет их через разделитель.
  string_a = LoRA триггеры (TriggerWordToggle)  ← ВЫСОКИЙ ПРИОРИТЕТ, идут первыми
  string_b = текст пользователя
  Результат: "frieren, elf, <lora:frieren:0.8>, фрирен в юбке"
       ↓

[RegexReplace — фильтр текста]
  Regex (регулярные выражения) — паттерны для поиска и замены в тексте.
  Здесь: убирает нежелательные символы, форматирует текст перед подачей в Qwen.
  Например: лишние пробелы, кириллицу которую Qwen не понимает, и т.д.
       ↓ очищенный текст
```

### Блок 2: Qwen VL — "текстовый IP Adapter" (Comfyui_LLM group)

```
[LoadImage — референс картинка]
  Любое изображение для "вдохновения". Qwen возьмёт из него атрибуты сцены,
  которые НЕ конфликтуют с твоим текстом (фон, освещение, поза и т.д.)
       ↓
[ImageScale → 1024px]
  Ресайз до 1024px — иначе Qwen падает на больших изображениях.
       ↓ image

[LLM_api_loader] → модель qwen3-vl-8b через LM Studio API (http://localhost:1234)
       ↓ model

[LLM — Qwen3-VL-8B]
  Принимает: текст (= LOCKED атрибуты) + картинку (= soft контекст)
  Логика конфликт-резолюции:
    → Парсит твой текст → всё что написано = LOCKED (нельзя перезаписать)
    → Анализирует ref image → извлекает все атрибуты (одежда, фон, поза...)
    → Если атрибут из картинки конфликтует с LOCKED → дропает атрибут картинки
    → Если не конфликтует → добавляет в промпт
  Пример: ты написал "колготки", на картинке носки → берёт "колготки", носки дропает.
  Выводит: comma-separated booru теги (стандарт danbooru для аниме)
       ↓
[ImpactSwitch — переключатель]
  select=2 → использует Qwen output (режим с LLM)
  select=1 → пропускает Qwen, берёт текст напрямую (режим без LLM)
```

### Блок 3: Сборка финального Positive промпта

```
[ImpactWildcardProcessor — extra booru tags]  → string_a ← НАИВЫСШИЙ ПРИОРИТЕТ
  Сюда пишешь дополнительные теги которые ВСЕГДА должны быть в промпте.
  ImpactWildcardProcessor: поддерживает wildcards + синтаксис __файл__.
  Идёт в начало финального промпта (string_a).

[ImpactSwitch output — Qwen или прямой текст]  → string_b

[StringConcatenate "Qwen + Extra Tags"]
  Склеивает: extra tags (string_a) + Qwen output (string_b)
  = итоговый Positive промпт
  ⚠️ Эта нода должна быть АКТИВНА даже если LLM group в bypass —
     иначе триггеры LoRA не попадают в промпт!
       ↓ Positive prompt → CLIPTextEncode

[ImpactWildcardProcessor — NEGATIVE]
  Отдельный негативный промпт (что НЕ должно быть на картинке).
       ↓ Negative prompt → CLIPTextEncode
```

### Блок 4: Загрузка модели

```
[CheckpointLoaderSimple]
  Загружает основную модель (SDXL чекпойнт) — содержит MODEL + CLIP + VAE.
  MODEL — диффузионная сеть (генерирует).
  CLIP — текстовый энкодер (понимает промпт).
  VAE — энкодер/декодер между пикселями и латентным пространством.
       ↓ MODEL, CLIP, VAE

[LoraManager]
  Применяет стек LoRA поверх основной модели.
  LoRA = небольшой дообученный модуль для конкретного персонажа/стиля.
  ⚠️ Не сохраняется в JSON — добавлять вручную через UI после каждого reload!
       ↓ MODEL, CLIP

[CLIPSetLastLayer]  (CLIP Skip group)
  Пропускает последние N слоёв CLIP. -2 = стандарт для аниме SDXL моделей.
  Влияет на "понимание" текста моделью.
       ↓ CLIP → CLIPTextEncode
```

### Блок 5: Опциональные модификаторы модели

```
MODEL → [CFGZeroStar]        устраняет артефакты при CFG > 7
MODEL → [Epsilon Scaling]    масштабирует шум для лучшего контраста
MODEL → [VPred + Mahiro]     переключает в v-prediction режим сэмплинга

MODEL → [IPAdapterStyleComposition]  (IP-Adapter Style & Composition)
  Берёт стиль с одной картинки, композицию с другой → встраивает в MODEL.
  Работает без изменения промпта — "визуальный" контроль.

MODEL → [IPAdapterAdvanced]   тонкая настройка весов style/composition
MODEL → [IPAdapterFaceID]     фиксирует идентичность лица с reference фото
```

### Блок 6: Conditioning (условия генерации)

```
CLIP + Positive text → [CLIPTextEncode] → CONDITIONING_POS
CLIP + Negative text → [CLIPTextEncode] → CONDITIONING_NEG

Conditioning = закодированный промпт, влияет на направление диффузии.

CONDITIONING_POS → [ControlNetApplyAdvanced + OpenPose]
  Добавляет контроль позы: pose image обрабатывается OpenposePreprocessor,
  ControlNet направляет позу персонажа по скелету.

CONDITIONING_POS → [ControlNetApplyAdvanced + AnyControlNet]
  Любой другой ControlNet: Canny (контуры), Depth (глубина), Tile (детализация).

CONDITIONING_POS → [AttentionCouple]  (Regional Prompting)
  Разбивает изображение на зоны по маскам, каждой зоне свой промпт.
```

### Блок 7: Генерация изображения

```
[EmptyLatentImage]  ← Width × Height из Control Center
  Создаёт пустое латентное пространство — "холст" для диффузии.
       ↓

[KSampler — главный]
  Основная генерация: MODEL + CONDITIONING + LATENT → заполненное латентное.
  Параметры: steps, cfg, sampler, scheduler, denoise.
  cfg (Classifier-Free Guidance) — сила следования промпту (обычно 5-8).
       ↓ latent

[KSampler 2nd]  (2ndSampler group)
  Второй проход с низким denoise (~0.3) — рефайнмент без кардинального изменения.
       ↓ latent

[VAEDecode]
  Декодирует латентный вектор в пиксели (IMAGE).
       ↓ IMAGE
```

### Блок 8: Детейлеры (улучшение качества зон)

ADetailer = "Area Detailer". Находит зону (лицо, руки...) → вырезает → перегенерирует → вставляет обратно.

```
IMAGE → [easy hiresFix Pre]       апскейл + пересэмплинг перед детейлерами
      → [FaceDetailerPipe]        лица: YOLO bbox → crop → KSampler → paste
      → [FaceDetailerPipe]        руки: то же самое
      → [FaceDetailerPipe]        тело / глаза / NSFW
      → [DetailerForEach]         mask-based: вся картинка как маска, общая детализация
      → [easy hiresFix Post]      финальный апскейл
       ↓ IMAGE improved
```

### Блок 9: Финальная обработка и сохранение

```
IMAGE → [UltimateSDUpscale]     тайловый апскейл: делит на тайлы, каждый генерирует
      → [imageColorMatch]        цветокоррекция
      → [FBCNN]                  удаление JPEG артефактов
      → [imageRemBg]             удаление фона
      → [ImageCompositeMasked]   вотермарка/подпись
      → [ImageSaver]             сохранение на диск
```

> ℹ️ n122 [PrimitiveInt] в Color Match группе: при bypass n125 ImpactSwitch дефолтится на input1.
> Color Match можно байпасить — save chain не ломается.

---

## Минимальная рабочая схема ✅

Подтверждена 2026-03-10. Всё остальное — bypass.

```
[Prompt Input]  ← пишешь текст / теги здесь (или в extra booru tags ниже)
       ↓
[TriggerWordToggle] → [StringConcatenate] ← LoRA триггеры
                             ↓
                      [RegexReplace]
                             ↓
[extra booru tags] → [StringConcatenate "Qwen+Extra"] ← ⚠️ ДОЛЖЕН БЫТЬ АКТИВЕН
                             ↓
                      [CLIPTextEncode]
                             ↓
[CheckpointLoader] → [LoraManager] → [KSampler] → [VAEDecode] → [ImageSaver]
```

**Обязательно активно:**
- Control Center полностью
- StringConcatenate "Qwen + Extra Tags" — иначе LoRA триггеры не попадают в промпт
- LoRA: добавить вручную в LoraManager после каждого reload страницы

**Можно держать в bypass:**
- Весь Comfyui_LLM group (Qwen) — пишешь промпт сам в extra booru tags или Prompt Input
- Все детейлеры, upscaler, IP Adapters, ControlNet

---

## Ключевые особенности gogo

| Концепция | Пояснение |
|-----------|-----------|
| "Текстовый IP Adapter" | Обычный IP Adapter берёт стиль с картинки напрямую. Здесь Qwen VL описывает картинку словами и добавляет только то, что не конфликтует с твоим запросом. |
| booru теги | Формат описания аниме: comma-separated теги (1girl, long hair, school uniform...) вместо предложений. Модели SDXL обучены на таком формате. |
| LoRA не сохраняется | LoraManager — UI-only нода. После F5/reload нужно заново добавлять LoRA через интерфейс. |
| Приоритет промпта | extra booru tags (начало) > Qwen output (конец). Что написано первым — сильнее влияет. |
| LLM group bypass | При bypass всей LLM группы — n196 StringConcatenate нужно вытащить наружу, иначе триггеры теряются. |
