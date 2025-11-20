gen-custom-command/input.md を読み取り、新しいカスタムコマンドの定義を生成してください。

要件：
- コマンド名、目的、入出力の形式などを input.md から読み取る
- CLAUDE.md とこれまでのカスタムコマンドのベストプラクティスに従う
- 命名規則（gen-/review-）を適用する
- 必ずサンプルデータを sample.md として作成する
- **全てのコマンドに `test` 引数を追加する**：`$ARGUMENTS` に `test` が指定されている場合、コマンド名/sample.md の内容を入力として使用
- 日本語で記述する

実行内容：
1. `.claude/commands/コマンド名.md` を作成
2. `コマンド名/` ディレクトリを作成
3. `コマンド名/input.md` を作成（空ファイル）
4. `コマンド名/output.md` を作成（空ファイル）
5. `コマンド名/sample.md` を作成（サンプルデータを含む）
6. README.md への追加内容を提示

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

上記のサンプルを参考に、gen-custom-command/input.md の内容から新しいカスタムコマンドを作成してください。

実行手順：
1. `.claude/commands/コマンド名.md` ファイルを作成（test引数機能を含める）
2. `コマンド名/` ディレクトリを作成
3. `コマンド名/input.md` を作成（空ファイル）
4. `コマンド名/output.md` を作成（空ファイル）
5. `コマンド名/sample.md` を作成（サンプルデータを含む）
6. ユーザーに以下を伝える：
   - 作成したファイル一覧
   - README.md に追加すべき行

**重要**: コマンド定義（`.claude/commands/コマンド名.md`）には、必ず以下の出力方法の指示を含めてください：

```
**重要**: 出力する際は、Writeツールではなく、Bashツールで `cat` コマンドとHEREDOC（`<<'EOF'`）を使用して書き込んでください。これにより文字化けを防ぐことができます。

例:
\`\`\`bash
cat > コマンド名/output.md <<'EOF'
（生成した内容）
EOF
\`\`\`
```
