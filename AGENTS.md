# TaskDock 官网（taskdock-website）· 项目 Agent 规范

> 只写本仓库与设备级规范的**差异**。Git 纪律、worktree 路径与生命周期、基线解析、冲突处理、
> PR 流程一律见 `~/.qoder/coder-rules/global-rules.md`，不在这里重复；冲突时以设备级安全条款为准。

Collaboration: solo
Baseline: main
Release: none
Validation: `bash scripts/verify-repository.sh`、`node scripts/check-site.mjs`、`git diff --check`（三者即 CI `verify` 作业的内容；站点无构建步骤，没有 lint/test/build 命令）；视觉与交互改动另需在浏览器中做桌面与手机宽度的人工审查，并在 PR 中写明。
Worktree: `~/项目/.wt/TaskDock-site/<slug>`

## 这是什么

TaskDock 的官网与下载入口，源码与产品仓库分离维护：

- 产品仓库（唯一事实来源）：`https://github.com/IGNGserver/taskdock`（默认分支是 **master**，不是 main；产品文档在仓库 `docs/`，品牌资产在 `assets/branding/`）；
- 本仓库只承载站点（页面、样式、静态资源、站点文档），不得复制产品源码、安装包或发布资产。

**前端技术栈（2026-10-15 定案）**：零依赖静态站点。`site/` 下手写 HTML、CSS 与原生 ES 模块 JavaScript，
没有框架、没有 `package.json`、没有锁文件、没有构建步骤；`site/` 目录原样就是 Cloudflare 的输出目录。
引入任何依赖或构建步骤之前，必须先在 PR 中说明理由，并同步更新本文件、`docs/SITE.md`、`docs/HOSTING.md` 与 CI。
站点结构、设计系统与内容事实来源见 `docs/SITE.md`；托管配置见 `docs/HOSTING.md`。

## 集成补充

- 无额外门槛：按设备级规范第 5 节（solo）执行；`main` 已设分支保护，必须经 PR 且 CI `verify` 通过。
- 站点部署（Cloudflare）不随合并自动发生，见 `docs/HOSTING.md`。

## 发布

- 无版本化 Release（`Release: none`）；站点以持续部署方式发布，未启用前不得创建 tag 或 Release。

## 项目特殊限制

- 不得提交 `node_modules`、构建产物、`.wrangler/`、`.env*` 与任何凭据；`scripts/verify-repository.sh` 会拦截。
- 页面只加载站内资源（脚本、样式、字体、图片）；唯一的外部请求是浏览器读取 GitHub 公共 API 的最新版本号，失败时回退到 `site/assets/js/release.js` 里的兜底版本。新增外部来源必须同步修改 `site/_headers` 的 CSP。
- 品牌图标只能由产品仓库 `assets/branding/taskdock-icon.png` 缩放得到（产品仓库品牌规范禁止重绘为 SVG）；更换母版后按 `docs/SITE.md` 重新生成。
- 站点文案引用产品事实（功能、平台、版本）时必须与产品仓库当前 `README.md` / Release 对齐，不得编造未发布能力；产品当前发布均为 `2.0.0-rc.*` 预览版，不得在站点上描述为稳定版。
- 域名与 Cloudflare 项目名在 `docs/HOSTING.md` 记录；未定案前不得把猜测值写入代码或文档结论。

## 环境事实

- 开发位置：NAS 共享卷 `~/项目`（ubuntu-dev 与 macOS 开发机共用）；仓库锚点 `~/项目/项目网站/TaskDock-site`，任务 worktree 在 `~/项目/.wt/TaskDock-site/<slug>`。
- 本地预览：站点是纯静态文件，任意静态服务器指向 `site/` 即可（例如 `python3 -m http.server -d site 8765`）；`404.html` 使用根路径资源，需从站点根目录提供才能正确显示。
- 生产目标：Cloudflare 边缘网络（账号与 zone 已具备，站点项目与域名未定；不写凭据）。
