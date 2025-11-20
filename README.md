# Claude Spells

Claude Codeのカスタムコマンド集です。日常的によく使うプロンプトを保存しています。

## 概要

このリポジトリは、Claude Codeで頻繁に使用するプロンプトをカスタムコマンドとして管理するためのものです。
繰り返し使うタスクをコマンド化することで、作業効率を向上させます。

## 命名規則

- **review-**: 添削・レビュー系のコマンド
- **gen-**: 生成系のコマンド

## カスタムコマンド例

### 生成系 (gen-)
- **gen-custom-command** - 新しいカスタムコマンドの定義を生成
- **gen-branch-naming** - タスク内容に基づいて適切なブランチ名を提案
- **gen-code-naming** - クラス名、メソッド名、変数名などコーディングに関する命名を提案
- **gen-commit-message** - git diffの内容から適切なコミットメッセージを生成

### 添削系 (review-)
- **review-text** - 文章の改善提案や校正

## 使い方

各カスタムコマンドは、`input.md` から入力を読み取り、`output.md` に結果を出力する構成になっています。

1. 各コマンド用のディレクトリ（例：`gen-branch-naming/`）の `input.md` に内容を記述
2. Claude Codeで `/コマンド名` を実行
3. 結果が同じディレクトリの `output.md` に出力されます

この構成により、複雑な入力内容の記述や結果のコピーが容易になります。

### テスト実行

全てのカスタムコマンドは `test` 引数をサポートしています。これにより、サンプルデータを使って動作確認ができます：

```
/コマンド名 test
```

`test` を指定すると、`コマンド名/sample.md` の内容を入力として実行されます。これにより：
- コマンドの動作確認が簡単にできる
- `sample.md` を見ながら実際の使い方を学べる
- `sample.md` を参考に `input.md` を作成できる

## ディレクトリ構造

```
claude-spells/
├── .claude/
│   └── commands/              # カスタムコマンド定義
│       ├── gen-custom-command.md
│       ├── gen-branch-naming.md
│       ├── gen-code-naming.md
│       ├── gen-commit-message.md
│       └── review-text.md
├── gen-custom-command/
│   ├── input.md              # ユーザー入力
│   ├── output.md             # 実行結果
│   └── sample.md             # サンプル入力（test引数用）
├── gen-branch-naming/
│   ├── input.md
│   ├── output.md
│   └── sample.md
├── gen-code-naming/
│   ├── input.md
│   ├── output.md
│   └── sample.md
├── gen-commit-message/
│   ├── input.md
│   ├── output.md
│   └── sample.md
├── review-text/
│   ├── input.md
│   ├── output.md
│   └── sample.md
├── CLAUDE.md                 # プロジェクト設定
└── README.md
```

## 貢献

新しいカスタムコマンドのアイデアや改善提案は随時追加していきます。
