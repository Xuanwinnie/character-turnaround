---
name: character-turnaround
description: 從一張正面角色參考圖分析角色身份、建立可重複使用的 Character Bible，並在設定確認後生成正面、四分之三正面、側面、背面四張獨立角色參考圖。適用於角色、吉祥物、品牌 IP、遊戲、動畫與繪本角色的角色基準包；不適用於一般場景插畫或直接大量生成素材。
---

# Character Turnaround

把一張正面角色圖整理成可供後續 IP 素材生成使用的角色基準包。核心成果是：可編輯的 Character Bible、四張獨立視角圖，以及可追溯的 QA 紀錄。本 Skill 與 `ip-asset-generator` 透過相容欄位協作，不修改或覆蓋另一個 Skill。

## 入口與模式

先確認使用者提供可讀的正面角色圖。若沒有，要求一張正面參考圖，不能假裝已完成分析或生圖。若有多張圖，指定哪一張是主正面參考；其他圖只作補充來源。

依需求選擇：

- **快速角色基準圖**：角色設定已明確，仍要先整理身份鎖定並讓使用者確認，再生成四張圖。
- **角色設定規劃**：先輸出已確認、合理推測、待確認三類內容，等待確認後才建立正式 Bible 與生圖。
- **完整角色基準包**：除 Bible 與四張核心角色基準圖外，再提供表情、動作、服裝、配色與後續素材建議；MVP 不要自動生成這些延伸圖片。

使用者未指定模式時，採「角色設定規劃」。即使使用者要求直接快速出圖，也不可跳過確認關鍵未知細節。

## 分析規則

只把參考圖或使用者明確提供的資料寫成已確認事實。回應必須區分：

- **已確認**：可由圖像直接觀察或使用者明確說明的內容。
- **合理推測**：可作創作建議，但尚未成為固定設定。
- **待確認**：正面圖無法判斷的內容，尤其是後腦、衣服背面、配件背側、尾巴／翅膀背面、鞋子後側與隱藏結構。

至少擷取 3 個身份錨點，最好 4–6 個，最多 8 個；為每個錨點標示優先級。錨點應是跨視角仍可辨識的特徵，例如輪廓、臉型、眼睛、主色、永久標記或招牌配件。不得把推測的背面細節寫入 `fixed_rules`。

## 確認閘門

正式生成前，呈現簡潔確認卡，至少包含：角色身份、身份錨點、固定外觀、服裝／配件、色彩與畫風、待確認的側面／背面細節，以及是否允許依整體風格合理補完。使用者未確認前，只能提供規劃與 prompt 草稿，不得生成正式基準圖。

若未知細節會影響角色識別，提供兩個選項：使用者補充側面／背面參考，或明確允許保守補完；後者要在 Bible 與 QA 標記為推測／AI 補完。

## Character Bible

建立 `character-bible.yaml`，欄位與 IP Asset Generator 相容。完整欄位說明見 [character-bible-schema.md](references/character-bible-schema.md)。至少包含：

```yaml
character_id: <kebab-case-id>
reference_confidence: high|medium|low
reference_sources: []
identity_anchors: []
fixed_rules: []
needs_confirmation: []
allowed_variations: []
forbidden_changes: []
personality: {}
story_role: {}
world_setting: {}
visual_language: {}
palette: []
materials: []
expression_vocabulary: []
costume: {}
signature_accessories: []
output_requirements:
  width: <pixels>
  height: <pixels>
  transparent_background: false
  preserve_original_front: true
turnaround_requirements:
  views: [front, three_quarter_front, side, back]
  separate_images: true
  neutral_pose: true
```

未知欄位使用 `needs_confirmation` 或明確的 `status`，不要用看似確定的空泛敘述填滿。`allowed_variations` 只列可變項目；物種／角色定位、臉型、身體比例、主色、永久標記、招牌配件與視覺語言原則上不得變更。

## 角色基準圖生成

核心輸出是四張獨立圖片：正面、四分之三正面、側面、背面。先保留原始正面圖。若使用者要求保留它，`character-front.png` 可直接引用原始檔，不必重新生成；若要求統一畫布或重新繪製，才生成新的正面圖。其餘三個視角必須各自獨立生成，每張只含一個角色。

四張圖共同鎖定：同一角色、同一比例、同一鏡頭距離、同一站立基準線、中性站姿、同一畫風、同一光線與背景處理。禁止合併成角色轉面拼版。每張不得加入動作、道具、文字、Logo 或額外角色。若需透明背景，四張都要一致。

生成 prompt 順序：身份鎖定與參考圖用途 → 必須保留的 3–8 個錨點與 fixed rules → 唯一視角 → 中性站姿、畫布、比例、基準線與鏡頭 → visual language、配色、材質、光線 → hard avoids 與未知細節政策。

如果圖像生成工具可用，逐張生成並回傳圖片；不可將未生成的結果說成已完成。若工具不可用，輸出四份可複製 prompt，明確說明尚未產生圖片。

固定命名：

```text
<character-id>-front.png
<character-id>-three-quarter-front.png
<character-id>-side.png
<character-id>-back.png
<character-id>-bible.yaml
turnaround-qa.yaml
```

有角色 ID 時使用 `<character-id>-bible.yaml`；沒有角色 ID 時使用 `character-bible.yaml`。圖片檔案則使用 `character-front.png`、`character-three-quarter-front.png`、`character-side.png`、`character-back.png`。

## QA 與重試

逐張檢查並記錄：身份、臉型／頭部比例、身體比例、服裝、招牌配件、主色／輔色、四分之三角度的臉部與配件連續性、側背未知細節、角色大小／基準線、單一角色、無文字／Logo／多餘道具，以及原始畫風。

使用 [turnaround-qa-template.yaml](references/turnaround-qa-template.yaml) 記錄每個 view 的 `status: pass|fail|needs_review`、檢查項目、失敗原因、是否重試與限制。只有失敗的視角可以重試，每個視角最多一次；已通過的視角不要重生。重試後仍失敗，明確說明視角與失敗特徵，並建議補充參考圖或人工修訂，不宣稱像素級一致。

## 與 IP Asset Generator 的交接

交付時把 Bible 與四張視角圖視為一個 reference pack。使用共同欄位 `identity_anchors`、`fixed_rules`、`allowed_variations`、`visual_language`、`reference_confidence`；角色轉面圖的 QA 結果只增加約束，不改寫未確認設定。後續 IP Asset Generator 應以這些檔案作為角色參考，並仍遵守其身份錨點與變化預算。

## 交付內容

最後交付生成成功的四張圖片或明確 prompt、Character Bible、QA 紀錄、每張視角的短摘要，以及仍待使用者決定的項目。另列出「建議補充參考圖」，例如頭部特寫、招牌配件特寫、手腳／尾巴／翅膀等特殊結構、主要表情與姿勢參考；這些不是核心輸出的必要項目。若使用者要長期經營 IP，再進入完整角色基準包，增加表情表、動作規劃、服裝規劃、配色規範與素材延伸建議。指出會限制一致性的因素，尤其是單張正面圖無法提供背面資訊、透明背景未能保證，或生成工具未支援參考圖控制。
