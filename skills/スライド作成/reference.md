# スライド作成 詳細リファレンス

## 基本の使い方

```javascript
import { createDeck, draw, layout } from '${VAULT_PATH}/.claude/slide-assets/index.mjs';

const deck = createDeck('green', 'LT資料');  // テーマID + タイトル
deck.addTitleSlide({ event: 'イベント名', title: 'タイトル', author: '著者' });
deck.addBulletSlide('見出し', '', ['項目1', '項目2']);
await deck.save('${VAULT_PATH}/スライド/LT・発表/output.pptx');
```

## テーマ一覧

| ID | 名前 | 用途 | カラー |
|---|---|---|---|
| `quest` | Quest Proposal | 提案資料 | ティール + ゴールド |
| `green` | 社内勉強会 | LT・勉強会 | ダークグリーン + ライム |
| `internal` | 社内テンプレート | 社内発表 | グレー + ブルー |
| `corporate` | 社内DX Proposal | DX提案 | ティール + ゴールド |

## タイトルスライドの引数

**LT系 (green):**
```javascript
deck.addTitleSlide({ event: '社内LTイベント #7', title: 'タイトル', author: '名前' });
```

**LT系 (internal):**
```javascript
deck.addTitleSlide({ number: '第1回', title: 'タイトル', subtitle: '〜サブ〜', department: '部署', author: '名前' });
```

**提案系 (quest, corporate):**
```javascript
deck.addTitleSlide({ clientName: 'XX株式会社 御中', title: 'ご提案資料', dateStr: '2026/03/29' });
```

## 80種スライドメソッド一覧

### A. 構造 (7)
| # | メソッド | 引数 |
|---|---|---|
| 1 | `addTitleSlide(params)` | `{ event, title, author }` or `{ clientName, title, dateStr }` |
| 2 | `addAgendaSlide(items, highlightIdx?)` | items: string[], highlightIdx: number |
| 3 | `addSectionDivider(sectionNum, sectionTitle)` | '01', 'タイトル' |
| 4 | `addContentSlide(header, lead?, contentFn?)` | contentFn: (slide, area) => void |
| 5 | `addBlankSlide()` | — |
| 6 | `addEndSlide(text?)` | デフォルト: 'ご清聴ありがとうございました' |
| 7 | `addStatementSlide(text)` | インパクト大文字 |

### B. テキスト・箇条書き (8)
| # | メソッド | 引数 |
|---|---|---|
| 8 | `addBulletSlide(header, lead?, bullets, twoColumn?)` | bullets: string[] or [text, level][] |
| 9 | `addNumberedListSlide(header, lead?, items)` | items: string[] |
| 10 | `addTwoColumnTextSlide(header, lead?, leftText, rightText)` | テキスト2列 |
| 11 | `addQuoteSlide(quoteText, attribution?)` | 引用 |
| 12 | `addCalloutSlide(header, lead?, calloutText, bodyText?)` | 強調ボックス |
| 13 | `addDefinitionSlide(header, lead?, terms)` | terms: [{label, desc}] |
| 14 | `addHighlightBoxSlide(header, lead?, boxes)` | boxes: [{title, desc}] or string[] |
| 15 | `addQASlide(header, lead?, pairs)` | pairs: [{q, a}] |

### C. 比較・レイアウト (8)
| # | メソッド | 引数 |
|---|---|---|
| 16 | `addComparisonSlide(header, lead?, leftTitle, leftItems, rightTitle, rightItems)` | 左右比較 |
| 17 | `addThreeColumnSlide(header, lead?, columns)` | columns: [{title, items}] |
| 18 | `addBeforeAfterSlide(header, lead?, before, after)` | {title?, items} |
| 19 | `addProsConsSlide(header, lead?, pros, cons)` | string[] |
| 20 | `addVsSlide(header, lead?, leftLabel, leftItems, rightLabel, rightItems)` | VS対決 |
| 21 | `addAsIsToBeSlide(header, lead?, asIs, toBe)` | {title?, items or text} |
| 22 | `addGridSlide(header, lead?, numRows, numCols, data)` | data: [{text, header?}] |
| 23 | `addTwoByTwoMatrixSlide(header, lead?, axes, quadrants)` | axes: {x, y}, quadrants: [{title, desc}] |

### D. カード (8)
| # | メソッド | 引数 |
|---|---|---|
| 24 | `addNumberedCardsSlide(header, lead?, cards)` | cards: [{num?, title, desc}] |
| 25 | `addIconCardsSlide(header, lead?, cards)` | cards: [{icon, title, desc}] |
| 26 | `addFeatureCardsSlide(header, lead?, cards)` | cards: [{title, desc}] |
| 27 | `addStepCardsSlide(header, lead?, steps)` | steps: [{title, desc}] |
| 28 | `addStatCardsSlide(header, lead?, stats)` | stats: [{value, label, desc?, color?}] |
| 29 | `addProfileCardsSlide(header, lead?, profiles)` | [{name, role, desc?, initials?}] |
| 30 | `addPricingCardsSlide(header, lead?, plans, recommended?)` | plans: [{name, price, features}] |
| 31 | `addChecklistCardsSlide(header, lead?, cards)` | cards: [{title, items}] |

### E. データ・KPI (8)
| # | メソッド | 引数 |
|---|---|---|
| 32 | `addKpiSlide(header, lead?, kpis)` | kpis: [{value, label, color?}] |
| 33 | `addKpiWithTrendSlide(header, lead?, kpis)` | [{value, label, trend, change}] |
| 34 | `addMetricDashboardSlide(header, lead?, metrics)` | [{label, value, color?}] |
| 35 | `addProgressBarSlide(header, lead?, items)` | [{label, percent, color?}] |
| 36 | `addScoreCardSlide(header, lead?, scores)` | [{label, value, max?, desc?}] |
| 37 | `addBenchmarkSlide(header, lead?, benchmarks)` | [{label, own, benchmark, max}] |
| 38 | `addGaugeSlide(header, lead?, gauges)` | [{label, value, unit?, max?}] |
| 39 | `addStatHighlightSlide(header, lead?, stat, context?)` | stat: {value, label} |

### F. テーブル (6)
| # | メソッド | 引数 |
|---|---|---|
| 40 | `addTableSlide(header, lead?, colHeaders, rows)` | colHeaders: string[], rows: string[][] |
| 41 | `addComparisonTableSlide(header, lead?, colHeaders, rows, highlightCol?)` | ハイライト列 |
| 42 | `addFeatureMatrixSlide(header, lead?, features, products)` | ○×マトリクス |
| 43 | `addPlanComparisonSlide(header, lead?, plans, recommended?)` | [{name, theme, budget, detail}] |
| 44 | `addScheduleSlide(header, lead?, rows)` | rows: Object[] (キー=列名) |
| 45 | `addRaciSlide(header, lead?, tasks, roles)` | tasks: [{task, raci: {role: 'R/A/C/I'}}] |

### G. プロセス・フロー (8)
| # | メソッド | 引数 |
|---|---|---|
| 46 | `addTimelineSlide(header, lead?, steps)` | [{label, title, desc}] |
| 47 | `addVerticalTimelineSlide(header, lead?, steps)` | [{title, desc}] |
| 48 | `addPhaseSlide(header, lead?, phases)` | [{label, theme, detail}] |
| 49 | `addFlowSlide(header, lead?, nodes)` | [{text, highlight?, type?}] |
| 50 | `addCycleSlide(header, lead?, steps)` | [{title}] |
| 51 | `addFunnelSlide(header, lead?, levels)` | [{label, target?}] |
| 52 | `addPyramidSlide(header, lead?, levels)` | [{label}] |
| 53 | `addRoadmapSlide(header, lead?, milestones)` | [{period, title, desc, highlight?}] |

### H. 戦略・提案 (10)
| # | メソッド | 引数 |
|---|---|---|
| 54 | `addPurposeGoalSlide(header, purposeText, goalText)` | 趣旨→GOAL |
| 55 | `addSummarySlide(header, objective, strategies, tactics)` | 戦略マップ |
| 56 | `addYokenSlide(header, fields)` | [{label, value}] |
| 57 | `addProposalOverviewSlide(header, lead?, objective, strategies, tactics)` | = addSummarySlide |
| 58 | `addAnalysisSummarySlide(header, lead?, categories, pointText?)` | [{label, finding}] |
| 59 | `addIssueFactorSlide(header, lead?, issue, factors, objective?)` | factors: [{text}] |
| 60 | `addSegmentSlide(header, lead?, segments)` | [{label, desc}] |
| 61 | `addCommStrategySlide(header, lead?, segmentsWwh)` | [{who, what, how}] |
| 62 | `addSwotSlide(header, lead?, swot)` | {strengths, weaknesses, opportunities, threats} |
| 63 | `addValuePropositionSlide(header, lead?, pains, gains, products)` | string[] each |

### I. ビジュアル (7)
| # | メソッド | 引数 |
|---|---|---|
| 64 | `addImageSlide(header, lead?, imagePath, caption?)` | 画像1枚 |
| 65 | `addTwoImageSlide(header, lead?, left, right)` | {path, caption?} |
| 66 | `addImageWithTextSlide(header, lead?, imagePath, text, position?)` | position: 'left'/'right' |
| 67 | `addDiagramSlide(header, lead?, contentFn)` | = addContentSlide |
| 68 | `addScreenshotSlide(header, lead?, imagePath, annotations)` | string[] |
| 69 | `addGallerySlide(header, lead?, images)` | string[] or [{path, caption}] |
| 70 | `addMockupSlide(header, lead?, imagePath, description)` | = addImageWithTextSlide |

### J. チャート (5) — PptxGenJS ネイティブ
| # | メソッド | 引数 |
|---|---|---|
| 71 | `addBarChartSlide(header, lead?, chartData, opts?)` | [{name, labels, values}] |
| 72 | `addLineChartSlide(header, lead?, chartData, opts?)` | 同上 |
| 73 | `addPieChartSlide(header, lead?, chartData, opts?)` | 同上 |
| 74 | `addAreaChartSlide(header, lead?, chartData, opts?)` | 同上 |
| 75 | `addComboChartSlide(header, lead?, chartData, opts?)` | 同上 |

### K. 特殊 (5)
| # | メソッド | 引数 |
|---|---|---|
| 76 | `addThankYouSlide(text?, contact?)` | 締め |
| 77 | `addAppendixDivider()` | 付録区切り |
| 78 | `addDisclaimerSlide(text)` | 免責 |
| 79 | `addContactSlide(header, contacts)` | [{name, role, email}] |
| 80 | `addNextStepsSlide(header, lead?, steps)` | [{task, owner?, deadline?}] |

## カスタムレイアウト（contentFn）

contentFn を使うと任意のレイアウトを描画できる:

```javascript
deck.addContentSlide('カスタム', '', (slide, area) => {
  const t = deck.theme;  // テーマ設定にアクセス

  // draw.* でテーマ対応描画
  draw.addRect(slide, t, { x: area.x, y: area.y, w: area.w, h: 0.5, fill: t.colors.primary, text: 'テキスト' });
  draw.addText(slide, t, { x: area.x, y: area.y + 0.6, w: area.w, h: 1, text: '説明文', fontSize: 14 });
  draw.addLine(slide, t, { x: area.x, y: area.y + 1.7, w: area.w, color: t.colors.accent });

  // layout.* でグリッド計算
  const { colW, positions } = layout.columns(3, area.x, area.w, 0.15);
  positions.forEach((x, i) => {
    draw.addRect(slide, t, { x, y: area.y + 2, w: colW, h: 1, fill: t.colors.lightGray, text: `カード${i+1}` });
  });
});
```

## テーマカラーへのアクセス

```javascript
const t = deck.theme;
t.colors.primary    // メインカラー
t.colors.secondary  // サブカラー
t.colors.accent     // アクセントカラー
t.colors.darkText   // テキスト色
t.colors.lightGray  // 背景色
t.fonts.bodyJP      // 本文フォント
t.fontSizes.body    // 本文サイズ
```

## デザインルール（自動適用）

- **暗い背景** → 白文字自動適用 (draw.addRect の autoTextColor)
- **コンテンツエリア** はテーマごとに最適化済み
- 全メソッドでテーマのカラー/フォント/サイズが自動適用

## 数値制約（SKILL.mdの規定）

- ヘッダー: 30文字以下
- 本文1行: 40文字以下
- 1スライド総行数: 12行以下
- 1スライド総文字数: 400文字以下
- 箇条書き: 5項目以下（超過時は2列化 or 分割）
- テーブル: 1個/スライド
- フォント下限: 10pt
