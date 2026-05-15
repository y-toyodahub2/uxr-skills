---
name: uxr-hot-topics
description: This skill should be used ONLY when the user explicitly invokes "/uxr-hot-topics". Generates UXR Insight Cards — one self-contained HTML file per project (1 project = 1 card), aggregated by an index.html with domain/theme filters. Cards are color/icon-tagged by business domain (メルペイ / メルカリモバイル / メルコイン / スマートマネー、拡張可能). Updates the index.html whenever a new card is added so the deck stays in sync. Do NOT auto-trigger from natural-language phrases — only the explicit slash command.
---

# UXR Insight Cards

UXRレポート1本＝1HTMLとして「Insight Card」を蓄積し、index.htmlで横断ソート・カテゴリ集約できるようにする。Slack共有・社内資料・将来的なカード式リサーチライブラリ用途。

## 原則

- **1 project = 1 card = 1 URL** — 各カードは自己完結HTML（CSSインライン）。単独でSlack/メールに共有可能
- **メタ情報は HTML の `<meta>` タグに格納** — `uxr-domain` / `uxr-themes` / `uxr-report-code` / `uxr-card-title`。index 再生成時はこれをスキャン
- **index.html はカード追加のたびに再生成** — 既存のindexを手で編集せず、ディレクトリの *.html を全スキャンして上書き
- **大テーマ粒度のタグを3つ** — 「行動カテゴリ／サービス名／領域」（例：`#決済行動 #メルペイ #特約加盟店`）。小粒度な insight キーワードは避ける
- **小さく、密度高く** — 1カードが1画面（~800px縦）に収まる前提でサイジング

## ファイル構成

保存先: `~/claude-workspace/UXR_Insight_Cards/`（推奨）または `<project>/insight_cards/`

```
insight_cards/
├── index.html                              # 自動再生成
├── 2604_kameiten_poc.html                  # 1 card = 1 file
├── 2604_mvno_tanmatsu.html
├── 2603_hybs_tsumitate.html
└── ...
```

**ファイル名規約**: `<YYMM>_<short_name>.html`
- YYMM: レポートのレポートコード前2桁（例：`2604` = 26年4月）
- short_name: タイトルから2-3語のスネークケース要約（例：`kameiten_poc`, `mvno_tanmatsu`, `hybs_tsumitate`）

## プロセス

### Step 1: ユーザーからインプット収集

PREPで順に確認する。一度に全部聞かない。

1. **モード確認** — 新規カード追加（1本/複数本）か、index再生成のみか
2. **保存先ディレクトリ** — `~/claude-workspace/UXR_Insight_Cards/` を提案。初回なら `mkdir -p` 確認
3. **レポートサマリー** — Slack形式（Title / Author / Objective / Key Insight / Recommendation）
4. **ドメイン判定の確認** — タイトル・Objectiveから自動判定するが、曖昧なら確認

### Step 2: ドメイン・テーマ判定

`references/domains.md` を参照：

- ドメインキー4種（pay / mobile / coin / money）から1つ選ぶ
- 大テーマタグを **3つ** 抽出：
  - **1つ目** = 行動カテゴリ（決済行動、購買行動、オンボーディング、投資行動、通信契約 等）
  - **2つ目** = サービス名（メルペイ、メルカリモバイル、メルコイン、スマートマネー、メルカード 等）
  - **3つ目** = 領域・施策（特約加盟店、中古端末、つみたて、借り換え 等）

### Step 3: カードHTMLを生成

1. `assets/card_template.html` を読み込む
2. プレースホルダを置換：

| トークン | 例 |
|---------|-----|
| `{{CARD_TITLE}}` | `特約加盟店PoC – NF/CB未達要因の特定` |
| `{{REPORT_CODE}}` | `2604_特約加盟店PoC` |
| `{{CARD_ID}}` | `CARD · 2604_PAY_01` |
| `{{DOMAIN_KEY}}` | `pay` |
| `{{DOMAIN_LABEL}}` | `メルペイ` |
| `{{ACCENT_HEX}}` | `#E84A3F` |
| `{{DOMAIN_TAG_BG}}` | `#FEDED9` |
| `{{DOMAIN_TAG_FG}}` | `#A92E22` |
| `{{DOMAIN_ICON_SVG}}` | SVG文字列（`references/domains.md` 参照） |
| `{{TOPBAR_RIGHT}}` | `2604 / pay` |
| `{{THEMES_CSV}}` | `決済行動,メルペイ,特約加盟店`（カンマ区切り、meta用） |
| `{{OBJECTIVE_HTML}}` | Objective本文（`<strong>` で太字化） |
| `{{INSIGHTS_LI_HTML}}` | `<li class="insight">...</li>` × N |
| `{{RECO_LI_HTML}}` | `<li>...</li>` × N |
| `{{THEME_TAGS_HTML}}` | `<span class="theme-tag">#xxx</span>` × 3 |

3. 保存：`<保存先>/<YYMM>_<short_name>.html`

### Step 4: 文章リライトのルール

Slack 原文をそのまま貼らない。読みやすさのために：

- **PREP で結論先出し** — 各 Insight / Reco の冒頭に結論文（**太字**）、その後に補足
- **太字 `<strong>` を要所に** — 数値・キーワード・対比軸を太字化。1 行に太字が 3 つを超えないように
- **記号で対比を強調** — 矢印 `→`、二項対立 `↔`、ハイライト `"..."` を活用
- **形容詞は数値に置換** — Slack 原文に数値があれば必ず保持
- **行数は Insight 3 行・Reco 2 行を目安**（カード密度の均一化）

### Step 5: index.html を再生成

1. 保存先ディレクトリの `*.html` をスキャン（`index.html` を除外）
2. 各ファイルの `<meta>` タグを読み取る：
   - `name="uxr-domain"` → ドメインキー
   - `name="uxr-themes"` → カンマ区切りテーマ
   - `name="uxr-report-code"` → レポートコード
   - `name="uxr-card-title"` → タイトル
3. Objective テキストも抽出（または、ユーザー入力時に控えておく）。1-2文の要約に圧縮（lib-summary用、~80字）
4. ファイル名（`YYMM_*.html`）で降順ソート（新着順）
5. `assets/index_template.html` の `{{LIB_CARDS_HTML}}` に挿入
6. `{{TOTAL_COUNT}}` / `{{UPDATED_DATE}}` も埋める

**lib-card エントリのテンプレート**（1カード分）:

```html
<a class="lib-card {{DOMAIN_KEY}}" href="{{FILENAME}}"
   data-domain="{{DOMAIN_KEY}}"
   data-themes="{{THEMES_CSV}}">
  <div class="lib-card-head">
    <span class="domain-tag domain-{{DOMAIN_KEY}}">
      {{DOMAIN_ICON_SVG_SMALL}}
      {{DOMAIN_LABEL}}
    </span>
  </div>
  <div class="report-code">{{REPORT_CODE}}</div>
  <h3>{{CARD_TITLE}}</h3>
  <p class="lib-summary">{{LIB_SUMMARY}}</p>
  <div class="lib-tags-row">
    <span class="theme-tag">#{{THEME_1}}</span>
    <span class="theme-tag">#{{THEME_2}}</span>
    <span class="theme-tag">#{{THEME_3}}</span>
  </div>
</a>
```

※ `{{DOMAIN_ICON_SVG_SMALL}}` は `width="11" height="11"` で他は同じSVG。

### Step 6: バリデーション

保存後に確認：

- カードHTMLがブラウザで開く（CSS崩れなし）
- meta タグが4つとも入っている
- index.html を開いて新カードが見える
- ドメインフィルタchipをクリックして絞り込みが動く

不具合があればユーザーに報告。

## アンチパターン

- ❌ カードを共有CSSで運用 → 自己完結性が壊れる、Slack共有時にスタイル崩壊
- ❌ 同じ `report-code` で複数カード → index でdupる。1プロジェクト1カードを守る
- ❌ 小粒度の insight キーワード（`#スイッチコスト` 等）をテーマタグに → ソート粒度が合わない
- ❌ index.html を手書きでメンテ → 不整合の温床。必ず再生成する
- ❌ カード間に prev/next ナビ → 並び順がソートで変わると不整合に。topbarの「← All cards」のみで運用

## 拡張

### 新規ドメインの追加

`references/domains.md` の手順に従う。CSS変数・タグ色・アイコンSVG・判定ルールを追加 → index_template.html のCSSと chip にも反映が必要（手動）。

### テーマタグの集計・横断ビュー

将来：index.html に「Themes」セクションを追加し、登場頻度順にテーマタグを並べる。テーマchipクリックで該当カードのみ表示するフィルタを追加。
