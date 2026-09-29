# dsh-autonomous-preset

DSH agent 预设「自主模式」(autonomous) 的**一份文件**。它不是插件:放进 dsh 的内置 Agent
预设列表,就会多出一个预设。目标版本 **dsh 0.2.0-rc.1**。

> 历史:本仓库在 0.1.5-rc.1 时代以「目录直接放进 harness」的方式工作(一份 `presets/` 目录
> + 一串 `patches/` 补丁)。0.1.7-alpha.2 起 registry(`dsh-agent-presets`)不再扫描预设目录,
> 新增预设只能靠 bundle patch 插入一行 `@deepseek-ai/dsh-agent-preset` ——
> 仓库已收敛为下面这一份文件,旧结构留在 git 历史里(`1d27f14` 及更早)。

## 放进内置预设列表(dsh 0.2.0-rc.1)

**三步,缺一不生效:**

1. 把 `cordis.patch.yml` 复制到 web-app bundle 的预设目录:

   ```
   packages/bundle/web-app/presets/autonomous.patch.yml
   ```

2. 在同一个 bundle 的 `packages/bundle/web-app/package.json` 里,把它的路径追加进
   `dsh.bundle.patch` 数组(数组顺序即应用顺序,放最后):

   ```json
   "./presets/autonomous.patch.yml"
   ```

3. 重建并重启:`pnpm run build`,然后 `dsh-restart`(只有它输出的新 token 有效)。

**为什么第 2 步不能省**:预设没有目录扫描发现机制。`packages/boot/app-boot/src/profile.ts:73`
的 `bundlePatchPaths()` 只返回 manifest `dsh.bundle.patch` 里列出的文件,再由
`packages/boot/plugin-manager/src/index.ts:548/634/741` 逐个 `loadOverlayPatches`。
只把文件丢进 `presets/` 而不加数组条目,预设不会出现。文件被加载后,那一行
`@deepseek-ai/dsh-agent-preset` 在激活时注册进 registry
(`packages/preset/agent-preset/src/index.ts:28`),`id: autonomous`、`order: 5`。

**不要既内置又装成 bundle**:`AgentPresets.register()` 遇到重复 id 直接抛
`Duplicate agent preset: autonomous`(`packages/preset/agent-preset-registry/src/index.ts:84`),
启动失败,不是静默覆盖。

完成后在 Web 设置「默认模式」里把它设为默认(旧 settings.yaml 的 `agent-presets.default`
机制已废除),或在会话的模式选择器里单次切换(选择器默认开启)。

> 2026-09-23 起,本机 dsh 已按上面的三步把它挂成**内置预设**;
> 本仓库是那份文件的备份与同步源。「内部模式」预设已删除,其回退理念并入自主模式第 5 条
> (文件见历史提交 a663ef6)。

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
4. **删除外部产品对齐表述**:header、description、tool-todo 注释中「仿照某外部 Agent 产品模式」的表述全部移除(当时的 `patches/0001` 为不可变 git am 补丁,内文保留;该补丁目录已随旧结构移出仓库,见 git 历史)。
5. **注明回退机制**:第三方工具缺席时回退本预设自带能力——Team 工具集缺席 → subagent 委派(explore/judge/subagent + send_message/list_agents/interrupt_agent);Pangu MCP 缺席 → **明示用户**后用 todo/goal/skill 行维持状态,不静默跳过。
6. **端到端 UI 验收与重启入 persona**:按历史记忆固化 Playwright 挂 `/usr/bin/chromium`(`~/.chromium-browser-snapshots` 那份是坏的,勿用)的真浏览器验收法,以及 `dsh-restart` 重启方式(只有其输出里的新 token 有效)。

行集本身零改动:全部 row config 键(`prefix`/`maxBytes`/`sampleOverCapGlobResults`/
`provider`/`backgroundMode`/`persona`/`toolFilter`/isolate)逐个对过 0.1.7 Config schema,
无死键。

### 0.2.0-rc.1 复核与对齐(2026-09-29)

以上 6 条与行集在 0.2.0-rc.1 上逐条复核,结论不变,行集零改动:

- `standard.patch.yml` 在 `dsh-v0.1.7-rc.1` 与 `dsh-v0.2.0-rc.1` 两个标签之间逐字节相同
  —— 本预设对齐的基线没动过。
- 本文件引用的 21 个 `@deepseek-ai/dsh-*` 包在 0.2.0-rc.1 树里全部可解析。
- `teams-x` / `teamsx` 在 0.2.0-rc.1 的 `packages/` 与 `apps/` 里仍然零命中,第 1 条继续成立。

仓库侧改动两处:本文的版本表述,以及**删除 `config` 里自带的 `name` / `description`**。
`isBuiltInPreset` 把「自带 name 的声明」判为自有文案,留着会把所有语言都钉死成中文;删掉后走
0.2.0-rc.1 新增的 `presetAutonomousName` / `presetAutonomousDescription` 字典键。代价:放到
0.1.7 机器上时不再有自带名称(该版本的字典键尚不存在)。工具行集与 persona 始终未动。

`cordis.patch.yml` 现与 harness 内置副本
`packages/bundle/web-app/presets/autonomous.patch.yml` **逐字节相同**,以后以本机那份为准同步:
内置副本随 harness 升级一起变,本仓库不再单独维护文件头部措辞(该头部现在描述的是内置注册
位置)。

## 仓库结构

| 路径 | 说明 |
| --- | --- |
| `cordis.patch.yml` | 全部内容:插入 `preset-autonomous` 行(`id: autonomous`、`order: 5`、22 行插件) |
| `README.md` | 本文件 |

仓库里没有别的东西:没有 `package.json`,不是 bundle 也不是插件。
0.1.5 时代的 `presets/` 旧格式、`patches/0001..0006` 与未启用的 `local-presets/gray-mode/`
已删除,需要时从 git 历史取(`1d27f14` 及更早)。
