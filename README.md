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
| `site/blur/privacy-policy.html` | `https://static.forte.inc/blur/privacy-policy.html` |
| `site/privacy-policy.html` | `https://static.forte.inc/privacy-policy.html`（汎用・後方互換） |

英語版は `*.en.html`。

| `site/inshade/min-version.json` | `https://static.forte.inc/inshade/min-version.json` |

`min-version.json` は inShade の**強制アップデート**判定（`ios.minBuild` / `android.minVersionCode`）。
**0 = 誰もブロックしない**。アプリ側は fail-open（届かない・壊れている・異常値なら素通り）だが、
**ここを誤って上げると、そのビルド未満のユーザーが全員起動不能になり、更新版を出しても回復できない**
（起動できないアプリからは何も操作できない）。値を上げるのは慎重に。

## デプロイ

**main に push するだけ。** `forte-static` は GitHub 連携（Workers Builds）なので、
push すると自動でビルド・デプロイされる（**反映まで約1分**）。

### ⚠️ `npx wrangler deploy` は使わないこと

手元から `wrangler deploy` を叩くと、**本番は一切変わらないのに成功したように見える**（実害を出した）。

- 本物の `forte-static` は **「Hirono@forte.inc's Account」**（`9b86416d9ffa348446df5967fd9bf1d1`）にあり、
  `static.forte.inc` が紐付いている。
- 一方ローカルの wrangler CLI は **`hajiming@gmail.com` の「hajipion」アカウント**にログインしていることがあり、
  その場合 **別アカウントの同名 `forte-static`（ドメイン未紐付けの分身）** に配信される。
- wrangler は "Uploaded" / "Current Version ID" を出し、`deployments list` も 100% と表示するので**成功に見える**。
  唯一の手掛かりは **`No targets deployed for forte-static`**（＝このワーカーにドメインが紐付いていない）。

### 反映確認（必ずやる）

デプロイのログを信じない。**公開 URL を実際に叩いて確認する**:

```bash
curl -s -o /dev/null -w "%{http_code}\n" https://static.forte.inc/inshade/min-version.json
```

既存ファイルは 200 なのに**新規追加したファイルだけ 404** なら、「配信は生きているが自分の変更が届いていない」状態。
