# Pilotdone Website

靜態網站，host 在 `https://pilotdone.com`，主要為了 Apple/Google Store 上架時要求的
**Privacy Policy URL** 與 **Terms of Service URL**。

## 內容

| 路徑 | 來源 |
|------|------|
| `/` | landing 頁（hard-coded 於 build script） |
| `/privacy/` | 由 [`docs/PRIVACY_POLICY.md`](https://github.com/VincentCorgi/Pilotdone/blob/develop/docs/PRIVACY_POLICY.md) 轉出 |
| `/terms/` | 由 [`docs/TERMS_OF_SERVICE.md`](https://github.com/VincentCorgi/Pilotdone/blob/develop/docs/TERMS_OF_SERVICE.md) 轉出 |

> ⚠️ 修法律文件請改 **App repo** 的 markdown，再跑 build script，**不要**直接改這個 repo 的 HTML。

## 部署（GitHub Pages）

### 一次性設定

1. 在 GitHub 開新 repo（建議 `pilotdone-website`，public 即可）
2. 把這個資料夾 push 上去：
   ```bash
   cd pilotdone-website
   git init
   git add .
   git commit -m "init: Pilotdone marketing site"
   git branch -M main
   git remote add origin https://github.com/<你的帳號>/pilotdone-website.git
   git push -u origin main
   ```
3. Repo Settings → **Pages**：
   - Source：`Deploy from a branch`
   - Branch：`main` / `/ (root)`
   - Custom domain：`pilotdone.com`（會自動讀 `CNAME` 檔）
   - **Enforce HTTPS**：✓
4. 等 SSL 簽發完成（5-10 分鐘）

### GoDaddy DNS 改 5 條 record

進 GoDaddy → Pilot Done → **網域 → DNS 管理**：

| 動作 | 類型 | 名稱 | 值 |
|---|---|---|---|
| **刪除** | A | @ | 原本指到 "WebsiteBuilder Site" 的那條 |
| **新增** | A | @ | `185.199.108.153` |
| **新增** | A | @ | `185.199.109.153` |
| **新增** | A | @ | `185.199.110.153` |
| **新增** | A | @ | `185.199.111.153` |
| **修改／確認** | CNAME | www | `<你的帳號>.github.io.` |

> 若 `www` 那條 CNAME 已存在但值不對，就改成 `<你的帳號>.github.io.`（注意結尾的點）。

DNS 改完 → 等 5-30 分鐘生效 → 開 `https://pilotdone.com` 應該看到網站。

## 更新內容

法律文件 / landing 文案要改：

1. 在 **App repo** (`Pilotdone/`) 改 `docs/PRIVACY_POLICY.md` 或 `docs/TERMS_OF_SERVICE.md`
2. （或改 `tools/build_site.dart` 內的 `_buildIndex` 函式來改 landing）
3. 在 App repo 跑：
   ```bash
   dart run tools/build_site.dart
   ```
4. 把 `website/*` 內容覆蓋到本 repo
5. commit + push → GitHub Pages 自動重新部署

## 檔案結構

```
.
├── index.html          首頁
├── privacy/index.html  /privacy/
├── terms/index.html    /terms/
├── style.css           共用樣式（深藍 + 藍色 accent）
├── favicon.png         16×16
├── og-image.png        192×192，社群分享預覽
├── CNAME               pilotdone.com（給 GitHub Pages）
├── robots.txt
├── sitemap.xml
└── README.md           （本檔）
```

## 替代部署：Cloudflare Pages

不想用 GitHub Pages 可改 Cloudflare Pages：
1. cloudflare.com → Pages → Connect to Git → 選此 repo
2. Build command 留空、Build output `/`
3. Custom domains → `pilotdone.com` + `www.pilotdone.com`
4. 如果 nameserver 在 GoDaddy 不想搬，Cloudflare 會告訴你要加哪幾條 CNAME 給 GoDaddy
