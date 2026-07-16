# Hydro XCPCIO ScoreBoard Better

一个基于 Hydro 官方 XCPCIO 榜单插件改进的比赛榜单。

> 本项目是社区维护的衍生版本，并非 Hydro 或 XCPCIO 的官方发行版。

## 改进内容

- 支持在 Hydro 插件后台配置中设置正式队伍的默认金、银、铜牌数量。
- 支持通过 URL 参数临时覆盖后台奖牌配置。
- Team 列显示 `昵称 (账号)`；没有昵称或昵称与账号相同时仅显示账号。
- 使用 XCPCIO Board 展示 Hydro 的 ICPC/CCPC 比赛榜单。
- 移除外榜推送配置、令牌及定时推送任务。

奖牌数量示例：

```text
/d/<domainId>/contest/<contestId>/scoreboard/xcpcio?gold=3&silver=5&bronze=7&group=official
```

在 Hydro 的插件配置中设置 `gold`、`silver`、`bronze`，即可作为所有 XCPCIO 榜单的默认奖牌数量。URL 中的同名参数会覆盖本次访问的后台配置。奖牌配置作用于正式队伍，因此应选择 `official` 分组。

## 本地开发

```bash
npm install --ignore-scripts
npx tsx install.ts
```

第二条命令会从 `@xcpcio/board-app` 复制榜单前端资源到 `public/`。在完整 Hydro 环境中安装插件时，可直接使用包内置的 `postinstall` 流程。

## 致谢与上游来源

本项目衷心致敬并感谢以下开源项目及其贡献者：

- [xcpcio/xcpcio](https://github.com/xcpcio/xcpcio)：提供优秀的 ICPC/CCPC 榜单可视化引擎与数据格式。本项目使用的 `@xcpcio/board-app` 及相关 XCPCIO 组件遵循其 MIT License。
- [hydro-dev/Hydro](https://github.com/hydro-dev/Hydro)：提供 Hydro Online Judge、插件 API，以及本项目所基于的 `@hydrooj/scoreboard-xcpcio` 原始实现。

感谢 Hydro 与 XCPCIO 社区为程序设计竞赛基础设施所做的持续投入。

## 许可证

本仓库中基于 Hydro 修改的代码依据 [GNU Affero General Public License v3.0 or later](LICENSE) 发布，并保留 Hydro 上游的版权与来源说明。XCPCIO 依赖及其代码仍分别遵循 XCPCIO 上游声明的 MIT License。
