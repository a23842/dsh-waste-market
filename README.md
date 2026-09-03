# DSH Waste Market

A 股行情面板插件，为 [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness) Web 界面增加一个可拖拽的行情看盘窗口。

在 DSH Web 左侧边栏点击 📊 入口即可打开：

| 页签 | 功能 |
| --- | --- |
| 大盘 | 上证 / 深成 / 创业板 / 科创50 实时指数，30 秒自动刷新 |
| 行情 | 股票 / ETF / 转债 / LOF 搜索（代码 / 名称 / 拼音）+ 多池榜单（沪深A股 / ETF·场内基金 / 可转债 / LOF，按涨幅 / 成交额 / 换手 / 主力净流入排序）+ 板块榜 + 龙虎榜 |
| 自选 | 自选股实时行情（持久化于 `$DSH_HOME/.waste-market-watchlist.json`），支持分组、JSON 备份 / CSV 导出、JSON / CSV / 文本导入（合并或覆盖） |
| 选股 | 两种模式：**评分选股**（价格 / 涨跌幅 / 换手率筛选 + 13 项技术信号勾选 + 加权评分排序）和**多策略交叉选股**（7 个预设策略并行交集，需同时命中多个策略，按命中数排序） |
| 快讯 | 新浪 / 东财 / 金十 三源聚合 7×24 快讯，来源可筛选，重要资讯红色高亮，60 秒自动刷新 |

行情页顶部为**市场情绪温度计**：涨停 / 跌停 / 炸板数、全市场涨跌家数、连板梯队，可展开涨停池列表。

点击任意股票会弹出独立详情窗口：大字现价 + 涨跌、15 项实时指标、日 / 周 / 月 / 5 分 / 30 分 K 线（前复权，蜡烛图 + MA5/10/20 均线 + 成交量 + 十字光标）。窗口可拖动、可多开，ESC 或点击遮罩关闭。

## 数据来源

- 实时行情 / 指数 / K 线：腾讯财经（qt.gtimg.cn / ifzq.gtimg.cn，多主机自动回退）
- 榜单 / 板块 / 龙虎榜 / 资金流 / 选股：东方财富（push2.eastmoney.com，多镜像回退）
- 搜索：东方财富（searchadapter.eastmoney.com）
- 7×24 快讯：新浪财经直播流 + 东方财富快讯 + 金十数据

所有数据由插件服务端代理抓取并标准化，浏览器端同源调用 `/api/waste-market/*`，仅限本机 loopback 访问。数据仅供研究参考，不构成投资建议。

## 安装

```bash
dsh plugin --profile web add dsh-waste-market
```

或手动安装（开发模式）：

```bash
# 1. 把包链接进 web profile 的 node_modules（或 pnpm add file:...）
ln -s "<本包路径>" "$DSH_HOME/profiles/web/node_modules/dsh-waste-market"

# 2. 在 $DSH_HOME/profiles/web/cordis.patch.yml 追加：
# - insert:
#     - id: waste-market
#       name: 'dsh-waste-market'

# 3. profile 配置 watcher 会在 ~1s 内热重组合；浏览器刷新页面后侧边栏出现 📊 入口。
#    若修改了 lib/index.js（服务端代码），需要重启 DeepSeek Harness 进程生效。
```

## 结构

- `lib/index.js` — 服务端 cordis 插件：`/api/waste-market/*` 路由 + 自选股持久化 + 快讯聚合
- `lib/emrank.js` — 东方财富榜单 / 板块 / 龙虎榜 / 资金流数据
- `lib/fetch-utils.js` — 共享 fetch 工具（GBK 解码、多主机回退）
- `lib/screener.js` — 条件选股引擎
- `lib/client.js` — 浏览器端面板（React）
- `cordis.patch.yml` — 插件行注册（`dsh.bundle.patch`）

## License

MIT
