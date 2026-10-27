<div align="center">

# site-traffic-teardown

**Tear down any site's competitors: who they are, where their traffic comes from, and what to copy. Under $1 a domain.**

[![License: MIT](https://img.shields.io/badge/license-MIT-eab308)](LICENSE)
[![Stars](https://img.shields.io/github/stars/zhuyansen/site-traffic-teardown?logo=github&color=0f172a)](https://github.com/zhuyansen/site-traffic-teardown/stargazers)
[![Last commit](https://img.shields.io/github/last-commit/zhuyansen/site-traffic-teardown?color=64748b)](https://github.com/zhuyansen/site-traffic-teardown/commits/main)

[Install](#install) · [What it does](#what-it-does) · [A real run](#a-real-run-14-agent-skills-sites-for-786) · [Cost](#cost) · [Run it by hand](#run-it-by-hand) · [中文说明](README.zh-CN.md)

</div>

An Agent Skill for Claude Code, Codex, Cursor and other agents. Give it a domain. It finds the competitors, screens them by traffic, pulls one side-by-side table and reads it into three things you can act on.

The data is Similarweb (traffic, trend, channel mix), Semrush (organic competitors, keywords, backlinks) and Ahrefs (domain rating), paid per call through one [AIsa](https://go.aisa.one/yansen-zhu) key (referral link), with no monthly Ahrefs or Semrush seat.

## Install

```bash
npx skills add zhuyansen/site-traffic-teardown
```

In Claude Code you can also add it as a plugin:

```
/plugin marketplace add zhuyansen/site-traffic-teardown
/plugin install site-traffic-teardown@site-traffic-teardown
```

Or copy `skills/site-traffic-teardown` into `~/.claude/skills/`.

**Needs:** Python 3.8+, and the AIsa CLI signed in with some credit:

```bash
npm i -g @aisa-one/cli@0.6.9
```

```bash
aisa login
```

## What it does

Ask your agent "tear down the competitors of example.com" or "where does example.com get its traffic". It runs four stages. Each one quotes first (free), shows you the price, and runs the billed calls only after you say yes.

| Stage | What it pulls | Cost |
|---|---|---|
| `discover` | Semrush organic competitors, overview and 3-month visits for your site | ~$0.31 |
| `screen` | One month of visits per candidate, to pick 3–5 real rivals | $0.075 per domain |
| `full` | Channel mix, 3-month trend, Semrush keywords and backlinks, Ahrefs DR | ~$0.84 per domain |
| `keywords` | Top Semrush keywords with landing pages, to see which page type earns traffic | $0.02 per domain |

Then it writes:

- the comparison table,
- each site's growth-engine type: brand/habit, SEO long tail, social burst or distribution,
- three opportunities for your site, each tied to a number in the table,
- the data caveats that apply,
- quoted versus charged cost.

## A real run: 14 Agent Skills sites for $7.86

Run on 2026-10-27 for [agentskillshub.top](https://agentskillshub.top). Visits are Similarweb, July to September 2026, subdomains included.

| Site | Sept visits | Jul→Sep | Main source | DR |
|---|---|---|---|---|
| skills.sh | 2.13M | -19% | Direct 76% | 82 |
| skillsmp.com | 489k | -30% | Direct 62% | 66 |
| clawhub.ai | 307k | -53% | Direct 71% | 83 |
| claudemarketplaces.com | 156k | -77% | Search 54% | 38 |
| openagentskill.com | 68k | +143% | Search 61% | 6 |
| skillry.dev | 29k | launched in August | Social 41% | 14 |
| nanoskill.ai | 28k | +186% | Search 76% | 12 |
| agentskillshub.top | 21k | flat | Search 42%, Social 28% | 12 |

What the run turned up:

- **Every big site fell; three small ones grew.** The growers each had a narrow angle: long-tail pages, one vertical, or a founder's audience.
- **DR didn't decide SEO.** openagentskill.com has DR 6 and grew 2.4x in two months. 16 of its top 20 keywords land on single skill pages, catching people who search a repo's name.
- **Two tools, one site, 90x apart.** Similarweb put agentskillshub.top at about 9,000 search visits a month (worldwide, all engines). Semrush's US database put its organic traffic at 99. Report both and say which you quote.
- **One default parameter hides 70% of traffic.** Similarweb's `main_domain_only` defaults to true and drops subdomains. On one site that was 29,580 against 99,450. The skill always sends `false`.

## Cost

Prices charged on 2026-10-27; the quote matched the charge on every call.

| Call | Price |
|---|---|
| Similarweb visits | $0.075 per domain per month |
| Similarweb channel sources | $0.525 per domain per month |
| Semrush organic competitors | $0.08 |
| Semrush organic keywords (top ~20 rows) | $0.02 |
| Semrush backlinks overview | $0.004 |
| Semrush domain overview | $0.001 |
| Ahrefs domain rating | $0.08 |
| Similarweb top geographies (off by default) | $2.25 |

The script refuses to run when one call is quoted above `--max-call` ($2) or the batch above `--max-total` ($5). Raise them yourself if you want geographies or a big batch.

## Run it by hand

```bash
python3 skills/site-traffic-teardown/scripts/teardown.py discover --target example.com
```

That only quotes. Add `--run` to call:

```bash
python3 skills/site-traffic-teardown/scripts/teardown.py full --target example.com --competitors a.com,b.com,c.com --month 2026-09 --run
```

Options: `--lang en|zh` for the summary, `--db us` for the Semrush database, `--out DIR` for where results go (default `teardown-out/`). Each run saves the calls, the raw results and a markdown summary.

## Caveats

- Similarweb is modelled. Sites under about 50k visits a month can be off by multiples; check against your own analytics where you have them.
- Similarweb only measures whole domains. A section like `lobehub.com/skills` can't be split out; for that the skill reads Semrush for the section URL and says it covers that page only.
- The `keywords` stage returns the top ~20 rows per domain. That's enough to see the page type that wins, not a full keyword list.
- Products installed inside an app or CLI have no web traffic to measure. Use GitHub stars or install counts and label them.

## Privacy

The script calls only the AIsa CLI on your machine. It never prints or writes your API key. Results stay in `--out` on your disk.

## License

[MIT](LICENSE)
