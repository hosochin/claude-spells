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
- **gen-branch-naming** - コミット内容やタスクに基づいて適切なブランチ名を提案
- **gen-sql** - 自然言語の要件からSQLクエリを生成
- **gen-code-naming** - クラス名、メソッド名、変数名などコーディングに関する命名を提案

### 添削系 (review-)
- **review-text** - 文章の改善提案や校正

## 使い方

各カスタムコマンドは、`input.md` から入力を読み取り、`output.md` に結果を出力する構成になっています。

1. 各コマンド用のディレクトリ（例：`gen-branch-naming/`）の `input.md` に内容を記述
2. Claude Codeで `/コマンド名` を実行（引数が必要な場合は `/コマンド名 arg=value`）
3. 結果が同じディレクトリの `output.md` に出力されます

この構成により、複雑な入力内容の記述や結果のコピーが容易になります。

### 引数の指定方法

コマンドに引数を渡す場合は、`$ARGUMENTS` を使用します：
```
/review-text tone=formal
```

## ディレクトリ構造

```
claude-spells/
├── .claude/
│   └── commands/
│       ├── gen-custom-command.md
│       ├── gen-branch-naming.md
│       ├── gen-code-naming.md
│       └── review-text.md
├── gen-custom-command/
│   ├── input.md
│   └── output.md
├── gen-branch-naming/
│   ├── input.md
│   └── output.md
├── gen-code-naming/
│   ├── input.md
│   └── output.md
├── review-text/
│   ├── input.md
│   └── output.md
└── README.md
```

## 貢献

新しいカスタムコマンドのアイデアや改善提案は随時追加していきます。
