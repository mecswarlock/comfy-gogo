# MecsAdvancedTIPOALT_V31 04.03.26.json — Основной воркфлоу

## Назначение
Полный пайплайн SDXL генерации: prompt → generation → hires → detailers → upscale → save.

## Базовый пайплайн
```
n182 [DPRandomGenerator] → n49 [WildcardProcessor] → n87 [StringConcatenate]
→ n93 [RegexReplace] → n181 [ImpactSwitch LLM] → n70 [CLIPTextEncode]
n75 [CheckpointLoader] → n50 [LoraManager] → n67 [CLIPSetLastLayer]
→ n99 [KSampler] → n98 [VAEDecode] → n35 [FaceDetailer] → n112 [ImageSaver]
```

## Группы (основные)
| Группа | Ноды | Статус |
|--------|------|--------|
| VPred | n60, n64 | ⬜ bypass |
| Epsilon/CFG | n72, n73 | n73 ✅ активен |
| LLM | n177-182 | ✅ активны |
| HiresFix 1 | n91 [easy hiresFix] | ⬜ bypass |
| HiresFix 2 | n92 | ⬜ bypass |
| Upscaler | n114 [UpscaleModelLoader], n115 [UltimateSDUpscale] | ⬜ bypass |
| Detailer Settings | n30, n56, n61, n63 | ✅ активны |
| ADetailer Face | n14, n35 | n35 ✅ |
| ADetailer Hand/Body/NSFW/Eyes | n11-n13, n15 | ⬜ bypass |
| IPAdapter Style | n105, n153 | ⬜ bypass |
| IPAdapter FaceID | n163 | ⬜ bypass |

## Ноды sampling
| Нода | Тип | Статус |
|------|-----|--------|
| n60 | ModelSamplingDiscrete | ⬜ bypass (eps) |
| n64 | Mahiro | ⬜ bypass |
| n72 | EpsilonScaling | ⬜ bypass |
| n73 | CFGZeroStar | ✅ активен |
| n99 | KSampler основной | ✅ |
| n57 | KSampler рефайнмент | ⬜ bypass |

## Детейлеры
| Нода | Тип | Модель | Статус |
|------|-----|--------|--------|
| n35 | FaceDetailerPipe | bbox/face_yolov9c.pt | ✅ |
| n33 | BodyDetailer | segm/yolo11m-seg.pt | ⬜ |
| n32 | HandDetailer | bbox/hand_yolov9c.pt | ⬜ |
| n36 | EyesDetailer | segm/Eyeful_v2-Paired.pt | ⬜ |
| n34 | NSFWDetailer | segm/ntd11_anime_nsfw_segm_v5-variant1.pt | ⬜ |
| n188 | Mask-based FaceDetailer | — | ⬜ |
- n30 [ToDetailerPipe]: ВСЕГДА активен
- n56 [DifferentialDiffusion]: ВСЕГДА активен

## IP Adapters
| Нода | Тип | Модель | Статус |
|------|-----|--------|--------|
| n105 | StyleComposition | noobIPAMARK1_mark1.safetensors | ⬜ |
| n153 | Advanced | noobIPAMARK1_mark1.safetensors | ⬜ |
| n163 | FaceID | ip-adapter-faceid-plusv2_sdxl.bin | ⬜ |
- Управление: Fast Groups Bypasser n53 (цвет #3f789e)
- CLIPVision n172 → CLIP-ViT-H-14 | n135 → CLIP-ViT-bigG-14

## Upscale (группа Upscaler)
- n114 [UpscaleModelLoader] + n115 [UltimateSDUpscale] — тайловый апскейл
- n118 [ControlNetLoader] + n157 [Canny] + n129 [ControlNetApply] — опц. ControlNet ⬜
- n113 [ImageSaver #2] — дублирующий сейвер ⬜

## Preview (rgthree Image Comparer n80-85)
- n80: before/after Mask-based Detailer
- n81-n85: before/after каждого ADetailer
- Показывают error если FaceDetailerPipe bypass не проходит slot 1 → не критично

## Save chain → n112 [ImageSaver]
```
n98 [VAEDecode] → n91 bypass → n92 bypass → n125 [ImpactSwitch n122 select]
→ n117 [FBCNN bypass] → n132 [imageRemBg bypass] → n134 [Composite bypass] → n112
```
🔴 n122 [PrimitiveInt] АКТИВЕН (mode=0) обязательно

## Управление bypass
```bash
# Все группы + ноды
curl -s http://192.168.31.103:8188/claude-code/groups
# Только bypassed ноды
curl -s http://192.168.31.103:8188/claude-code/node-modes
```
- MCP: `edit_graph action="set" node_id="X" property="mode" value=0/4`
