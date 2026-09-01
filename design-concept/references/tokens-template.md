# Design Tokens Specification: <Product Name>

本書は、<Product Name> のUI構築で使用するデザイントークン（カラー、タイポグラフィ、スペーシング、エレベーション等）の定義書です。
フロントエンド実装時は本仕様に基づきトークンを参照してください。

---

## 1. カラーパレット (Color Palette)

### 1.1 Brand & Neutral Colors
| トークン名 | 用途 | HEX / 基準値 | 備考 |
| :--- | :--- | :--- | :--- |
| `primary` | 主要アクション、ブランド訴求 | `#...` | コントラスト比 4.5:1 以上確保 |
| `primary-hover` | プライマリアクションのホバー | `#...` | |
| `secondary` | 補助アクション、副次要素 | `#...` | |
| `surface` | カード、モーダル、ドロップダウン背景 | `#...` | |
| `background` | ページ全体の基礎背景 | `#...` | |
| `border` | 境界線、ディバイダー | `#...` | |

### 1.2 Text Colors
| トークン名 | 用途 | HEX / 基準値 | 備考 |
| :--- | :--- | :--- | :--- |
| `text-primary` | 本文、主要タイトル | `#...` | 最高コントラスト |
| `text-secondary` | 補足説明、ラベル、メタ情報 | `#...` | |
| `text-muted` | プレースホルダー、非活性テキスト | `#...` | |
| `text-on-primary` | プライマリ背景上の文字 | `#ffffff` | |

### 1.3 Semantic Colors
| トークン名 | 用途 | HEX / 基準値 |
| :--- | :--- | :--- |
| `success` | 完了、成功、安全ステータス | `#...` |
| `warning` | 注意、警告、保留 | `#...` |
| `danger` / `error` | エラー、破壊的アクション、危険 | `#...` |
| `info` | 情報提供、案内 | `#...` |

---

## 2. タイポグラフィ (Typography)

- **フォントファミリー**:
  - 本文・UI: `...`
  - コード・等幅: `...`

| スタイル名 | フォントサイズ | 行間 (Line Height) | ウェイト (Weight) | 用途 |
| :--- | :--- | :--- | :--- | :--- |
| `display` | `32px (2rem)` | `1.2` | `700 (Bold)` | メインヒーロー |
| `heading-1` | `24px (1.5rem)` | `1.3` | `700 (Bold)` | ページタイトル |
| `heading-2` | `20px (1.25rem)` | `1.4` | `600 (SemiBold)` | セクション見出し |
| `heading-3` | `16px (1rem)` | `1.5` | `600 (SemiBold)` | カード・ブロック見出し |
| `body` | `14px (0.875rem)` | `1.5` | `400 (Regular)` | 基本本文、入力フォーム |
| `caption` | `12px (0.75rem)` | `1.4` | `400 (Regular)` | 補足テキスト、タグ |

---

## 3. スペーシング & サイジング (Spacing & Sizing)

4px / 8px の偶数スケールを基本とします。

| トークン名 | 値 (px / rem) | 用途例 |
| :--- | :--- | :--- |
| `space-xs` | `4px (0.25rem)` | アイコンとテキストの間隔、極小インセット |
| `space-sm` | `8px (0.5rem)` | バッジのパディング、密接な要素間 |
| `space-md` | `16px (1rem)` | カード内パディング、フォームフィールド間 |
| `space-lg` | `24px (1.5rem)` | コンポーネント間のグリッドギャップ |
| `space-xl` | `32px (2rem)` | セクション間のマージン |
| `space-2xl`| `48px (3rem)` | ページトップ・ボトムの余白 |

---

## 4. コンポーネント造形 (Shape & Elevation)

### 4.1 角丸 (Border Radius)
| トークン名 | 値 | 用途例 |
| :--- | :--- | :--- |
| `radius-sm` | `4px` | バッジ、タグ、インラインコード |
| `radius-md` | `8px` | ボタン、インプット、ドロップダウン |
| `radius-lg` | `12px` | カード、モーダルダイアログ |
| `radius-full`| `9999px` | アバター、ピルボタン |

### 4.2 エレベーション / 陰影 (Elevation & Shadow)
| トークン名 | CSS Box-Shadow 値 | 用途例 |
| :--- | :--- | :--- |
| `shadow-none`| `none` | フラット要素 |
| `shadow-sm` | `0 1px 2px 0 rgba(0, 0, 0, 0.05)` | 通常カード、テーブル |
| `shadow-md` | `0 4px 6px -1px rgba(0, 0, 0, 0.1)` | ホバー時カード、ドロップダウンメニュー |
| `shadow-lg` | `0 10px 15px -3px rgba(0, 0, 0, 0.1)`| モーダルダイアログ、トースト通知 |

---

## 5. 実装用コードスニペット

プロジェクトのフロントエンド技術スタックに応じた設定コードです。
（※プロジェクト環境に合わせて出力します）

```css
/* 例: Vanilla CSS / CSSカスタムプロパティ */
:root {
  /* Colors */
  --color-primary: #...;
  --color-primary-hover: #...;
  --color-surface: #...;
  --color-background: #...;
  --color-text-primary: #...;
  --color-text-secondary: #...;
  
  /* Typography */
  --font-family-base: ...;
  
  /* Spacing */
  --spacing-xs: 4px;
  --spacing-sm: 8px;
  --spacing-md: 16px;
  --spacing-lg: 24px;
  
  /* Radius & Shadow */
  --radius-md: 8px;
  --shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
}
```
