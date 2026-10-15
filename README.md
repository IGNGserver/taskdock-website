# TaskDock 官网 · TaskDock Website

TaskDock 的产品官网与下载入口。本仓库只承载官网站点，产品源码与发版在独立仓库维护：

- 产品仓库（唯一事实来源）：<https://github.com/IGNGserver/taskdock>
- 产品定位：面向个人开发者的离线优先、自托管任务工作台
- 托管方案：Cloudflare Pages Direct Upload（见 [`docs/HOSTING.md`](docs/HOSTING.md)）

> **状态**：首版站点已部署上线：<https://taskdock-website.pages.dev/>。

## 仓库结构

| 路径 | 内容 |
| --- | --- |
| [`site/`](site/) | 站点本体：HTML、CSS、原生 ES 模块 JS、图标；原样作为 Cloudflare 输出目录 |
| [`docs/SITE.md`](docs/SITE.md) | 官网定位、站点结构、设计系统、内容事实来源 |
| [`docs/HOSTING.md`](docs/HOSTING.md) | Cloudflare 托管方案与上线检查清单 |
| [`docs/WORKSPACE.md`](docs/WORKSPACE.md) | 本机 worktree 工作区约定 |
| [`scripts/verify-repository.sh`](scripts/verify-repository.sh) | 仓库契约检查（本地与 CI 共用） |
| [`scripts/check-site.mjs`](scripts/check-site.mjs) | 站点静态检查：页面元信息、站内链接与锚点、外部资源、脚本语法、体积预算 |
| [`.github/workflows/ci.yml`](.github/workflows/ci.yml) | CI：仓库契约、站点检查、whitespace、凭据扫描 |

## 开发

站点没有构建步骤，本地预览只需一个静态服务器：

```bash
python3 -m http.server -d site 8765
```

然后打开 <http://127.0.0.1:8765/>。工作区、分支、验证与 PR 流程见 [`AGENTS.md`](AGENTS.md) 与 [`CONTRIBUTING.md`](CONTRIBUTING.md)。

## 许可

[MIT](LICENSE)
