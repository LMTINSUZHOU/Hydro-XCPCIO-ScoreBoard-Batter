# Hydro XCPCIO ScoreBoard Better

一个基于 Hydro 官方 XCPCIO 榜单插件改进的比赛榜单。

> 本项目是社区维护的衍生版本，并非 Hydro 或 XCPCIO 的官方发行版。

## 改进内容

- 支持在 Hydro 插件后台配置中设置正式队伍的默认金、银、铜牌数量。
- 支持通过 URL 参数临时覆盖后台奖牌配置。
- Team 列显示 `昵称 (账号)`；没有昵称或昵称与账号相同时仅显示账号。
- 适配移动端：榜单支持触控横向滚动，排名与队伍列保持可见，菜单与“选项/筛选”弹窗针对窄屏和横屏重新排布。
- 统一各端队伍头像的显示框、比例与裁切方式，避免不同比例的原图撑大榜单列。
- 使用 XCPCIO Board 展示 Hydro 的 ICPC/CCPC 比赛榜单。
- 移除外榜推送配置、令牌及定时推送任务。

## 环境要求

- 已安装并能正常启动的 Hydro。
- Node.js 22 或更高版本。
- 能够执行 `git`、`npm`、`hydrooj` 和 `pm2` 命令。
- 使用运行 Hydro 的系统用户执行下列命令；常规安装通常为 `root`。

本仓库包含 `package-lock.json`，以下流程统一使用 npm。请勿在同一插件目录中混用 npm 和 Yarn。

## 安装

### 1. 下载插件

```bash
cd /root
git clone https://github.com/LMTINSUZHOU/Hydro-XCPCIO-ScoreBoard-Batter.git
cd /root/Hydro-XCPCIO-ScoreBoard-Batter
```

### 2. 安装依赖并生成榜单资源

```bash
npm ci
```

安装完成时应看到类似输出：

```text
Copied board app to /root/Hydro-XCPCIO-ScoreBoard-Batter/public
```

可以进一步确认必要文件和依赖均已生成：

```bash
test -f public/assets/board.html && echo "board assets OK"
node -p "require.resolve('lru-cache')"
```

### 3. 替换已经安装的官方同名插件

本项目与官方插件的包名均为 `@hydrooj/scoreboard-xcpcio`，不能同时启用。先查看当前注册项：

```bash
hydrooj addon list
```

如果列表中存在 `@hydrooj/scoreboard-xcpcio`，先将其移除：

```bash
hydrooj addon remove @hydrooj/scoreboard-xcpcio
```

如果列表中已经存在旧的本地目录注册项，也应使用完全相同的绝对路径移除：

```bash
hydrooj addon remove /root/Hydro-XCPCIO-ScoreBoard-Batter
```

### 4. 注册本插件

`hydrooj addon add` 必须使用包含 `package.json` 的插件目录绝对路径：

```bash
hydrooj addon add /root/Hydro-XCPCIO-ScoreBoard-Batter
hydrooj addon list
```

### 5. 重启并验证 Hydro

```bash
pm2 restart hydrooj
pm2 logs hydrooj --lines 100 --nostream
```

日志中不应出现 `Addon load fail`，并应能看到 Hydro 正常启动。如果你的 Hydro 不是由 PM2 管理，请改用实际使用的服务管理器重启。

## 后台配置

安装成功后进入 **控制面板 → 系统设置 → XCPCIO Scoreboard Config**：

- `gold`：默认金牌数量。
- `silver`：默认银牌数量。
- `bronze`：默认铜牌数量。
- `asDefault`：将 XCPCIO 设置为默认比赛榜单。
- `cacheTTL`：榜单缓存时间，单位为毫秒；`0` 表示不启用缓存。
- `cacheSize`：最多缓存的榜单状态数量。
- `override`：高级 XCPCIO 赛事配置覆盖项。

保存配置后建议重启 Hydro：

```bash
pm2 restart hydrooj
```

三种奖牌数量均为 `0` 时使用 XCPCIO 默认奖牌行为。后台配置作用于正式队伍，查看榜单时应选择 `official` 分组。

URL 中的同名参数可以只覆盖本次访问的后台配置，例如：

```text
/d/<domainId>/contest/<contestId>/scoreboard/xcpcio?gold=3&silver=5&bronze=7&group=official
```

## 更新

```bash
cd /root/Hydro-XCPCIO-ScoreBoard-Batter
git pull --ff-only
npm ci
pm2 restart hydrooj
pm2 logs hydrooj --lines 100 --nostream
```

`npm ci` 会按照锁文件重新安装依赖，并自动重新生成 `public` 榜单资源。

## 卸载

先使用安装时注册的绝对路径移除插件，然后重启 Hydro：

```bash
hydrooj addon remove /root/Hydro-XCPCIO-ScoreBoard-Batter
pm2 restart hydrooj
```

如需恢复官方版本：

```bash
yarn global add @hydrooj/scoreboard-xcpcio
hydrooj addon add @hydrooj/scoreboard-xcpcio
pm2 restart hydrooj
```

## 常见问题

### `Cannot find module 'lru-cache'`

说明依赖没有完整安装或仍在使用旧版本。更新仓库后重新安装：

```bash
cd /root/Hydro-XCPCIO-ScoreBoard-Batter
git pull --ff-only
npm ci
pm2 restart hydrooj
```

### `Cannot find module '@hydrooj/register'`

说明仍在使用旧安装脚本。当前版本的 `postinstall` 已改为直接执行 `node install.js`，不再依赖 `@hydrooj/register`。执行上面的更新流程即可。

### `public/assets/board.html` 不存在

重新执行资源生成脚本：

```bash
cd /root/Hydro-XCPCIO-ScoreBoard-Batter
npm run postinstall
```

### `Addon load fail`

查看 `Addon load fail` 后面的首个 `Error` 或 `Cannot find module`：

```bash
pm2 logs hydrooj --lines 200 --nostream
```

## 本地开发

```bash
npm install
```

安装过程会从 `@xcpcio/board-app` 复制榜单前端资源到 `public/`，无需依赖 Hydro 源码仓库中的 `@hydrooj/register`。

## 致谢与上游来源

本项目衷心致敬并感谢以下开源项目及其贡献者：

- [xcpcio/xcpcio](https://github.com/xcpcio/xcpcio)：提供优秀的 ICPC/CCPC 榜单可视化引擎与数据格式。本项目使用的 `@xcpcio/board-app` 及相关 XCPCIO 组件遵循其 MIT License。
- [hydro-dev/Hydro](https://github.com/hydro-dev/Hydro)：提供 Hydro Online Judge、插件 API，以及本项目所基于的 `@hydrooj/scoreboard-xcpcio` 原始实现。

感谢 Hydro 与 XCPCIO 社区为程序设计竞赛基础设施所做的持续投入。

## 许可证

本仓库中基于 Hydro 修改的代码依据 [GNU Affero General Public License v3.0 or later](LICENSE) 发布，并保留 Hydro 上游的版权与来源说明。XCPCIO 依赖及其代码仍分别遵循 XCPCIO 上游声明的 MIT License。
