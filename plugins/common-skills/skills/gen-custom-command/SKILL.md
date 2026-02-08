---
name: gen-custom-command
description: 新しいスキル定義を生成する
disable-model-invocation: true
allowed-tools: Bash, Read, Write
argument-hint: "[test]"
---

gen-custom-command/input.md を読み取り、新しいスキルの定義を生成してください。

要件：
- コマンド名、目的、入出力の形式などを input.md から読み取る
- CLAUDE.md のベストプラクティスに従う
- 命名規則（gen-/review-）を適用する
- 必ずサンプルデータを sample.md として作成する
- **全てのスキルに `test` 引数を追加する**：`$ARGUMENTS` に `test` が指定されている場合、スキル名/sample.md の内容を入力として使用
- 日本語で記述する

実行内容：
1. `plugins/common-skills/skills/スキル名/SKILL.md` を作成（frontmatter 付き）
2. `plugins/common-skills/skills/スキル名/sample.md` を作成（サンプルデータを含む）
3. ユーザーに以下を伝える：
   - 作成したファイル一覧
   - marketplace.json への追加は不要（common-skills プラグインに自動で含まれる）
   - README.md のスキル一覧に追加すべき行

## サンプル

### 想定 input.md:
```
コマンド名: gen-commit-message
カテゴリ: 生成系

目的:
git diff の内容から適切なコミットメッセージを生成する

入力:
- gen-commit-message/input.md に git diff の結果を記述

出力:
- 3~5個のコミットメッセージ候補
- 一番おすすめのメッセージを最初に表示
- 各メッセージの選定理由を記載
- Conventional Commits 形式に従う（feat:, fix:, docs: など）

引数: なし
```

$ARGUMENTS に `test` が指定されている場合は、gen-custom-command/sample.md の内容を入力として使用してください。

**重要**: 出力する際は、Writeツールではなく、Bashツールで `cat` コマンドとHEREDOC（`<<'EOF'`）を使用して書き込んでください。これにより文字化けを防ぐことができます。

例:
```bash
cat > スキル名/SKILL.md <<'EOF'
（生成した内容）
EOF
```
