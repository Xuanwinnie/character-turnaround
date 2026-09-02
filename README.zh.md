# Character Turnaround

[English](README.md) · 繁體中文

`Character Turnaround` 是一個角色基準包建立 Skill。使用者提供一張正面角色參考圖後，Skill 會先分析角色設定，在使用者確認後生成四張獨立圖片：正面、四分之三正面、側面與背面。

它適合角色、吉祥物、品牌 IP、遊戲、動畫、漫畫與繪本創作。輸出的 Character Bible 與參考圖可直接交給 `ip-asset-generator`，用於後續生成場景圖、貼圖、社群圖片與透明角色素材。

## 它能做什麼

- 從正面參考圖整理可觀察的角色身份。
- 區分已確認、合理推測與待確認內容。
- 建立 identity anchors、fixed rules、allowed variations 與 visual language。
- 正式生成前要求使用者確認設定。
- 生成四張比例、基準線、站姿與畫風一致的獨立圖片。
- 逐視角執行 QA，且只重試失敗視角一次。

## 純圖片模式

如果只想要圖片，可以使用純圖片模式。Skill 仍會在內部擷取必要的身份錨點，並確認重要的未知細節，但不建立或交付 `character-bible.yaml` 與 `turnaround-qa.yaml`，最後只交付四張圖片與簡短 QA 結論。

如果日後要長期使用 `ip-asset-generator` 經營 IP，再改用完整 Character Bible 流程。

## 快速開始

### 安裝到 Codex

```bash
git clone https://github.com/Xuanwinnie/character-turnaround.git \
  ~/.codex/skills/character-turnaround
```

### 安裝到 Claude Code

```bash
git clone https://github.com/Xuanwinnie/character-turnaround.git \
  ~/.claude/skills/character-turnaround
```

重新啟動對應工具後，提供一張清楚的正面角色圖：

```text
使用 character-turnaround 分析這張正面角色參考圖。
請先建立 Character Bible 規劃，並區分已確認、合理推測與待確認內容。
在我確認設定前，不要生成正式角色參考圖。
```

如果只需要圖片：

```text
使用 character-turnaround 的純圖片模式。
只生成正面、四分之三正面、側面與背面四張獨立圖片，
不要建立 Character Bible。如果看不到背面細節，請先詢問我是否允許保守補完。
```

確認設定後：

```text
Character Bible 已確認。
請生成四張獨立角色參考圖：
正面、四分之三正面、側面與背面。
維持相同角色大小、基準線、中性站姿、鏡頭距離、畫風、光線與背景處理。
不要把四個視角合併成同一張圖。
```

## 使用流程

```text
正面參考圖
        ↓
分析身份與視覺語言
        ↓
區分已確認／合理推測／待確認
        ↓
使用者確認 Character Bible
        ↓
生成四張獨立視角圖
        ↓
逐視角執行一致性 QA
        ↓
交付給 IP Asset Generator 使用
```

## 可直接複製的使用指令

### 純圖片模式

```text
使用 character-turnaround 的純圖片模式。

請生成四張獨立角色基準圖：
1. 正面
2. 四分之三正面
3. 側面
4. 背面

不要建立 Character Bible。
如果正面圖無法確認背面細節，請先詢問我是否允許保守補完。
```

### 角色設定模式

```text
使用 character-turnaround 的角色設定模式。

請先分析這張正面角色參考圖，整理：
- 已確認特徵
- 合理推測
- 待確認內容
- identity anchors
- fixed rules
- allowed variations

先不要生成圖片，等我確認 Character Bible 後再繼續。
```

確認 Bible 後：

```text
Character Bible 已確認。

請生成正面、四分之三正面、側面與背面四張獨立角色基準圖，
並在生成後執行一致性 QA。
```

## 核心輸出

```text
<character-id>-front.png
<character-id>-three-quarter-front.png
<character-id>-side.png
<character-id>-back.png
<character-id>-bible.yaml
turnaround-qa.yaml
```

若使用者要求保留原始正面圖，可直接將它作為正面參考，不必重新生成。四分之三正面圖可補足臉部身份與側面結構之間的視覺連續性。

若有角色 ID，Character Bible 使用 `<character-id>-bible.yaml`；沒有角色 ID 時使用 `character-bible.yaml`。

## Character Bible 與 IP Bible

`character-bible.yaml` 是角色轉面階段使用的 IP Bible，沿用 `ip-asset-generator` 的核心欄位：

- `identity_anchors`
- `fixed_rules`
- `allowed_variations`
- `visual_language`
- `reference_confidence`

此外會記錄轉面專用的參考來源、未知細節、確認狀態與視角要求。建議只維護這一份主檔，不要同時維護兩份重複的 IP Bible；只有下游工具要求舊格式時，才額外匯出相容版本。

使用 `ip-asset-generator` 時，請將已確認的 `character-bible.yaml` 與四張角色參考圖一起提供，作為一個 reference pack。

## 建議補充參考圖

以下不是核心輸出的必要項目，但可以提高後續一致性：

- 頭部與臉部特寫；
- 招牌配件特寫；
- 手、腳、尾巴、翅膀或其他特殊結構；
- 中性全身比例圖；
- 常用表情參考；
- 實用姿勢參考；
- 核准的配色與材質參考。

如果使用者需要長期經營 IP，可再生成完整角色基準包，包含 Character Bible、四張核心視角圖、表情表、動作規劃、服裝規劃、配色規範與後續素材建議。

## 一致性規則

Skill 會維持角色物種／定位、臉型、身體比例、年齡感、招牌色、永久標記、招牌配件與視覺語言。不得把未確認的背面細節寫成永久設定，不得新增文字、Logo、道具或額外角色，也不得把四個視角合併成一張拼版。

影像模型無法保證像素級一致。若出現身份漂移，Skill 會記錄具體問題，且只重試失敗的視角一次。

## Repository 結構

```text
character-turnaround/
├── SKILL.md
├── agents/
│   └── openai.yaml
├── references/
│   ├── character-bible-schema.md
│   └── turnaround-qa-template.yaml
├── README.md
└── README.zh.md
```

## License

Skill 說明與範本請依本 repository 設定的授權使用。若加入角色插圖、品牌素材或第三方參考資產，請另外確認使用權限。
