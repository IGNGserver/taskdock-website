# 托管方案（Cloudflare）

> 目标：站点使用 Cloudflare Pages Direct Upload 托管。本文记录方案边界、
> 已具备的前提与上线检查清单；**清单未完成不得上线**。

## 已确认的前提（2026-10-13 核实）

- Cloudflare 账号已就绪，zone 与 Pages/Workers 均可用；
- 本仓库为独立公开仓库，CI 走 GitHub Actions；
- Cloudflare Pages 项目 `taskdock-website` 已创建，采用 Direct Upload；
- 默认公开地址为 `https://taskdock-website.pages.dev/`；正式自定义域名尚未选定。

## 构建配置（2026-10-15 定案）

站点无构建步骤，`site/` 目录原样发布：

| 项 | 值 |
| --- | --- |
| 构建命令 | 无（Pages 中留空，或填 `exit 0`） |
| 输出目录 | `site` |
| 响应头 | `site/_headers`（CSP、`nosniff`、`frame-ancestors 'none'`、静态资源缓存）；Pages 与 Workers 静态资源都识别该文件 |
| 404 | `site/404.html`，资源使用根路径 `/assets/...` |
| 外部请求 | 仅浏览器读取 `https://api.github.com/repos/IGNGserver/taskdock/releases`（CSP `connect-src` 已放行） |

## 当前方案（2026-10-15 定案）

- Pages 项目：`taskdock-website`；部署类型：Direct Upload；生产分支元数据：`main`。
- 站点目录 `site/` 原样上传，无构建步骤；仓库合并不会自动触发生产发布。
- 首次生产部署：`c83ed42a-87fc-4f3f-a7f7-a8e6db7336a9`，对应提交 `ba46b7c`。
- 默认公开地址：<https://taskdock-website.pages.dev/>。
- 当前没有配置自定义域名；`taskdock.pages.dev` 是产品站地址，不属于本官网项目，不能覆盖。
- 后续生产发布沿用 Direct Upload，并在发布前记录部署 ID；长期自动发布流程待补充最小权限的 CI 凭据后再启用。

## 候选方案（实现时二选一）

| 方案 | 说明 | 何时适用 |
| --- | --- | --- |
| Cloudflare Pages（Git 集成） | 连接本仓库，push / PR 自动构建与预览 | 静态输出，希望 PR 预览部署 |
| Cloudflare Pages（Direct Upload） | CI 构建后 `wrangler pages deploy` 上传 | 需要构建缓存或自定义流水线 |
| Workers 静态资源 | `wrangler deploy` 发布 assets | 需要边缘逻辑（重定向、API 代理等）时 |

决策点：是否需要边缘逻辑、构建时长与缓存、预览部署方式。

## 上线检查清单

- [x] 前端技术栈与构建命令定案，登记到 `AGENTS.md` 并接入 CI（零依赖静态站点，`node scripts/check-site.mjs`）；
- [x] 创建 Cloudflare Pages 项目 `taskdock-website`，采用 Direct Upload；
- [x] 默认 Pages 地址 `https://taskdock-website.pages.dev/` 已配置并验证 HTTPS；
- [ ] 若启用 CI 上传：在 GitHub 仓库 Secrets 配置最小权限的 `CLOUDFLARE_API_TOKEN` 与 `CLOUDFLARE_ACCOUNT_ID`；凭据不得提交；
- [ ] 自动部署工作流尚未配置；当前由受控 Direct Upload 发布，并记录部署 ID；
- [x] 已验证首页、下载页、404、CSP、安全响应头、产品仓库链接与 Releases 链接；
- [ ] 评估是否将产品仓库 `homepageUrl`（当前为空）改为官网地址；
- [ ] 选定正式自定义域名后补 `og:image`（需要绝对地址）、`sitemap.xml` 与 `robots.txt` 中的 Sitemap 行。

## 回滚

- 当前部署回滚点：`c83ed42a-87fc-4f3f-a7f7-a8e6db7336a9`（提交 `ba46b7c`）。
- Pages：在 Cloudflare Pages 项目中选择上一成功部署版本恢复；发布前先记录新的部署 ID。
- 域名变更：修改 DNS 记录前记录原值，回滚即恢复原记录。
