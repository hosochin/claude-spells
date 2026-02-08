# Claude Code プロジェクト設定

## プロジェクト概要

このプロジェクトは、個人用の **Claude Code プラグインマーケットプレイス**です。
Skills や MCP サーバー設定をプラグインとして一元管理し、どのリポジトリからでもインストールして利用可能にします。

## 重要なルール

### 言語設定
- **日本語を使用すること**：すべてのコミュニケーション、ドキュメント、スキルの説明を日本語で行います

### シークレット管理
- **シークレット情報は絶対にリモートに push しない**
- MCP 設定では環境変数のプレースホルダー（`${GITHUB_TOKEN}` 等）を使用する
- トークン自体は `~/.zshrc` 等に設定し、リポジトリには含めない

## ディレクトリ構造

```
claude-spells/
├── .claude-plugin/
│   └── marketplace.json              # マーケットプレイスカタログ
├── plugins/
│   └── common-skills/                # 全機能を含む単一プラグイン
│       ├── .claude-plugin/
│       │   └── plugin.json
│       ├── .mcp.json                 # MCP サーバー設定（全サーバー）
│       └── skills/
│           └── スキル名/
│               ├── SKILL.md          # スキル定義
│               └── sample.md         # サンプル入力（test引数用）
├── docs/
│   └── architecture.md               # アーキテクチャ設計書
├── CLAUDE.md                         # このファイル
├── README.md
└── .gitignore
```

## スキルの構造

### SKILL.md の設計方針

各スキルは `plugins/common-skills/skills/` 配下にディレクトリとして配置します。

```
skills/スキル名/
├── SKILL.md     # スキル定義（frontmatter + マークダウン本文）
└── sample.md    # サンプル入力（test引数で使用）
```

### frontmatter の設定

- ユーザーが明示的に呼ぶスキルには `disable-model-invocation: true` を設定
- AI が文脈から自動で使うスキルにはデフォルト（設定なし）を使用
- `allowed-tools` で使用ツールを制限する

```markdown
---
name: スキル名
description: スキルの説明
disable-model-invocation: true
allowed-tools: Bash, Read, Write
argument-hint: "[test]"
---

（スキルの指示内容）
```

### input.md / output.md の扱い

- `input.md` と `output.md` は**利用するリポジトリ側に作成**する
- プラグイン側には含めない（プロジェクト固有のデータのため）
- `sample.md` はプラグイン側にバンドルする（テスト用サンプルデータ）

### 命名規則

スキルは目的に応じて接頭辞を付けます：

- **gen-**: 生成系スキル（例: gen-commit-message, gen-branch-naming）
- **review-**: 添削・レビュー系スキル（例: review-text）

## MCP サーバーの管理

MCP サーバー設定は `plugins/common-skills/.mcp.json` に全サーバー分をまとめて定義します。
シークレットが必要な場合は環境変数プレースホルダー（`${GITHUB_TOKEN}` 等）を使用します。

## スキル作成のベストプラクティス

### 1. サンプルデータの提供

各スキルには必ず `sample.md` を含めてください。`$ARGUMENTS` に `test` が指定された場合にサンプルとして使用されます。

### 2. 出力フォーマットの統一

- セクション見出しには【】を使用
- おすすめを最初に提示する
- 複数の候補を提供する（3〜5個程度）
- 各選択肢の理由を説明する

### 3. 出力方法

出力する際は、Write ツールではなく、Bash ツールで `cat` コマンドと HEREDOC（`<<'EOF'`）を使用して書き込んでください。これにより文字化けを防ぐことができます。

```bash
cat > スキル名/output.md <<'EOF'
（生成した内容）
EOF
```
