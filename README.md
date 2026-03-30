# Claude Code Skills Collection

Claude Code で使える汎用スキル集です。日報作成、議事録、リサーチ、スライド作成など、日常業務を自動化するスキルを収録しています。

## セットアップ

### 方法1: プラグインとしてインストール（推奨）

`~/.claude/settings.json` に以下を追加するだけで使えます：

```json
{
  "extraKnownMarketplaces": {
    "yut0takagi-skills": {
      "source": {
        "source": "github",
        "repo": "yut0takagi/claude-skills"
      }
    }
  },
  "enabledPlugins": {
    "claude-skills@yut0takagi-skills": true
  }
}
```

スキルは `/claude-skills:日報` のようにプラグイン名付きで呼び出せます。

### 方法2: スキルを直接コピー

```bash
# リポジトリをclone
git clone https://github.com/yut0takagi/claude-skills.git

# プロジェクトのスキルディレクトリにコピー
cp -r claude-skills/skills/* /path/to/your/project/.claude/skills/
```

### パスを設定

スキル内で `${VAULT_PATH}` というプレースホルダーを使用しています。
自分の環境に合わせて一括置換してください：

```bash
# 例: Obsidian Vault が ~/my-vault にある場合
find .claude/skills -name "*.md" -exec sed -i '' 's|\${VAULT_PATH}|/Users/yourname/my-vault|g' {} +
```

### 個人情報を設定

一部のスキルにプレースホルダーがあります。必要に応じて置換してください：

| プレースホルダー | 説明 |
|---|---|
| `your-email@example.com` | Google カレンダーのメールアドレス |
| `YOUR_IMPORTED_CALENDAR_ID` | インポート済みカレンダーID |
| `YOUR_NAME` | 自分の名前 |
| `YOUR_ROLE` | 役職・ポジション |
| `YOUR_DEPARTMENT` | 部署名 |
| `YOUR_UNIVERSITY` | 大学名（学生の場合） |
| `YOUR_ACCOUNT` | Google アカウント名 |

## スキル一覧

| スキル | 説明 |
|---|---|
| 日報 | 今日の日報を作成。カレンダー連携・目標引き継ぎ付き |
| 議事録作成 | 音声ファイルから議事録を自動作成（NotebookLM連携） |
| リサーチ | トピックをWeb検索→ナレッジとして保存 |
| リサーチ2スライド | リサーチ→スライド作成を一気通貫で実行 |
| スライド作成 | テーマ指定→PowerPoint自動生成。改善ループ付き |
| 議事録→ナレッジ | 議事録からナレッジを抽出・整理 |
| ナレッジ整理 | draft/temp のメモをナレッジに昇華 |
| ナレッジ検索 | Vault内のナレッジを横断検索 |
| 週次レビュー | 日報を集約→週の振り返りを生成 |
| 月次レビュー | 週次レビューを集約→月の振り返りを生成 |
| 週末まとめ | 週末の振り返り＋来週準備を一括実行 |
| 1on1準備 | 1on1の準備メモを自動作成 |
| 会議準備 | 定例・レビュー等の会議準備メモを作成 |
| 提案レビュー準備 | 提案のレビュー履歴・想定質問を整理 |
| 提案構成チェック | スライドの論理構成を自動検証 |
| プロジェクト進捗 | 全プロジェクトの進捗・期限を一覧表示 |
| アクション確認 | 議事録・日報から未完了アクションを抽出 |
| 分析メモ | データ分析の結果をナレッジとして保存 |
| スプレッドシート | Excel(.xlsx)の作成・編集・分析 |
| ドキュメント作成 | Word(.docx)の作成・編集 |
| PDF操作 | PDFの結合・分割・変換 |
| Web取り込み | URLの内容をナレッジとして保存 |
| Webクロール | サイト全体を再帰的に取得・保存 |
| Webテスト | Playwrightでブラウザテスト実行 |
| 広告キーワード抽出 | 検索クエリからキーワードのマッチタイプを自動判定 |
| 勉強会運営 | 企画→資料準備→振り返りまで一貫管理 |
| Difyワークフロー作成 | Dify WorkflowのDSL(YAML)を自動生成 |

## 前提環境

- [Claude Code](https://claude.ai/code) がインストール済み
- 一部スキルは以下のMCPサーバーが必要:
  - Google Calendar MCP（日報・会議準備）
  - NotebookLM MCP（議事録作成）
  - Playwright MCP（Webテスト）

## フォルダ構成の推奨

スキルは以下のようなVault構成を前提としています（Obsidian等）：

```
vault/
├── 日報/
├── 議事録/
├── ナレッジ/
│   ├── 技術/
│   ├── 業務/
│   └── 時事/
├── スライド/
├── draft/
│   ├── temp/
│   └── まとめ済み/
├── 添付ファイル/
├── 文字起こし/
└── 自分について.md
```

## ライセンス

MIT
