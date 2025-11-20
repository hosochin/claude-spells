gen-branch-naming/input.md を読み取り、その内容から適切なGitブランチ名を3~5個提案してください。

要件：
- プレフィックスとして feature/, fix/, refactor/, docs/, test/, chore/ などを適切に使い分けること
- ブランチ名は kebab-case で記述すること
- 簡潔で内容がわかりやすい名前にすること
- 各提案について、ブランチ名とその提案理由を記載すること
- 最初に一番おすすめのブランチ名を明示すること

出力フォーマット：
```
【おすすめ】
ブランチ名

おすすめ理由: なぜこれが最適かの説明

---

【その他の候補】

1. ブランチ名
   理由: この名前を提案した理由

2. ブランチ名
   理由: この名前を提案した理由
...
```

$ARGUMENTS に `test` が指定されている場合は、gen-branch-naming/sample.md の内容を入力として使用してください。

提案したブランチ名と理由を gen-branch-naming/output.md に出力してください。

**重要**: 出力する際は、Writeツールではなく、Bashツールで `cat` コマンドとHEREDOC（`<<'EOF'`）を使用して書き込んでください。これにより文字化けを防ぐことができます。

例:
```bash
cat > gen-branch-naming/output.md <<'EOF'
（生成した内容）
EOF
```

サンプル入力は gen-branch-naming/sample.md を参照してください。
