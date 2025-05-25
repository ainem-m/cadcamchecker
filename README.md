# CAD/CAM Checker 🦷（ハンズオンセミナー教材）

このリポジトリは、歯科用CAD/CAM冠の**適応判定フローチャート**をJavaScriptとHTMLで再現するセミナー用教材です。

---

## 🎯 セミナーのゴール

- **プロンプトを使ってChatGPTにコードを書かせる**体験
- 自分の手でCodePenに貼り付けて、**インタラクティブなUIを作成**
- 判定ロジックやUIのしくみを、コードを通して**理解・検証**

---

## 🔧 実施ステップ（参加者用）

### Step 1: ChatGPTを開く
👉 [https://chat.openai.com/](https://chat.openai.com/) にアクセス（GPT-o3mini モデル推奨）

---

### Step 2: 以下のプロンプトをそのままコピペして送信

```yaml
# --- Legend: 用語定義 -----------------------------------------
toothCodes:
  UR6: 右上6, UR7: 右上7
  UL6: 左上6, UL7: 左上7
  LR6: 右下6, LR7: 右下7
  LL6: 左下6, LL7: 左下7

definitions:
  contralateral:
    desc: "対側＝左右反対・同じ顎"
    example: "UR6 → UL6"
  ipsilateralMolar:
    desc: "同側別大臼歯＝装着歯と同側の6 or 7"
    example: "装着歯 UR6 → UR7"
  opposingTooth:
    desc: "対合歯＝上下反対・同番号"
    example: "UR6 → LR6"
  support:
    desc: "咬合支持＝上下両方残存"
    example: "UR6 と LR6 が両方 ON"
  mesialAdjacent:
    desc: "近心隣在歯＝装着歯の1本手前"
    example: "UR6 → UR5"

# --- 目的 ---------------------------------------------------
purpose: |
  CodePen で動作する単一ファイル（HTML+CSS+JS）UI を作成し、
  CAD/CAM冠の適応判定を行う。

# --- 判定ロジック -------------------------------------------
molarNumbers: [6, 7]

flow:
  0. if selectedTooth not in molarNumbers:
       result = "適応"  # 前提評価不要
       reason = "5番以下は常に適応"
  1. 前提条件（対側大臼歯に 少なくとも1つのsupport がある）を評価
     - NG: result="不適応", reason="前提不成立"
     - OK: 次へ
  2. A条件: ipsilateralMolarSupport(selectedTooth)
     - OK: result="適応", reason="A条件成立"
     - NG: 次へ
  3. B条件:
     - B1: noIpsilateralMolarSupport(selectedTooth)
     - B2: opposingToothMissing(selectedTooth)
     - B3: supportUpToMesialAdjacent(selectedTooth)
     - if B1 && B2 && B3:
         result="適応", reason="B条件①②③成立"
       else:
         result="不適応", reason="B条件X不成立"

# --- テストケース -------------------------------------------
testCases:
  - id: 1
    selected: UR6
    missing: [UR7]
    expect: NG
    path: ["前提OK","A✕","B②✕"]
  - id: 2
    selected: UR6
    missing: [UR7, LR7, LR6]
    expect: OK
    path: ["前提OK","A✕","B①②③OK"]
  # ... 以下省略

# --- UI 要件 -----------------------------------------------
ui:
  toothGrid:
    type: checkboxes
    items: all 28 teeth (初期ON)
  selector:
    type: dropdown
    items: [5,6,7] × [UR,UL,LR,LL]
  button:
    label: "適応判定"
    action: runEvaluation()
  output:
    location: ページ下部
    shows:
      - "適応" or "不適応"
      - 理由テキスト (例: "A条件成立")

# --- 実装ガイド ---------------------------------------------
functions:
  hasContralateralSupport(toothCode): boolean
  hasIpsilateralMolarSupport(toothCode): boolean
  isOpposingToothMissing(toothCode): boolean
  hasSupportUpToMesialAdjacent(toothCode): boolean
  evaluate(): { result, reason }

# --- 出力仕様 -----------------------------------------------
output:
  format: single-file (HTML+CSS+JS)
  dependencies: none
  preview: CodePen-ready
```

---

### Step 3: ChatGPTから返ってきたHTMLをコピー

1. ChatGPTの返答を選択し、右上の「Copy code」で全文コピー
2. [https://codepen.io/pen](https://codepen.io/pen) にアクセス
3. HTML欄に貼り付け、「Save」で保存！


