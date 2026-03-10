# gogo.json — Qwen VL Prompt Enhancer

> Полная схема всех нод → [scheme.md](scheme.md) | Mindmap → [gogo_markmap.md](gogo_markmap.md)

## Назначение
Генерация промптов через LLM с текстовым IP Adapter эффектом.
Пишешь 2-3 слова + кидаешь ref-картинку → получаешь полный booru промпт.

## Пайплайн
```
n182 [DPRandomGenerator] ──→ n87 [StringConcatenate b] ← n71 [TriggerWordToggle a]
                                      ↓
                          n93 [RegexReplace] → n180 [Reroute] → n178:user_prompt_input
n189 [LoadImage ref] → n192 [ImageScale 1024px] ──────────────→ n178:images
n177 [LLM_api_loader] ────────────────────────────────────────→ n178:model
                                   n178 [LLM Qwen3-VL] → n181 [ImpactSwitch]
n49 [extra booru tags] ──────────────────────────────────────→ n191 [Concat a]
                                   n181 ──────────────────────→ n191 [Concat b]
                                   n191 → n70 [CLIPTextEncode] → CLIP → KSampler
```

## Ключевые ноды
| Нода | Тип | Функция |
|------|-----|---------|
| n49 | ImpactWildcardProcessor | extra booru теги (приоритет выше Qwen) |
| n71 | TriggerWordToggle | LoRA триггеры → string_a в n87 |
| n87 | StringConcatenate | склейка: триггеры + юзер текст |
| n93 | RegexReplace | фильтр текста перед Qwen |
| n177 | LLM_api_loader | LM Studio: qwen3-vl-8b-nsfw-caption-v4.5 |
| n178 | LLM | Qwen3-VL генератор тегов |
| n180 | Reroute | n93 → n178:user_prompt_input |
| n181 | ImpactSwitch | select=1 прямой / select=2 Qwen |
| n182 | DPRandomGenerator | ввод текста пользователя |
| n189 | LoadImage | reference image для Qwen |
| n191 | StringConcatenate | n49 (a) + n181 (b) → CLIP |
| n192 | ImageScale | ресайз ref image до 1024px |

## Режимы
| Режим | n181 select | Где писать |
|-------|-------------|-----------|
| Qwen ON + ref image | 2 | n182: 2-3 слова; n189: картинка |
| Qwen ON без картинки | 2 | n182: описание текстом |
| Прямой (без Qwen) | 1 | n49: полный booru промпт |

## n178 [LLM] — параметры
- system_prompt: Booru-engineer + конфликт-резолюция (НЕ МЕНЯТЬ)
- user_prompt: пустой (иначе баг "你好")
- temperature: 0.4 (↑ больше с картинки, ↓ точнее текст)
- max_length: 1920 токенов

## Логика конфликт-резолюции (system_prompt)
1. Parse user_prompt_input → LOCKED атрибуты
2. Analyze ref image → все атрибуты
3. Конфликт с LOCKED → DROP (image версия), оставить LOCKED
4. Нет конфликта → ADD атрибут с картинки

## Save chain (n112)
```
n98 [VAEDecode] → n36 [FaceDetailerPipe bypass] → n92 [easy hiresFix bypass]
→ n125 [ImpactSwitch] → n117 [FBCNN bypass] → n132 [imageRemBg bypass]
→ n134 [ImageCompositeMasked bypass] → n112 [ImageSaver]
```
🔴 n122 [PrimitiveInt] должен быть АКТИВЕН → n125 получает select → цепочка работает

## LoRA в gogo
- n50 [LoraManager]: добавлять через UI, не сохраняется в JSON → добавлять каждый раз после reload
- n71 [TriggerWordToggle]: включать нужные группы триггеров вручную

## Типичные проблемы
| Симптом | Причина | Фикс |
|---------|---------|------|
| Все превью error (NoC) | n122 в bypass или Qwen упал | Активировать n122; уменьшить ref image |
| Не те теги одежды | Qwen неточно переводит | Дописать в n49 нужные теги как override |
| LoRA не работает | Не добавлены в n50 после reload | Добавить в UI + Ctrl+S |
| Баг "你好" | n178 user_prompt не пустой | Очистить виджет user_prompt |
| Картинка ломает Qwen | Ref image слишком большая | n192 фиксит (1024px ресайз) |
