# Character Bible Schema

本結構沿用 IP Asset Generator 的 IP Bible 欄位，增加 `reference_confidence`、`reference_sources`、`needs_confirmation` 與 `turnaround_requirements`，讓下游能分辨已鎖定內容與 AI 補完內容。

## 欄位規則

- `character_id`：穩定、短小的 kebab-case ID；未命名角色可先用 `character`。
- `reference_confidence`：正面圖清晰且特徵可見為 `high`；局部遮擋或畫質有限為 `medium`；只能辨識大致輪廓為 `low`。
- `reference_sources`：列出原始圖與補充圖，包含 `path`、`role`、`notes`。
- `identity_anchors`：每項包含 `id`、`description`、`priority`、`status`。只有 `confirmed` 的可觀察特徵可進入核心身份鎖定；`inferred` 必須標明是推測。
- `fixed_rules`：永遠不變的身份與視覺規則。未知背面細節不得放在這裡。
- `needs_confirmation`：尚未由使用者決定、但會影響轉面或後續生成的問題。
- `allowed_variations`：可依任務改變的動作、表情、場景、功能性道具、季節性配件等；三視圖本身不應使用這些變化。
- `forbidden_changes`：不得擅自更改的內容，尤其是物種／角色定位、比例、主色、永久標記、招牌配件與畫風。
- `output_requirements`：記錄輸出寬高、透明背景與是否保留原始正面圖，避免生成時遺失使用者的交付要求。

## 最小模板

```yaml
character_id: character
reference_confidence: medium
reference_sources:
  - path: original-front.png
    role: primary-front-reference
    notes: 使用者提供的正面圖
identity_anchors:
  - id: silhouette
    description: 從正面圖可觀察的整體輪廓
    priority: critical
    status: confirmed
fixed_rules:
  - 保留已確認的角色物種／角色定位與身體比例
needs_confirmation:
  - 背面服裝結構
allowed_variations:
  - primary_action
  - primary_emotion
  - scene
  - functional_props
forbidden_changes:
  - 改變身份錨點
  - 擅自新增 Logo、文字或永久標記
personality:
  confirmed: []
  inferred: []
story_role:
  confirmed: null
  inferred: null
world_setting:
  confirmed: []
  inferred: []
visual_language:
  medium: 從參考圖分析
  line: 待確認
  shape: 待確認
  shading: 待確認
  texture: 待確認
palette:
  confirmed: []
  inferred: []
materials: []
expression_vocabulary: []
costume:
  confirmed: []
  inferred: []
signature_accessories: []
output_requirements:
  width: 1024
  height: 1024
  transparent_background: false
  preserve_original_front: true
turnaround_requirements:
  views: [front, three_quarter_front, side, back]
  separate_images: true
  neutral_pose: true
  same_canvas_size: true
  same_baseline: true
  transparent_background: false
  unknown_detail_policy: needs_confirmation
```

不要為了填滿 schema 而創作故事、品牌事實或背面設計。`inferred` 是建議，不是永久設定；只有使用者確認後才可移至 `confirmed` 或 `fixed_rules`。
