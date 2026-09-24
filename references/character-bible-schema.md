# Character Bible Schema

本結構沿用 IP Asset Generator 的 IP Bible 欄位，增加 `asset_mode`、`reference_confidence`、`reference_sources`、`needs_confirmation` 與 `turnaround_requirements`，讓 2D 與 3D 下游都能分辨已鎖定內容與 AI 補完內容。

## 欄位規則

- `character_id`：穩定、短小的 kebab-case ID；未命名角色可先用 `character`。
- `asset_mode`：`2d`、`3d` 或 `both`。未指定時使用 `2d`；它只決定可選交接資訊，不改變四視角與身份一致性的核心要求。
- `reference_confidence`：正面圖清晰且特徵可見為 `high`；局部遮擋或畫質有限為 `medium`；只能辨識大致輪廓為 `low`。
- `reference_sources`：列出原始圖與補充圖，包含 `path`、`role`、`notes`。
- `identity_anchors`：每項包含 `id`、`description`、`priority`、`status`。只有 `confirmed` 的可觀察特徵可進入核心身份鎖定；`inferred` 必須標明是推測。
- `fixed_rules`：永遠不變的身份與視覺規則。未知背面細節不得放在這裡。
- `needs_confirmation`：尚未由使用者決定、但會影響轉面或後續生成的問題。
- `allowed_variations`：可依任務改變的動作、表情、場景、功能性道具、季節性配件等；三視圖本身不應使用這些變化。
- `forbidden_changes`：不得擅自更改的內容，尤其是物種／角色定位、比例、主色、永久標記、招牌配件與畫風。
- `output_requirements`：記錄輸出寬高、透明背景與是否保留原始正面圖，避免生成時遺失使用者的交付要求。
- `proportion_lock`：由 AI 從參考圖估算的相對比例與畫布對齊規則。它是柔性約束，不是精確量測；每份資料都要標示來源與可信度。
- `spatial_feature_contract`：只記錄跨視角容易出錯、且需要可驗證約束的結構或特徵。每項依其類型說明所在身體面、角色自身左右、連接方式、朝向、遮擋、重複數量、圖樣／材質連續性及逐視角規則。對尾巴、翅膀、角、辮子等延伸結構，還要記錄根部、可辨識轉折點、末端的角色自身座標與相對地標關係，讓各視角以同一條空間路徑投影，而非重新設計輪廓。未知值必須標為 `needs_confirmation` 或 `inferred`，不可偽裝成已確認設定。
- `downstream_handoff`：可選的媒材交接資訊。`two_d` 記錄可用素材目標；`three_d` 只記錄已確認或明確標為推測的立體結構、材質與可動部位。它不是建模、拓撲、骨架或動畫工作的授權，也不應逼迫 `2d` 使用者填寫。

## 最小模板

```yaml
character_id: character
asset_mode: 2d
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
spatial_feature_contract:
  - id: <feature-id>
    type: appendage|facial_feature|limb|accessory|costume|marking|other
    priority: critical
    status: confirmed|inferred|needs_confirmation
    character_side: left|right|center|bilateral|not_applicable
    spatial_definition:
      surface_or_body_region: <所在身體面或區域>
      attachment_or_boundary: <與身體、衣物或其他特徵的連接關係>
      direction_or_orientation: <朝向；不適用則填 not_applicable>
      path_in_character_space:
        root: <根部的角色自身位置；不適用則填 not_applicable>
        turning_points: [] # 由根部至末端依序列出可辨識轉折點
        endpoint: <末端的角色自身位置；不適用則填 not_applicable>
      landmark_relations: [] # 例如末端與哪個固定特徵同側／相對側；未知時標 needs_confirmation
      expected_occlusion: <誰遮住誰；不適用則填 not_applicable>
      continuity_rule: <圖樣、材質、色段、線條或輪廓如何跨視角保持連續>
    view_rules:
      front:
        expected_state: <可見性、數量與必要輪廓>
      three_quarter_front:
        expected_state: <可見性、數量與必要輪廓>
      side:
        expected_state: <可見性、數量與必要輪廓>
      back:
        expected_state: <可見性、數量與必要輪廓>
    fail_conditions:
      - <不可能的附著、遮擋或穿模>
      - <視角不符或必要特徵缺失>
      - <圖樣、材質、根部／轉折點／末端側別或相對地標關係無故跳變>
proportion_lock:
  source: ai-estimated-from-reference
  confidence: medium
  body_ratio:
    head_to_body: 約 1:3
    shoulder_width: 約 1.2 個頭寬
  canvas_alignment:
    character_height: 畫布高度約 82%
    feet_baseline: 距離畫布底部約 8%
  accessory_positions: []
  notes:
    - 以上為視覺估算，不是實際測量值
downstream_handoff:
  two_d:
    enabled: true
    targets: [stickers, scenes, comics]
  three_d:
    enabled: false
    targets: []
    spatial_structure:
      confirmed: []
      inferred: []
    material_notes:
      confirmed: []
      inferred: []
    movable_parts:
      confirmed: []
      needs_confirmation: []
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

當 `asset_mode: 3d` 或 `both` 時，將 `downstream_handoff.three_d.enabled` 設為 `true`，並僅填入可由參考圖觀察、使用者提供或明確標示為推測的資訊。不要為了填滿 schema 而創作故事、品牌事實、背面設計、立體厚度、拓撲或骨架規格。`inferred` 是建議，不是永久設定；只有使用者確認後才可移至 `confirmed` 或 `fixed_rules`。
