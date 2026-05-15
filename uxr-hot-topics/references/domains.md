# 事業ドメイン定義

UXR Hot Topics カードのドメインタグ・アクセント色・アイコンのマスターデータ。テンプレート側（`assets/template.html`）の CSS は本ファイルと同期させる。

## ドメイン一覧

| key | label | accent_hex | tag_bg | tag_fg | icon |
|------|-------|------------|---------|--------|------|
| `pay` | メルペイ | `#E84A3F` | `#FEDED9` | `#A92E22` | wallet |
| `mobile` | メルカリモバイル | `#D9A21F` | `#FFF1C2` | `#8A6A00` | smartphone |
| `coin` | メルコイン | `#2F5BC9` | `#DDE5FA` | `#1F47A8` | bitcoin |
| `money` | スマートマネー | `#6D49A3` | `#EBE0F5` | `#5A3D8C` | yen |

## ドメイン判定ルール

レポートタイトル・Objective から下記のキーワードでマッピングする。

| 含まれる語 | domain key |
|-----------|-----------|
| 「メルペイ」「加盟店」「Outer Pay」「Inner Buy」「無限決済」「Otoku」 | `pay` |
| 「メルカリモバイル」「MVNO」「回線」「端末セット」「キャリア」 | `mobile` |
| 「メルコイン」「BTC」「ビットコイン」「暗号資産」「つみたて」「HYBS」 | `coin` |
| 「スマートマネー」「借り換え」「借入」「与信」「ローン」 | `money` |

複数該当する場合は **メイン主語** で判定（例：「メルペイのつみたて連携」→ `pay`）。判定不能ならユーザーに確認する。

## アイコン SVG（インライン埋め込み用）

すべて Lucide スタイル（stroke-based, 13×13, stroke-width 2.2）。`currentColor` を使うので、親要素の `color` プロパティで色が変わる。

### wallet（メルペイ）

```html
<svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round"><path d="M21 12V7H5a2 2 0 0 1 0-4h14v4"/><path d="M3 5v14a2 2 0 0 0 2 2h16v-5"/><path d="M18 12a2 2 0 0 0 0 4h4v-4Z"/></svg>
```

### smartphone（メルカリモバイル）

```html
<svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round"><rect width="14" height="20" x="5" y="2" rx="2" ry="2"/><path d="M12 18h.01"/></svg>
```

### bitcoin（メルコイン）

```html
<svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round"><path d="M11.767 19.089c4.924.868 6.14-6.025 1.216-6.894m-1.216 6.894L5.86 18.047m5.908 1.042-.347 1.97m1.563-8.864c4.924.869 6.14-6.025 1.215-6.893m-1.215 6.893-3.94-.694m5.155-6.2L8.29 4.26m5.908 1.042.348-1.97M7.48 20.364l3.126-17.727"/></svg>
```

### yen（スマートマネー）

```html
<svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round"><path d="M12 9.5V21m0-11.5L6 4m6 5.5L18 4M6 15h12M6 11h12"/></svg>
```

## ドメインを追加するとき

未定義ドメイン（例：メルカード、新規プロジェクト等）が出てきた場合の手順：

1. **キー名を決める** — kebab-case 単語1つ（例：`card`, `nft`）
2. **色を選ぶ** — ブランド色を参考に、既存4色（赤・黄・青・紫）と区別可能なもの。緑・オレンジ・ピンクが候補
3. **CSS 変数を追加** — `assets/template.html` の `:root` に `--accent-{key}: #XXXXXX;`
4. **クラスを追加** — `.card.{key} { --card-accent: var(--accent-{key}); }` と `.domain-{key} { background: ...; color: ...; }`
5. **アイコンを Lucide から選んで SVG をコピー** — https://lucide.dev/ から該当アイコンの「Copy SVG」、stroke-width を 2.2 に修正
6. **本ファイルのテーブル・判定ルール・SVG セクションに追記**

色選定の参考（既存と被らない選択肢）：

| 候補色 | accent_hex | tag_bg | tag_fg |
|--------|------------|--------|--------|
| 緑 | `#1E8F5C` | `#D6F0E2` | `#0E5A37` |
| オレンジ | `#D96A20` | `#FCE0CC` | `#8E3F0E` |
| ピンク | `#C94476` | `#FBDDE9` | `#8A2548` |
| ティール | `#178F9B` | `#D0EEF2` | `#0B5A63` |
