# dsh-autonomous-preset

DSH agent 预设「自主模式」(autonomous) 的备份与安装源,适配 **dsh 0.1.7-alpha.2** 的
bundle 机制。

> 历史:本仓库在 0.1.5-rc.1 时代以「目录直接放进 harness」的方式工作(`presets/` +
> `patches/`)。0.1.7-alpha.2 起 registry(`dsh-agent-presets`)不再扫描预设目录,
> 新增/覆盖预设只能通过 bundle patch 插入一行 `@deepseek-ai/dsh-agent-preset` ——
> 本仓库已按新机制改造,旧结构原样留档。

## 安装(dsh 0.1.7-alpha.2)

```bash
pnpm dsh plugin --profile web add github:Mlte0907/dsh-autonomous-preset
dsh-restart
```

装完在 Web 设置「默认模式」里把它设为默认(旧 settings.yaml 的 `agent-presets.default`
机制已废除),或在会话的模式选择器里单次切换(选择器默认开启)。

> 2026-09-23 起,本机 dsh 已把该预设作为**内置预设**挂载(harness web-app bundle 的
> `presets/autonomous.patch.yml`);本仓库安装法面向其他机器与重装恢复。
> 「内部模式」预设已删除,其回退理念并入自主模式第 5 条(文件见历史提交 a663ef6)。

## 相对 0.1.5 版的调整(2026-09,逐条对着 0.1.7 源码取证)

1. **清除 teamsx 死引用**:`dsh-teams-x` / `teamsx_*` 工具在 0.1.7 全仓不存在(grep 零命中)。
   persona 的 TeamsX 段改写为面向 profile 层 agent-team 工具集(`spawn_teammate`、
   `send_message`、`list_agents`、`wait_agent`、`interrupt_agent`、`team_task_*`),
   协议细则交给上游自带的 Team policy prompt section,工具缺席时整段静默失效;
   header 注释同步改写。
2. **pangu 行退役**:profile 层 `dsh-pangu` bundle 已挂 `mcp-pangu`(唯一配置源
   `~/.pangu/config.json`)。预设内再挂 Pangu MCP 行 = 双配置源可漂移陷阱(旧行按
   `PANGU_API_KEY` 环境变量 opt-in,与现配置源不同),删除;persona 的长期记忆门保持,
   按 `mcp__pangu__*` 是否可用降级。
3. **恢复 plan-mode**:0.1.5-alpha.1 时代因 `@deepseek-ai/dsh-plan-mode` 包不存在而裁掉;
   0.1.7 已有该包(standard 同款),恢复 planning group(与 standard 的块逐字节同源),
   头部注释与实际行集重新一致。
4. **删除外部产品对齐表述**:header、description、tool-todo 注释中「仿照某外部 Agent 产品模式」的表述全部移除(历史 `patches/0001` 为不可变 git am 补丁,内文保留)。
5. **注明回退机制**:第三方工具缺席时回退本预设自带能力——Team 工具集缺席 → subagent 委派(explore/judge/subagent + send_message/list_agents/interrupt_agent);Pangu MCP 缺席 → **明示用户**后用 todo/goal/skill 行维持状态,不静默跳过。
6. **端到端 UI 验收与重启入 persona**:按历史记忆固化 Playwright 挂 `/usr/bin/chromium`(`~/.chromium-browser-snapshots` 那份是坏的,勿用)的真浏览器验收法,以及 `dsh-restart` 重启方式(只有其输出里的新 token 有效)。

行集本身零改动:全部 row config 键(`prefix`/`maxBytes`/`sampleOverCapGlobResults`/
`provider`/`backgroundMode`/`persona`/`toolFilter`/isolate)逐个对过 0.1.7 Config schema,
无死键。

## 仓库结构

| 路径 | 状态 | 说明 |
| --- | --- | --- |
| `cordis.patch.yml` | **现行** | bundle patch,插入 `preset-autonomous` 行(order 5) |
| `package.json` | **现行** | bundle manifest(`dsh.bundle.patch`) |
| `presets/autonomous/` | 0.1.5 留档 | 旧格式(`preset.yml` + `agent.cordis.yml`),0.1.7 不可安装 |
| `patches/0001..0006` | 历史 | 对旧 harness 树的 git am 补丁,上游结构已变,仅存档 |
| `local-presets/gray-mode/` | 留档 | 未启用;`text:` 键自 0.1.3-alpha.2 起非法,复活需改 `prefix` |
