# Stash · Nagram 分流规则集

Nagram（第三方 Telegram 客户端，`xyz.nextalone.nagram`）专用的 **Stash 远程规则集**。
域名池 + 官方数据中心 IP + 进程级匹配，自包含单文件，无外部依赖。

> 数据来源：`core.telegram.org/resources/cidr.txt`（官方 DC IP）、
> LM-Firefly / dler-io Telegram 规则集（域名）、Nagram 仓库 `NaConfig.kt`（专属域名）。

## 文件

| 文件 | behavior | 用途 |
|---|---|---|
| `stash/nagram.yaml` | `classical` | 单文件全量（域名+进程+IP），**推荐** |
| `stash/nagram-cidr.yaml` | `ipcidr` | 仅 IP 段，想"域名走本地规则、IP 走远程"时用 |

## 用法一：单文件全量（推荐）

在 Stash 配置文件（或覆写文件）中：

```yaml
rule-providers:
  nagram:
    type: http
    behavior: classical
    url: "https://raw.githubusercontent.com/xxesc/stash-nagram-rules/main/stash/nagram.yaml"
    interval: 86400

rules:
  - RULE-SET, nagram, 🚀 你的代理组名
  - MATCH, DIRECT
```

- `🚀 你的代理组名` 替换为你 Stash 里实际存在的代理组（组名写错整份配置会被 Stash 拒绝）。
- 想让未匹配流量也走代理：把 `MATCH, DIRECT` 改成 `MATCH, 🚀 你的代理组名`。

## 用法二：IP 单独远程，域名自带

```yaml
rule-providers:
  nagram-cidr:
    type: http
    behavior: ipcidr
    url: "https://raw.githubusercontent.com/xxesc/stash-nagram-rules/main/stash/nagram-cidr.yaml"
    interval: 86400

rules:
  - DOMAIN-SUFFIX, t.me, 🚀 你的代理组名
  - DOMAIN-SUFFIX, telegram.org, 🚀 你的代理组名
  # ... 其余域名规则自行维护
  - RULE-SET, nagram-cidr, 🚀 你的代理组名
  - MATCH, DIRECT
```

## 说明

- `PROCESS-NAME` 规则只在 Stash 与客户端同机时生效；Nagram 在别的设备上跑，靠域名/IP 规则兜底，效果一致。
- 规则集每 24h 自动刷新（`interval: 86400`）。
- Telegram 换 DC IP 段时，更新本仓库 `stash/nagram.yaml` / `stash/nagram-cidr.yaml` 并提交即可，客户端侧无需改配置。
