---
name: review-text
description: 文章の改善提案や校正を行う
disable-model-invocation: true
allowed-tools: Bash, Read, Write
argument-hint: "[test]"
---

review-text/input.md に記述された文章を添削してください。

input.md のフォーマット：
```
トーン: formal または casual（指定がない場合は formal）

【添削する文章】
（添削したい文章）
```

要件:
- トーンに応じて添削のスタイルを変更すること
  - `formal`: フォーマルなトーンで添削（ビジネス文書、公式な場面向け）
  - `casual`: カジュアルなトーンで添削（日常会話、親しい相手向け）
- 文法、表現、語彙の改善提案を行うこと
- 元の文章と添削後の文章を両方示すこと
- 主な変更点を箇条書きで説明すること

出力フォーマット:
```
【元の文章】
（元の文章をそのまま表示）

【添削後の文章】
（添削後の文章）

【主な変更点】
- 変更点1の説明
- 変更点2の説明
...
```

$ARGUMENTS に `test` が指定されている場合は、review-text/sample.md の内容を入力として使用してください。

添削結果を review-text/output.md に出力してください。

**重要**: 出力する際は、Writeツールではなく、Bashツールで `cat` コマンドとHEREDOC（`<<'EOF'`）を使用して書き込んでください。これにより文字化けを防ぐことができます。

例:
```bash
cat > review-text/output.md <<'EOF'
（生成した内容）
EOF
```

サンプル入力は review-text/sample.md を参照してください。
