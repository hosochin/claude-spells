# Claude Spells

個人用の Claude Code プラグインマーケットプレイスです。
1つのプラグインをインストールするだけで、Skills と MCP サーバーがすべて使える状態になります。

## 含まれる機能

### Skills

| スキル | 呼び出し | 説明 |
|---|---|---|
| gen-commit-message | `/common-skills:gen-commit-message` | git diff からコミットメッセージを生成 |
| gen-branch-naming | `/common-skills:gen-branch-naming` | タスク内容からブランチ名を提案 |
| gen-code-naming | `/common-skills:gen-code-naming` | クラス名・メソッド名・変数名を提案 |
| gen-custom-command | `/common-skills:gen-custom-command` | 新しいスキル定義を生成 |
| review-text | `/common-skills:review-text` | 文章の改善提案・校正 |
| review-secret | `/common-skills:review-secret` | push 前のシークレット・個人情報漏洩チェック |

### MCP サーバー

| サーバー | 説明 | シークレット |
|---|---|---|
| github | GitHub 連携 | `GITHUB_TOKEN` |
| playwright | ブラウザ操作 | 不要 |
| atlassian | Jira / Confluence 連携 | OAuth（ブラウザ認証） |
| context7 | 最新ライブラリドキュメント取得 | 不要 |
| slack | Slack 連携 | `SLACK_BOT_TOKEN` |
| serena | セマンティックコード解析 | 不要 |

## セットアップ

### 1. 環境変数の設定

シークレットが必要な MCP サーバーを使う場合、事前に環境変数を設定してください。

```bash
# ~/.zshrc に追加
export GITHUB_TOKEN=ghp_xxxxxxxxxxxxxxxxxxxx
export SLACK_BOT_TOKEN=xoxb-xxxxxxxxxxxxxxxxxxxx
```

### 2. インストール

ターミナルから以下を実行すれば完了です。

```bash
claude plugin marketplace add /path/to/claude-spells && \
claude plugin install common-skills@claude-spells
```

これだけで Skills と MCP サーバーがすべてセットアップされます。

Claude Code 内からも可能です。

```
/plugin marketplace add /path/to/claude-spells
/plugin install common-skills@claude-spells
```

## 使い方

### スキルの呼び出し

```
# 明示的に呼び出す
/common-skills:gen-commit-message
/common-skills:review-text

# テスト実行（sample.md を使用）
/common-skills:gen-commit-message test
```

### スキルの入出力

各スキルは `input.md` から入力を読み取り、`output.md` に結果を出力します。
利用するリポジトリ側に以下のディレクトリを作成してください。

```
my-project/
├── gen-commit-message/
│   ├── input.md       # 入力を記述
│   └── output.md      # 結果が出力される
└── ...
```

## プラグイン管理コマンド

### マーケットプレイス操作

| コマンド | 説明 |
|---|---|
| `/plugin` | プラグインマネージャーを開く（Discover / Installed / Marketplaces / Errors タブ） |
| `/plugin marketplace add <source>` | マーケットプレイスを登録 |
| `/plugin marketplace list` | 登録済みマーケットプレイスの一覧 |
| `/plugin marketplace update <name>` | マーケットプレイスのプラグインリストを更新 |
| `/plugin marketplace remove <name>` | マーケットプレイスを削除（インストール済みプラグインも削除される） |

### プラグイン操作

| コマンド | 説明 |
|---|---|
| `/plugin install <plugin>@<marketplace>` | プラグインをインストール |
| `/plugin uninstall <plugin>@<marketplace>` | プラグインをアンインストール |
| `/plugin disable <plugin>@<marketplace>` | プラグインを無効化（アンインストールせず） |
| `/plugin enable <plugin>@<marketplace>` | 無効化したプラグインを再有効化 |
| `/plugin validate .` | プラグインの構成を検証 |

### ターミナルから実行

Claude Code 内の `/plugin` コマンドは、ターミナルから `claude plugin` でも実行できます。

```bash
claude plugin install common-skills@claude-spells
claude plugin marketplace list
```

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
│           ├── gen-commit-message/
│           │   ├── SKILL.md
│           │   └── sample.md
│           ├── gen-branch-naming/
│           ├── gen-code-naming/
│           ├── gen-custom-command/
│           └── review-text/
├── docs/
│   └── architecture.md               # アーキテクチャ設計書
├── CLAUDE.md
├── README.md
└── .gitignore
```

## 命名規則

スキルは目的に応じて接頭辞を付けます。

- **gen-**: 生成系（例: gen-commit-message, gen-branch-naming）
- **review-**: 添削・レビュー系（例: review-text）
