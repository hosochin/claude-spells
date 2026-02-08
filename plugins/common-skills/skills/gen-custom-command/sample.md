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
