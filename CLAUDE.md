# CLAUDE.md — PKC-Public

## Language Policy
- Final output MUST be in Japanese

## 「公開して」ワークフロー

### 基本フロー
1. 開始時刻を記録 (JST)
2. PKC2 の `git fetch --all`
3. 最新 dev ブランチを特定
   `git branch -r --sort=-committerdate | grep -v 'origin/main' | grep -v 'origin/claude/sync-pkc2-html' | grep -v 'origin/dependabot' | head -1`
4. `dist/pkc2.html`・`PKC2-Extensions/pkc2-manual.html` を取得
5. md5sum で変更検知 → 変更なしならスキップ
6. feature branch `claude/sync-pkc2-html-7axZm` に commit & push
7. PR 作成 → MCP でマージ
8. 2分待機 (GitHub Pages デプロイ)
9. 完了時刻・結果を報告

### ブランチ / PR 指定オプション

**単一指定**: `公開して <branch名>` または `公開して PR#<番号>`
→ 指定ブランチ(またはPRのhead branch)を DEV版として `PKC2-DEV/index.html` に使用

**複数指定**: `公開して <branch1> <branch2> ...`
→ `PKC2-DEV-1/`, `PKC2-DEV-2/`, ... を一時作成して並列公開
→ 完了後、フォルダ名と対応ブランチを報告

### 報告形式

```
公開完了。

開始: 2026-05-27 09:14 JST / 完了: 2026-05-27 09:17 JST
PR: #74 | DEV: claude/pkc-md-v4-changelog-final (main+10)

最新変更 (上位5件):
- feat: pkc-format-block 5 surface CSS + Viewer popup mirror
- feat: built-in mermaid render (3 surface)
- fix:  Inspector AI tab を「Hints」に rename
- perf: tag-target options を lazy-build on mousedown (-47%)
- docs: session handoff 3rd update

【テスト推奨】
- (コミット内容から自動生成、下記ルール参照)

【公開URL】
- 安定版:    https://sm06224.github.io/PKC-Public/PKC2/
- マニュアル: https://sm06224.github.io/PKC-Public/PKC2-MANUAL/
- 開発版:    https://sm06224.github.io/PKC-Public/PKC2-DEV/
```

変更なし時:
```
変更なし。スキップ。(DEV: claude/pgc-xxx、前回と同ハッシュ)
```

複数DEV指定時は公開URLの開発版を展開:
```
- 開発版1 (pgc-100): https://sm06224.github.io/PKC-Public/PKC2-DEV-1/
- 開発版2 (pgc-101): https://sm06224.github.io/PKC-Public/PKC2-DEV-2/
```

### テスト推奨の生成ルール

コミットログの prefix から以下を判定して【テスト推奨】に列挙する。複数該当時は重複排除。

| コミット種別 | 推奨テスト観点 |
|---|---|
| `feat(inspector)` | Inspectorパネル全タブの表示・切替 |
| `feat(editor)` | テキスト入力・フォーマット・wordcount表示 |
| `feat(activity-bar)` | Activity Bar各タブの切替・バッジ表示 |
| `feat(textlog)` | ログ追加・フィルタ・検索 |
| `feat(todo)` | Todo作成・チェック・subtask |
| `feat(quick-open)` | Cmd+P での各モード起動 |
| `feat(mermaid)` | mermaidブロックの描画 (3 surface) |
| `perf` | 重いコンテナ(500件以上)でのレスポンス体感 |
| `fix` | 修正対象機能の基本動作 |
| `feat(s4-css)` / `feat(css)` | 各Viewのレイアウト崩れ目視確認 |
| `docs` | テスト不要 |

`docs` のみの場合は【テスト推奨】セクション自体を省略する。
