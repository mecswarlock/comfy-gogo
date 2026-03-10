# Mecs 05.03.26 — Основной рабочий воркфлоу

## Назначение
Полный SDXL пайплайн с активными IPA, ControlNet, OpenPose, HiresFix, mask-based Detailer.
Более полная версия чем MecsAdvancedTIPOALT_V31 — больше активных групп по умолчанию.

## Статус групп по умолчанию
| Группа | Статус | Ноды |
|--------|--------|------|
| Control Center | ✅ активна | n49/51/70/74/75/87/93/97/99/98/50/71 ... |
| Comfyui_LLM group | ✅ активна | n177/178/180/181 |
| IP-Adapter Style & Composition | ✅ активна | 7 нод |
| Any ControlNet | ✅ активна | 7 нод |
| OpenPose | ✅ активна | 6 нод |
| Hires PreDetailer | ✅ активна | n91 [easy hiresFix] |
| Detailer (mask-based) | ✅ активна | n23/24/26 |
| Detailer Settings | ✅ активна | 9 нод |
| Epsilon Scaling | ✅ активна | 1 нода |
| CFGZeroStar | ✅ активна | 1 нода |
| CLIP Skip | ✅ активна | 1 нода |
| 2ndSampler | ⬜ bypass | 3 ноды |
| Upscaler | ⬜ bypass | 9 нод |
| IP-Adapter FaceID | ⬜ bypass | 6 нод |
| IP-Adapter Advanced | ⬜ bypass | 5 нод |
| Face/Hand/Body/NSFW/Eyes ADetailer | ⬜ bypass | по 3 ноды |
| Regional Prompting | ⬜ bypass | 18 нод |
| VPred Model? | ⬜ bypass | 2 ноды |
| QwenVL Enhancer | ⬜ bypass | n8 (требует llama_cpp — не установлен) |
| Apply Signature | ⬜ bypass | 15 нод |
| Remove Background | ⬜ bypass | 2 ноды |
| Color Match | ⬜ bypass | 3 ноды |

## Control Center — ключевые ноды
| Нода | Тип | Функция |
|------|-----|---------|
| n49 | ImpactWildcardProcessor | POSITIVE промпт |
| n51 | ImpactWildcardProcessor | NEGATIVE промпт |
| n50 | LoraManager | загрузка LoRA |
| n71 | TriggerWordToggle | LoRA триггеры |
| n87 | StringConcatenate | склейка текста |
| n93 | RegexReplace | фильтр |
| n70 | CLIPTextEncode | Positive CLIP |
| n74 | CLIPTextEncode | Negative CLIP |
| n75 | CheckpointLoaderSimple | модель |
| n99 | KSampler | основная генерация |
| n98 | VAEDecode | декодирование |
| n53 | Fast Groups Bypasser | управление группами |
| n90 | Seed | сид |
| n62 | easy int | Width |
| n65 | easy int | Height |
| n100 | WidgetToString | ⬜ bypass (крашит API) |

## Comfyui_LLM group (ноды 177-181)
- n177 [LLM_api_loader] → qwen3-vl-8b, LM Studio http://192.168.31.103:1234/v1/
- n178 [LLM] → Qwen3-VL генератор тегов
- n180 [Reroute] → n93 → n178:user_prompt_input
- n181 [ImpactSwitch] → select=? (1=прямой промпт, 2=Qwen output)
- ⚠️ В этом воркфлоу нет n182 [DPRandomGenerator] — текст идёт напрямую в n49

## QwenVL Enhancer (n8) vs Comfyui_LLM group
| | QwenVL Enhancer (n8) | Comfyui_LLM group |
|--|--|--|
| Статус | ⬜ bypass | ✅ активна |
| Тип | AILab_QwenVL GGUF | LLM ноды (LM Studio API) |
| Требует | llama_cpp (НЕ установлен) | LM Studio запущен |
| Использовать | нельзя | ✅ |

## Detailer (mask-based) — активен по умолчанию
```
n23 [SolidMask] → n24 [MaskToSEGS] → n26 [DetailerForEach]
```
- Детейлит всё изображение (не зоны)
- denoise=0.42, sampler=dpmpp_2m/karras
- Для ADetailer (Face/Hand/etc) → включать группы ADetailer отдельно

## Fast Groups Bypasser (n53)
- Управляет группами цвета `#3f789e`
- Основной способ включения/выключения блоков

## Отличия от MecsAdvancedTIPOALT_V31
| Функция | MecsAdvancedTIPOALT | Mecs 05.03.26 |
|---------|---------------------|----------------|
| IPA Style | ⬜ bypass | ✅ активен |
| ControlNet | ⬜ bypass | ✅ активен |
| OpenPose | ⬜ bypass | ✅ активен |
| HiresFix | ⬜ bypass | ✅ активен |
| Mask Detailer | ⬜ bypass | ✅ активен |
| Regional Prompting | нет | ⬜ bypass |
| DPRandomGenerator | n182 ✅ | нет |
| LLM группа | ✅ | ✅ |
