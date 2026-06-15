# public-pages

Forte の公開静的ページ（プライバシーポリシー・利用規約など）。

- 公開 URL: `https://static.forte.inc/<product>/...`
- 配信対象は **`site/` 配下のみ**（Cloudflare Worker + 静的アセット）。
- プロダクト別にディレクトリを分ける（例: `site/inshade/`, `site/sleep-log/`）。

## ディレクトリ構成（URL対応）

| ファイル | 公開URL |
|---|---|
| `site/inshade/privacy-policy.html` | `https://static.forte.inc/inshade/privacy-policy.html` |
| `site/inshade/terms.html` | `https://static.forte.inc/inshade/terms.html` |
| `site/sleep-log/privacy-policy.html` | `https://static.forte.inc/sleep-log/privacy-policy.html` |
| `site/privacy-policy.html` | `https://static.forte.inc/privacy-policy.html`（汎用・後方互換） |

英語版は `*.en.html`。

## デプロイ

```bash
npx wrangler deploy
```

main への push では自動デプロイされない（直接アップロード方式）。更新したら上記を実行する。
