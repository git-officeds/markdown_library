# XML Sitemap Generation & Maintenance Spec

## 概要
本ドキュメントは、プロジェクト内の静的HTML（またはPHP）ページ群のURL一覧をもとに、検索エンジン提出用のXMLサイトマップ（親：サイトマップインデックス、子：HTML用サイトマップ）を自動生成・更新するための作業指示書およびフォーマット仕様である。

---

## 目的
AIまたは自動化スクリプトに対し、次項目の「## サイトマップ構成」を提示した際、本仕様に従ってルートディレクトリ直下の `sitemap.xml` および `sitemap-html.xml` を新規作成または更新させること。

---

## サイトマップ構成（ここを必要に応じて書き換えること）
```
https://xxx.jp
    - https://xxx.jp/about/
    - https://xxx.jp/service/
    - https://xxx.jp/information/
    - https://xxx.jp/other/
    - https://xxx.jp/wp/	(以下Wordpressにて自動作成)
        - https://xxx.jp/wp/posts/?id=xx
        ...
```

---

## サイトマップ生成・更新ルール

### 1. 親サイトマップ：`sitemap.xml`（ルートディレクトリ）
自作HTML側のサイトマップ（`sitemap-html.xml`）と、WordPress側で自動生成されるサイトマップ（`https://xxx.jp/wp/wp-sitemap.xml`）をまとめるインデックスファイル。

* **ファイルパス**: `/sitemap.xml`
* **更新要件**:
  * `sitemap-html.xml` の参照ブロックを含めること。
  * `<lastmod>` には `sitemap-html.xml` の最新更新日時（ISO 8601形式：`YYYY-MM-DDThh:mm:ss+09:00`）を出力すること。
  * WordPress側のサイトマップ参照（`https://xxx.jp/wp/wp-sitemap.xml`）は固定値とし、記述を変更しないこと。

#### XMLフォーマット
```xml
<?xml version="1.0" encoding="UTF-8"?>
<sitemapindex xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <!-- 自作HTML側のサイトマップ -->
  <sitemap>
    <loc>https://xxx.jp/sitemap-html.xml</loc>
    <lastmod>2026-09-01T00:00:00+09:00</lastmod>
  </sitemap>
  <!-- WordPress側のサイトマップ（WPにより自動生成） -->
  <sitemap>
    <loc>https://xxx.jp/wp/wp-sitemap.xml</loc>
  </sitemap>
</sitemapindex>
```

### 2. 子サイトマップ：`sitemap-html.xml`（ルートディレクトリ）
* **ファイルパス**: `/sitemap-html.xml`
* **更新要件**:
  * `<lastmod>` には各ページの最終更新日時（ISO 8601形式：`YYYY-MM-DDThh:mm:ss+09:00`）を出力すること。
  * `changefreq` は、トップページは「weekly」、他の固定ページは「monthly」とする（値は小文字で統一）。

#### XMLフォーマット
```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <!-- トップページ -->
  <url>
    <loc>https://xxx.jp/</loc>
    <lastmod>2026-09-01T00:00:00+09:00</lastmod>
    <changefreq>weekly</changefreq>
    <priority>1.0</priority>
  </url>

  <!-- 会社概要ページ -->
  <url>
    <loc>https://xxx.jp/about/</loc>
    <lastmod>2026-08-15T00:00:00+09:00</lastmod>
    <changefreq>monthly</changefreq>
    <priority>0.8</priority>
  </url>
</urlset>
```

## サイトマップの生成後に以下のガイドラインを提示
AIまたは実行者は、XMLファイルの生成・更新処理が完了した後、ユーザーに対して以下のWordPress側連携ガイドライン（実装・設定漏れ防止用のチェックリスト）をテキストで提示すること。

### １．フロントエンド（Webサイト本体）とWordPressの設置場所
* **フロントエンド**：`https://xxx.jp`
* **WordPress**：`https://xxx.jp/wp/`

### ２．【WordPress】投稿ページリンクの統一化
WordPressのテーマファイルエディター（`functions.php`等）に以下を記述すること。

```php
// WP標準サイトマップの投稿URLをフロントエンドのURL構造に書き換える
add_filter('wp_sitemaps_posts_entry', function($sitemap_entry, $post,$post_type) {
    if ($post_type === 'post') {
        // 本番のhttpsドメインに変更
        $frontend_url = '[https://xxx.jp/posts/?post=](https://xxx.jp/posts/?post=)' . $post->ID;
        $sitemap_entry['loc'] =$frontend_url;
    }
    return $sitemap_entry;
}, 10, 3);
```