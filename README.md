# IELTS Atlas - LazyCat App

[IELTS Atlas](https://github.com/gtiipoi/ielts-practice)（雅思阅读与听力练习系统）的懒猫微服（LazyCat）打包。

## 模式

- **静态 Web 应用**：`content/` 为上游运行时文件（`index.html` + `css/` + `js/bundles/` + `assets/`，与上游 `developer/release.sh` 分发包白名单一致），直接打包进 LPK，无 Docker 镜像。
- **自动跟上游**：`sync-upstream.yml` 每日检查上游 `gtiipoi/ielts-practice` main HEAD（上游无 tag/release，以 commit SHA 为准）；有变化时重建 bundles、更新 `content/`，自动 patch +1 打 `v*` tag 触发发布（`.upstream-sha` 记录已同步的 SHA 基线）。
- **喵喵商店发布**：仅私有商店（MiaoMiao private store），`lazycat.yml` 由 tag 推送 / 定时 / 手动触发。

## 结构

| 文件 | 说明 |
| --- | --- |
| `package.yml` | 包元数据（`community.lazycat.app.ielts-practice`） |
| `lzc-manifest.yml` | 静态路由：`/` → `file:///lzcapp/pkg/content/` |
| `lzc-build.yml` | 构建配置（`contentdir: ./content`） |
| `content/` | 上游运行时文件（自动同步） |
| `icon.png` | 图标（上游 favicon） |
| `.github/lazycat-action.yml` | [lazycat-github-action](https://github.com/ca-x/lazycat-github-action) 配置 |
| `.github/workflows/sync-upstream.yml` | 上游同步 + 发版 |
| `.github/workflows/lazycat.yml` | 构建 + 发布喵喵商店 |

## 更新流程（全自动）

每日 `sync-upstream.yml`（约北京时间 14:13）自动执行；也可手动 Run workflow 立即检查：

1. 对比上游 main HEAD SHA 与 `.upstream-sha`，无变化则跳过；
2. clone 上游 → `node scripts/build-bundles.mjs` 重建 bundles；
3. 按上游分发包白名单更新 `content/`，`package.yml` 版本 patch +1，提交并打 `v*` tag；
4. tag 触发 `lazycat.yml` 构建并发布喵喵商店。

## 使用提示

- 数据保存在浏览器本地存储（localStorage / IndexedDB），首次练习时需允许弹窗。
- 上游 README 声明：题源等第三方内容仅建议个人学习使用，请控制访问范围。

## 所需 Secrets

| Secret | 说明 |
| --- | --- |
| `APPSTORE_URL` / `APPSTORE_TOKEN` | 喵喵商店 API 地址与发布令牌 |
| `APP_ID` | 可选 |
| `PRIVATE_STORE_GROUP_CODES` | 可选，私有分组码 |

## 许可

代码 GPL-3.0（上游）；题源与素材版权归原权利人。
