---
name: Difyワークフロー作成
description: "Dify WorkflowのDSL（YAML）ファイルを自動生成し、Difyにインポート可能な.ymlとして出力。Use when: 「Difyワークフロー」「DSL作成」「Difyで自動化」「ワークフローをYAMLで」「Difyアプリ作成」"
argument-hint: "[作りたいワークフローの説明]"
allowed-tools: "Read, Write, Edit, Glob, Grep, Agent, WebFetch"
---

$ARGUMENTS の説明に基づいて、Dify WorkflowのDSLファイル（YAML形式）を生成し、Difyにインポート可能な `.yml` ファイルとして出力してください。

引数（必須）: 作りたいワークフローの説明
（例: `/Difyワークフロー作成 URLを入力したら内容を要約してSlackに投稿するワークフロー`）
（例: `/Difyワークフロー作成 CSVファイルをアップロードしたらLLMで分析してレポートを生成`）

---

## ステップ0: DSL仕様リファレンスを読み込む

以下を読み込む（必須）：
- `${VAULT_PATH}/ナレッジ/技術/Dify Workflow DSL仕様リファレンス.md`

このファイルには全23ノードタイプのYAML構造、エッジ定義、変数参照構文が定義されている。
**生成するDSLはこの仕様に厳密に従うこと。**

---

## ステップ1: ワークフロー設計

ユーザーの説明から以下を分析・設計する：

### 1.1 要件分析

- **入力:** ユーザーが提供するもの（テキスト、ファイル、URL等）
- **処理:** 必要な処理ステップ（LLM呼び出し、API呼び出し、条件分岐等）
- **出力:** 最終的な出力（テキスト、ファイル、API呼び出し結果等）

### 1.2 アプリモード判定

| モード | 使うケース |
|--------|-----------|
| `workflow` | 単発タスク（分析、変換、生成等）。Start + End ノード |
| `advanced-chat` | 会話型（チャットボット、Q&A等）。Start + Answer ノード |

### 1.3 ノード構成の設計

必要なノードを選定し、接続順序を設計する。

**ノード選択の指針:**
```
やりたいこと → 使うノード
─────────────────────────
LLMに質問・生成させたい → llm
外部APIを叩きたい → http-request
コードで処理したい → code
テンプレートで整形 → template-transform
ナレッジベースを検索 → knowledge-retrieval
条件で分岐したい → if-else
質問を分類したい → question-classifier
リストを順に処理 → iteration
自然言語→構造化データ → parameter-extractor
ファイルからテキスト抽出 → document-extractor
ツールを自動選択させたい → agent
```

### 1.4 設計案をユーザーに提示

```
## ワークフロー設計

**アプリモード:** workflow / advanced-chat
**ノード構成:**

[Start] → [ノード1: 名前(タイプ)] → [ノード2: 名前(タイプ)] → [End/Answer]

### ノード詳細
1. **Start** — 入力: {変数名}({型})
2. **{ノード名}**({タイプ}) — {処理内容}
3. ...
N. **End/Answer** — 出力: {内容}

この設計でよろしいですか？（変更があれば指示してください）
```

**ユーザーの承認を待つ**

---

## ステップ2: DSLファイル生成

承認後、DSL仕様リファレンスに従ってYAMLを生成する。

### 2.1 生成ルール

**ノードID生成:**
- タイムスタンプ風の13桁数字文字列を使用
- 開始ノード: `'1720794829558'` のような形式
- 後続ノード: 連番で `'1720794830001'`, `'1720794830002'`, ... と増加

**ポジション計算:**
- Start ノード: `x: 30, y: 263`
- 後続ノードは x を +300 ずつ増加（左から右へ配置）
- 分岐がある場合は y を ±150 してずらす

**エッジID生成:**
- `{source_id}-{sourceHandle}-{target_id}-target` の命名規則

**変数参照:**
- プロンプト内: `{{#ノードID.変数名#}}`
- YAML内: `variable_selector: ['ノードID', '変数名']`

**必須フィールド:**
- 各ノードに `type: custom`, `position`, `width`, `height`, `data` が必須
- `data` 内に `type`（ノードタイプ）, `title`, `desc` が必須
- エッジに `source`, `target`, `sourceHandle`, `targetHandle: target`, `type: custom` が必須

### 2.2 LLMノードのモデル設定

ユーザーが指定しない場合のデフォルト:

```yaml
model:
  provider: openai
  name: gpt-4o-mini
  mode: chat
  completion_params:
    temperature: 0.7
```

ユーザーが「Claude使いたい」等と言った場合:

```yaml
model:
  provider: anthropic
  name: claude-sonnet-4-20250514
  mode: chat
  completion_params:
    temperature: 0.7
```

### 2.3 DSLテンプレート

```yaml
version: "0.6.0"
kind: app
app:
  name: "{アプリ名}"
  description: "{説明}"
  mode: workflow  # or advanced-chat
  icon: "{絵文字}"
  icon_type: EMOJI
  icon_background: '#EFF1F5'
  use_icon_as_answer_icon: false
dependencies: []
workflow:
  graph:
    nodes:
      # --- ここにノード定義 ---
    edges:
      # --- ここにエッジ定義 ---
    viewport:
      x: 0
      y: 0
      zoom: 0.7
  features:
    file_upload:
      enabled: false
    opening_statement: ''
    retriever_resource:
      enabled: true
    sensitive_word_avoidance:
      enabled: false
    speech_to_text:
      enabled: false
    suggested_questions: []
    suggested_questions_after_answer:
      enabled: false
    text_to_speech:
      enabled: false
      language: ''
      voice: ''
  environment_variables: []
  conversation_variables: []
```

### 2.4 ノード定義テンプレート（主要なもの）

**Start ノード:**
```yaml
- id: '{id}'
  type: custom
  position: {x: 30, y: 263}
  positionAbsolute: {x: 30, y: 263}
  sourcePosition: right
  targetPosition: left
  width: 244
  height: 90
  selected: false
  data:
    type: start
    title: "Start"
    desc: ''
    variables:
      - variable: {変数名}
        label: "{表示名}"
        type: text-input  # text-input/paragraph/number/select/file/file-list
        required: true
        max_length: 48
```

**LLM ノード:**
```yaml
- id: '{id}'
  type: custom
  position: {x: 330, y: 263}
  positionAbsolute: {x: 330, y: 263}
  sourcePosition: right
  targetPosition: left
  width: 244
  height: 127
  selected: false
  data:
    type: llm
    title: "{タイトル}"
    desc: ''
    model:
      provider: openai
      name: gpt-4o-mini
      mode: chat
      completion_params:
        temperature: 0.7
    prompt_template:
      - role: system
        text: "{システムプロンプト}"
      - role: user
        text: "{{#StartノードID.変数名#}}"
    context:
      enabled: false
      variable_selector: []
    memory:
      enabled: false
    vision:
      enabled: false
```

**End ノード（workflow用）:**
```yaml
- id: '{id}'
  type: custom
  position: {x: 630, y: 263}
  positionAbsolute: {x: 630, y: 263}
  sourcePosition: right
  targetPosition: left
  width: 244
  height: 90
  selected: false
  data:
    type: end
    title: "End"
    desc: ''
    outputs:
      - variable: result
        value_selector: ['{LLMノードID}', 'text']
```

**Answer ノード（advanced-chat用）:**
```yaml
- id: '{id}'
  type: custom
  position: {x: 630, y: 263}
  positionAbsolute: {x: 630, y: 263}
  sourcePosition: right
  targetPosition: left
  width: 244
  height: 107
  selected: false
  data:
    type: answer
    title: "Answer"
    desc: ''
    answer: "{{#LLMノードID.text#}}"
```

**エッジ:**
```yaml
- id: '{sourceId}-source-{targetId}-target'
  source: '{sourceId}'
  sourceHandle: source
  target: '{targetId}'
  targetHandle: target
  type: custom
  zIndex: 0
  data:
    isInIteration: false
    isInLoop: false
    sourceType: {ソースノードタイプ}
    targetType: {ターゲットノードタイプ}
```

---

## ステップ3: ファイル出力

1. 生成したDSLを `${VAULT_PATH}/draft/dify/` ディレクトリに保存
2. ファイル名: `{アプリ名（英語スネークケース）}.yml`
3. ディレクトリがなければ作成

---

## ステップ4: 検証・報告

### 4.1 セルフチェック

生成したDSLを以下の観点で検証：

- [ ] `version: "0.6.0"` が設定されている
- [ ] `kind: app` が設定されている
- [ ] `app.mode` が正しい（workflow / advanced-chat）
- [ ] 全ノードに `id`, `type: custom`, `position`, `data` がある
- [ ] `data.type` が正しいノードタイプ名（ハイフン区切り: `if-else`, `http-request` 等）
- [ ] Start ノードがある（workflow/advanced-chat の場合）
- [ ] End ノード（workflow）または Answer ノード（advanced-chat）がある
- [ ] 全ノード間のエッジが正しく接続されている
- [ ] 変数参照が正しい（`{{#ノードID.変数名#}}` 形式）
- [ ] `variable_selector` がリスト形式（`['ノードID', '変数名']`）
- [ ] IF/ELSE の `sourceHandle` が `true` / `false` になっている
- [ ] エッジIDが `{source}-{handle}-{target}-target` 形式

### 4.2 完了報告

```
## Difyワークフロー作成完了

**アプリ名:** {名前}
**モード:** workflow / advanced-chat
**ノード数:** {N}
**ファイル:** {パス}

### ワークフロー構成
[Start] → [ノード1] → [ノード2] → ... → [End/Answer]

### 使い方
1. Difyを開く（https://cloud.dify.ai または セルフホスト環境）
2. 「スタジオ」→「DSLファイルをインポート」
3. `{ファイル名}` をアップロード
4. モデルプロバイダーのAPIキーを設定（LLMノードがある場合）
5. 「公開」ボタンで公開

### 注意事項
- ナレッジベースID（knowledge-retrieval ノード）はインポート後に手動設定が必要
- ツールの認証情報（tool ノード）はインポートに含まれないため別途設定が必要
- 環境変数のSecret値はインポート後に再設定が必要
```

---

## 補足: よくあるパターン

### パターン1: シンプルなテキスト処理
```
Start(text-input) → LLM → End
```

### パターン2: 条件分岐つき
```
Start → LLM(分類) → IF/ELSE → LLM(パターンA) → End
                              → LLM(パターンB) → End
```

### パターン3: RAG（ナレッジ検索）
```
Start → Knowledge Retrieval → LLM(コンテキスト付き) → End
```

### パターン4: API連携
```
Start → HTTP Request → Code(データ加工) → LLM(分析) → End
```

### パターン5: ファイル処理
```
Start(file) → Document Extractor → LLM(要約) → End
```

### パターン6: イテレーション
```
Start → Code(リスト生成) → Iteration[LLM → Template] → End
```
