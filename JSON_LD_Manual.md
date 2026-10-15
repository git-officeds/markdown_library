# JSON-LD 記述仕様書・サンプルコード集

本ドキュメントは、ウェブサイトにおける構造化データ（JSON-LD）の設置方針およびページ種別ごとの実装サンプルをまとめたリファレンスです。

---

## 1. 設置方針の要約

「トップページだけに構造化データを配置すれば十分」という説は誤り（または説明不足）です。
Googleなどの検索エンジンは**「各ページに書かれているコンテンツに対応する構造化データ」**を個別評価します。そのため、ページの役割に合わせた適切な JSON-LD を配置する必要があります。

### ページ種別ごとの役割と設置項目一覧

| ページ種別 | 主な役割・メリット | 設置する主な Schema タイプ |
| :--- | :--- | :--- |
| **トップページ** | 運営者情報・ブランドの証明、サイト内検索ボックスの提示 | `Organization` / `LocalBusiness`, `WebSite` |
| **一般の下層ページ** | 検索結果での階層表示（パンくず）の最適化、ページ内容の明示 | `BreadcrumbList`, `WebPage` |
| **投稿記事ページ** | 検索結果への日付・著者・画像表示、Google Discover 掲載率向上 | `BlogPosting`（または `Article`）, `BreadcrumbList` |

---

## 2. 実装サンプルコード

以下のコードを、各ページの HTML 内（`<head>` タグ内または `<body>` タグ内）に配置してください。

### ① トップページ用（Organization + WebSite）

組織情報（`Organization`）とサイト全体の情報（`WebSite`）を `@graph` を用いて1つのスクリプトに統合したフォーマットです。

```html
<script type="application/ld+json">
{
  "@context": "[https://schema.org](https://schema.org)",
  "@graph": [
    {
      "@type": "Organization",
      "@id": "[https://example.com/#organization](https://example.com/#organization)",
      "name": "株式会社サンプル",
      "url": "[https://example.com/](https://example.com/)",
      "logo": "[https://example.com/assets/images/logo.png](https://example.com/assets/images/logo.png)",
      "sameAs": [
        "[https://x.com/example](https://x.com/example)",
        "[https://www.facebook.com/example](https://www.facebook.com/example)"
      ]
    },
    {
      "@type": "WebSite",
      "@id": "[https://example.com/#website](https://example.com/#website)",
      "url": "[https://example.com/](https://example.com/)",
      "name": "サンプル公式サイト",
      "publisher": {
        "@id": "[https://example.com/#organization](https://example.com/#organization)"
      },
      "potentialAction": {
        "@type": "SearchAction",
        "target": "[https://example.com/search?q=](https://example.com/search?q=){search_term_string}",
        "query-input": "required name=search_term_string"
      }
    }
  ]
}
</script>
```

### ② 一般の下層ページ用（BreadcrumbList + WebPage）
サイト内の階層構造を示すパンくずリスト（`BreadcrumbList`）と、ページ概要（`WebPage`）のフォーマットです。

```html
<script type="application/ld+json">
{
  "@context": "[https://schema.org](https://schema.org)",
  "@graph": [
    {
      "@type": "BreadcrumbList",
      "itemListElement": [
        {
          "@type": "ListItem",
          "position": 1,
          "name": "ホーム",
          "item": "[https://example.com/](https://example.com/)"
        },
        {
          "@type": "ListItem",
          "position": 2,
          "name": "サービス紹介",
          "item": "[https://example.com/service/](https://example.com/service/)"
        },
        {
          "@type": "ListItem",
          "position": 3,
          "name": "Web制作サービス"
        }
      ]
    },
    {
      "@type": "WebPage",
      "name": "Web制作サービス概要 | 株式会社サンプル",
      "description": "株式会社サンプルの提供するWeb制作サービスの詳細ページです。"
    }
  ]
}
</script>
```

### ③ 定期的な投稿記事ページ用（BlogPosting + BreadcrumbList）
ブログやニュース記事のメタデータ（`BlogPosting`）と記事へのパンくずリストのフォーマットです。

```html
<script type="application/ld+json">
{
  "@context": "[https://schema.org](https://schema.org)",
  "@graph": [
    {
      "@type": "BlogPosting",
      "headline": "SEO対策における構造化データの活用法",
      "image": [
        "[https://example.com/images/blog-thumb.jpg](https://example.com/images/blog-thumb.jpg)"
      ],
      "datePublished": "2026-10-01T09:00:00+09:00",
      "dateModified": "2026-10-14T15:00:00+09:00",
      "author": {
        "@type": "Person",
        "name": "山田 太郎",
        "url": "[https://example.com/author/yamada/](https://example.com/author/yamada/)"
      },
      "publisher": {
        "@type": "Organization",
        "name": "株式会社サンプル",
        "logo": {
          "@type": "ImageObject",
          "url": "[https://example.com/assets/images/logo.png](https://example.com/assets/images/logo.png)"
        }
      },
      "description": "この記事では、構造化データ（JSON-LD）を正しく実装して検索エンジンからの評価を高める方法を解説します。"
    },
    {
      "@type": "BreadcrumbList",
      "itemListElement": [
        {
          "@type": "ListItem",
          "position": 1,
          "name": "ホーム",
          "item": "[https://example.com/](https://example.com/)"
        },
        {
          "@type": "ListItem",
          "position": 2,
          "name": "ブログ一覧",
          "item": "[https://example.com/blog/](https://example.com/blog/)"
        },
        {
          "@type": "ListItem",
          "position": 3,
          "name": "SEO対策における構造化データの活用法"
        }
      ]
    }
  ]
}
</script>
```

## 3. テスト・検証ツール
記述後のエラーチェックや表示確認には、以下の公式ツールを利用します。
- リッチリザルト テスト：Googleの検索拡張表示（リッチリザルト）への対応状況を検証
- Schema Markup Validator：Schema.orgの仕様に沿った純粋な構文チェックを実施