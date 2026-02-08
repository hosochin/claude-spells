# claude-spells アーキテクチャ設計書

## 目的

claude-spells をローカルの **Claude Code プラグインマーケットプレイス** として再構築する。

### ゴール

1. MCP サーバー設定を一括管理し、どのリポジトリでもプラグインをインストールするだけで利用可能にする
2. カスタム Skills を異なるリポジトリ間で共有可能にする
3. 共通化できるナレッジやプラグインを体系的に管理する
4. シークレット情報は絶対にリモートに push しない

---

## 全体構成

```
claude-spells/
├── .claude-plugin/
│   └── marketplace.json              # マーケットプレイスカタログ
│
├── plugins/
│   ├── common-skills/                # 共通スキルプラグイン
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json
│   │   └── skills/
│   │       ├── gen-commit-message/
│   │       │   ├── SKILL.md          # スキル定義（旧 .claude/commands/*.md）
│   │       │   └── sample.md
│   │       ├── gen-branch-naming/
│   │       │   ├── SKILL.md
│   │       │   └── sample.md
│   │       ├── gen-code-naming/
│   │       │   ├── SKILL.md
│   │       │   └── sample.md
│   │       ├── gen-custom-command/
│   │       │   ├── SKILL.md
│   │       │   └── sample.md
│   │       └── review-text/
│   │           ├── SKILL.md
│   │           └── sample.md
│   │
│   ├── mcp-github/                   # GitHub MCP プラグイン
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json
│   │   └── .mcp.json
│   │
│   ├── mcp-playwright/               # Playwright MCP プラグイン
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json
│   │   └── .mcp.json
│   │
│   ├── mcp-atlassian/                # Atlassian (Jira/Confluence) MCP プラグイン
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json
│   │   └── .mcp.json
│   │
│   ├── mcp-context7/                 # Context7 最新ドキュメント取得 MCP プラグイン
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json
│   │   └── .mcp.json
│   │
│   ├── mcp-slack/                    # Slack MCP プラグイン
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json
│   │   └── .mcp.json
│   │
│   └── mcp-serena/                   # Serena セマンティックコード解析 MCP プラグイン
│       ├── .claude-plugin/
│       │   └── plugin.json
│       └── .mcp.json
│
├── docs/
│   └── architecture.md               # このファイル
├── CLAUDE.md
├── README.md
└── .gitignore
```

---

## マーケットプレイス定義

### `.claude-plugin/marketplace.json`

```json
{
  "name": "claude-spells",
  "owner": {
    "name": "owner"
  },
  "metadata": {
    "description": "個人用 Claude Code プラグインマーケットプレイス"
  },
  "plugins": [
    {
      "name": "common-skills",
      "source": "./plugins/common-skills",
      "description": "コミットメッセージ生成、ブランチ命名、コードレビュー等の共通スキル"
    },
    {
      "name": "mcp-github",
      "source": "./plugins/mcp-github",
      "description": "GitHub MCP サーバー連携"
    },
    {
      "name": "mcp-playwright",
      "source": "./plugins/mcp-playwright",
      "description": "Playwright MCP サーバー連携"
    },
    {
      "name": "mcp-atlassian",
      "source": "./plugins/mcp-atlassian",
      "description": "Atlassian (Jira/Confluence) MCP サーバー連携"
    },
    {
      "name": "mcp-context7",
      "source": "./plugins/mcp-context7",
      "description": "最新ライブラリドキュメントのリアルタイム取得"
    },
    {
      "name": "mcp-slack",
      "source": "./plugins/mcp-slack",
      "description": "Slack MCP サーバー連携"
    },
    {
      "name": "mcp-serena",
      "source": "./plugins/mcp-serena",
      "description": "Serena セマンティックコード解析"
    }
  ]
}
```

---

## プラグイン詳細

### 1. common-skills プラグイン

既存のカスタムコマンドを Skills に移行したもの。

#### plugin.json

```json
{
  "name": "common-skills",
  "description": "コミットメッセージ生成、ブランチ命名、コードレビュー等の共通スキル",
  "version": "1.0.0"
}
```

#### SKILL.md の設計方針

- 全スキルに `disable-model-invocation: true` を設定（ユーザーが明示的に呼ぶもの）
- `$ARGUMENTS` で `test` 引数をサポート
- `allowed-tools` で使用ツールを制限

#### SKILL.md 例（gen-commit-message）

```markdown
---
name: gen-commit-message
description: git diff からコミットメッセージを3〜5個生成する
disable-model-invocation: true
allowed-tools: Bash, Read, Write
argument-hint: "[test]"
---

gen-commit-message/input.md を読み取り、`git diff` コマンドを実行して
変更内容を取得し、適切なコミットメッセージを3〜5個生成してください。

（以下、現在の .claude/commands/gen-commit-message.md と同等の内容）
```

#### input.md / output.md の扱い

`input.md` と `output.md` は**各リポジトリ側に作成する**。
プラグイン側には含めない（プロジェクト固有のデータのため）。

```
# 各リポジトリでの利用イメージ
my-project/
├── gen-commit-message/
│   ├── input.md       # ユーザーが入力を記述
│   └── output.md      # スキルが結果を出力
└── ...
```

`sample.md` はプラグイン側にバンドルする（テスト用のサンプルデータ）。

### 2. mcp-github プラグイン

#### plugin.json

```json
{
  "name": "mcp-github",
  "description": "GitHub MCP サーバー連携",
  "version": "1.0.0"
}
```

#### .mcp.json

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

### 3. mcp-playwright プラグイン

#### plugin.json

```json
{
  "name": "mcp-playwright",
  "description": "Playwright MCP サーバー連携",
  "version": "1.0.0"
}
```

#### .mcp.json

```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["@anthropic-ai/mcp-playwright@latest"]
    }
  }
}
```

### 4. mcp-atlassian プラグイン

Jira / Confluence との連携。

#### plugin.json

```json
{
  "name": "mcp-atlassian",
  "description": "Atlassian (Jira/Confluence) MCP サーバー連携",
  "version": "1.0.0"
}
```

#### .mcp.json

```json
{
  "mcpServers": {
    "atlassian": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://mcp.atlassian.com/v1/mcp"],
      "env": {
        "READ_ONLY_MODE": "true"
      }
    }
  }
}
```

### 5. mcp-context7 プラグイン

プロンプトに `use context7` と書くだけで、ライブラリの最新ドキュメントをリアルタイム取得。
Claude の知識カットオフ外のライブラリを使う際のハルシネーション防止に有効。

#### plugin.json

```json
{
  "name": "mcp-context7",
  "description": "最新ライブラリドキュメントのリアルタイム取得",
  "version": "1.0.0"
}
```

#### .mcp.json

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp@latest"]
    }
  }
}
```

### 6. mcp-slack プラグイン

Slack との連携。

#### plugin.json

```json
{
  "name": "mcp-slack",
  "description": "Slack MCP サーバー連携",
  "version": "1.0.0"
}
```

#### .mcp.json

```json
{
  "mcpServers": {
    "slack": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/mcp-slack@latest"],
      "env": {
        "SLACK_BOT_TOKEN": "${SLACK_BOT_TOKEN}"
      }
    }
  }
}
```

### 7. mcp-serena プラグイン

LSP を活用したセマンティックなコード解析・編集。シンボルレベルでのコード理解が可能。

#### plugin.json

```json
{
  "name": "mcp-serena",
  "description": "Serena セマンティックコード解析",
  "version": "1.0.0"
}
```

#### .mcp.json

```json
{
  "mcpServers": {
    "serena": {
      "command": "uvx",
      "args": ["--from", "git+https://github.com/oraios/serena", "serena", "start-mcp-server", "--context", "ide-assistant"]
    }
  }
}
```

---

## シークレット管理

### 方針

- MCP 設定ではシークレットを直接記述しない
- 環境変数のプレースホルダー（`${GITHUB_TOKEN}` 等）を使用
- トークン自体は `~/.zshrc` や `~/.env` に設定

### 例

```bash
# ~/.zshrc に追加
export GITHUB_TOKEN=ghp_xxxxxxxxxxxxxxxxxxxx
export SLACK_BOT_TOKEN=xoxb-xxxxxxxxxxxxxxxxxxxx
```

### .gitignore でガード

```
# シークレットを含む可能性のあるファイル
*.env
secrets/
```

---

## インストール・利用方法

### 初回セットアップ

```bash
# 1. マーケットプレイスを登録
/plugin marketplace add /path/to/claude-spells

# 2. 必要なプラグインをインストール
/plugin install common-skills@claude-spells
/plugin install mcp-github@claude-spells
/plugin install mcp-playwright@claude-spells
/plugin install mcp-atlassian@claude-spells
/plugin install mcp-context7@claude-spells
/plugin install mcp-slack@claude-spells
/plugin install mcp-serena@claude-spells
```

### 他のリポジトリでの利用

```bash
# スキルを明示的に呼び出す
/common-skills:gen-commit-message
/common-skills:review-text

# テスト実行
/common-skills:gen-commit-message test
```

---

## 移行計画

### 現在の構成 → 新構成の対応

| 現在 | 移行先 |
|------|--------|
| `.claude/commands/gen-commit-message.md` | `plugins/common-skills/skills/gen-commit-message/SKILL.md` |
| `.claude/commands/gen-branch-naming.md` | `plugins/common-skills/skills/gen-branch-naming/SKILL.md` |
| `.claude/commands/gen-code-naming.md` | `plugins/common-skills/skills/gen-code-naming/SKILL.md` |
| `.claude/commands/gen-custom-command.md` | `plugins/common-skills/skills/gen-custom-command/SKILL.md` |
| `.claude/commands/review-text.md` | `plugins/common-skills/skills/review-text/SKILL.md` |
| `gen-*/sample.md` | `plugins/common-skills/skills/gen-*/sample.md` |
| `gen-*/input.md` | 各リポジトリに配置（プラグインには含めない） |
| `gen-*/output.md` | 各リポジトリに配置（プラグインには含めない） |

### 移行ステップ

1. `plugins/` ディレクトリ構成を作成
2. `.claude-plugin/marketplace.json` を作成
3. 各プラグインの `plugin.json` を作成
4. 既存コマンドを `SKILL.md` に変換（frontmatter 追加）
5. `sample.md` をプラグインディレクトリにコピー
6. MCP プラグイン（github, playwright）を作成
7. ローカルで `/plugin marketplace add` → `/plugin install` でテスト
8. CLAUDE.md と README.md を更新

---

## 今後の拡張案

- **coding-conventions スキル**: `user-invocable: false` でAIが自動適用するコーディング規約
- **hooks プラグイン**: ファイル保存時の自動lint等
- **gen-pdf プラグイン**: PDF生成（既存の gen-pdf/ を移行）
