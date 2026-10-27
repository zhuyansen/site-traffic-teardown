<div align="center">

# site-traffic-teardown

**拆任何一个站的竞品：对手是谁、流量从哪来、该抄什么。每个域名不到 $1。**

[![License: MIT](https://img.shields.io/badge/license-MIT-eab308)](LICENSE)
[![Stars](https://img.shields.io/github/stars/zhuyansen/site-traffic-teardown?logo=github&color=0f172a)](https://github.com/zhuyansen/site-traffic-teardown/stargazers)

[安装](#安装) · [做什么](#做什么) · [实测案例](#实测案例786-拆完-14-个-agent-skills-站) · [成本](#成本) · [手动运行](#手动运行) · [English](README.md)

</div>

一个给 Claude Code、Codex、Cursor 等 agent 用的 Skill。给它一个域名，它会自己找竞品、按流量筛出真对手、拉一张对比表，最后给出 3 个能直接动手的机会点。

数据来自 Similarweb（访问量、趋势、渠道来源）、Semrush（自然搜索竞品、关键词、外链）和 Ahrefs（DR），通过一个 [AIsa](https://go.aisa.one/yansen-zhu)（返佣链接）key 按次付费，不用开 Ahrefs 或 Semrush 的月费账号。

## 安装

```bash
npx skills add zhuyansen/site-traffic-teardown
```

Claude Code 里也可以当插件装：

```
/plugin marketplace add zhuyansen/site-traffic-teardown
/plugin install site-traffic-teardown@site-traffic-teardown
```

或者把 `skills/site-traffic-teardown` 复制到 `~/.claude/skills/`。

**需要：** Python 3.8+，以及登录好、有余额的 AIsa 命令行：

```bash
npm i -g @aisa-one/cli@0.6.9
```

```bash
aisa login
```

## 做什么

对 agent 说"拆解 example.com 的竞品"或"这个站流量从哪来"，它会分 4 步跑。每一步都先报价（免费），给你看价格，你说可以之后才跑扣费的调用。

| 步骤 | 拉什么 | 成本 |
|---|---|---|
| `discover` 找竞品 | Semrush 自然搜索竞品、概况，加你自己站 3 个月访问量 | 约 $0.31 |
| `screen` 筛选 | 每个候选拉 1 个月访问量，挑出 3–5 个真对手 | 每个域名 $0.075 |
| `full` 完整拆解 | 渠道来源、3 个月趋势、Semrush 关键词数和外链、Ahrefs DR | 每个域名约 $0.84 |
| `keywords` 关键词 | Semrush 前 20 个词和落地页，看对手靠哪类页面吃流量 | 每个域名 $0.02 |

最后输出：

- 对比表；
- 每个站属于哪种增长路子：靠品牌、靠长尾 SEO、靠社媒冲量、靠渠道分发；
- 给你的站 3 个机会点，每条都对应表里的一个数字；
- 适用的数据口径提醒；
- 预估和实扣的费用。

## 实测案例：$7.86 拆完 14 个 Agent Skills 站

2026-10-27 为 [agentskillshub.top](https://agentskillshub.top) 跑的。访问量来自 Similarweb，2026 年 7–9 月，含子域名。

| 站 | 9 月访问 | 7→9 月 | 主要来源 | DR |
|---|---|---|---|---|
| skills.sh | 213 万 | -19% | 直接访问 76% | 82 |
| skillsmp.com | 49 万 | -30% | 直接访问 62% | 66 |
| clawhub.ai | 31 万 | -53% | 直接访问 71% | 83 |
| claudemarketplaces.com | 16 万 | -77% | 搜索 54% | 38 |
| openagentskill.com | 6.8 万 | +143% | 搜索 61% | 6 |
| skillry.dev | 2.9 万 | 8 月上线 | 社媒 41% | 14 |
| nanoskill.ai | 2.8 万 | +186% | 搜索 76% | 12 |
| agentskillshub.top | 2.1 万 | 持平 | 搜索 42%、社媒 28% | 12 |

这次拆出来的东西：

- **大站全在跌，涨的是三个小站。** 每个都有一个窄切口：长尾页面、单一垂直领域，或者创始人自己的粉丝。
- **DR 不决定 SEO。** openagentskill.com 的 DR 只有 6，两个月涨了 2.4 倍。它前 20 个词里 16 个落在单个 skill 页面，接住的是搜 GitHub 项目名的人。
- **同一个站，两家工具差 90 倍。** Similarweb 估 agentskillshub.top 每月约 9,000 次搜索访问（全球、所有搜索引擎），Semrush 美国库估它自然搜索流量 99。两个都报，并说清楚引用的是哪个口径。
- **一个默认参数藏掉 70% 流量。** Similarweb 的 `main_domain_only` 默认是 true，会丢掉子域名。某个站因此从 99,450 变成 29,580。这个 skill 永远传 `false`。

## 成本

2026-10-27 实扣价格，每次调用的预估和实扣一致。

| 调用 | 价格 |
|---|---|
| Similarweb 访问量 | 每个域名每月 $0.075 |
| Similarweb 渠道来源 | 每个域名每月 $0.525 |
| Semrush 自然搜索竞品 | $0.08 |
| Semrush 自然搜索关键词（前约 20 行） | $0.02 |
| Semrush 外链概况 | $0.004 |
| Semrush 域名概况 | $0.001 |
| Ahrefs DR | $0.08 |
| Similarweb 地域分布（默认不跑） | $2.25 |

单次调用预估超过 `--max-call`（$2）或整批超过 `--max-total`（$5），脚本拒绝执行。要跑地域分布或大批量，自己调高这两个值。

## 手动运行

```bash
python3 skills/site-traffic-teardown/scripts/teardown.py discover --target example.com
```

这一步只报价。加 `--run` 才真正调用：

```bash
python3 skills/site-traffic-teardown/scripts/teardown.py full --target example.com --competitors a.com,b.com,c.com --month 2026-09 --lang zh --run
```

参数：`--lang en|zh` 选汇总语言，`--db us` 选 Semrush 数据库，`--out DIR` 选结果目录（默认 `teardown-out/`）。每次运行都会存下调用清单、原始结果和 markdown 汇总。

## 数据口径

- Similarweb 是模型估算。月访问 5 万以下的站可能差好几倍，有自己后台数据的话对照着看。
- Similarweb 只能查整个域名，`lobehub.com/skills` 这种栏目拆不出来。这时 skill 改用 Semrush 查栏目 URL，并注明只代表那一页。
- `keywords` 每个域名只返回前约 20 行，够看出哪类页面在赢，不是完整词表。
- 装在应用或命令行里的产品没有网页流量可量，改看 GitHub 星标或安装量，并标注清楚。

## 隐私

脚本只调用你本机的 AIsa 命令行，不打印也不写入你的 API key，结果只存在你本地的 `--out` 目录。

## 许可

[MIT](LICENSE)
