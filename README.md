# dsh「自主模式」(autonomous) agent 预设

从本机 dsh 环境取出、单独存放备用的预设，2026-09-11 整理（重刷系统前）。**内容不含任何密钥/凭据。**

## 目录

| 路径 | 来源 | 说明 |
|---|---|---|
| `presets/autonomous/agent.cordis.yml` | `deepseek-harness/packages/preset/agent-presets/presets/autonomous/` | 自主模式预设本体（27K，cordis 树：工具域、persona、约束规则等） |
| `presets/autonomous/preset.yml` | 同上 | 元信息：`name: 自主模式`、`order: 5` |
| `patches/0001..0006-*.patch` | 本地 6 个提交（`git format-patch origin/master..HEAD -- …autonomous`） | **上游没有的本地改动**，可用 `git am` 逐个应用 |
| `local-presets/gray-mode/` | `~/.dsh/.agent-presets/gray-mode/` | 「灰度模式」预设——**只存在于本机 `~/.dsh`，不在任何仓库里**，刷机即丢，故一并留档 |

## 本地 6 个提交（未在上游）

1. `feat(preset)`: restore autonomous preset on alpha.1 with pangu gate
2. `preset`: enhance autonomous persona with evidence-first analysis and agent-teams guidance
3. `preset`: replace dsh-agent-teams with dsh-teams-x in autonomous preset
4. `fix(presets)`: remove dsh-teams-x row from autonomous preset（profile bundles 已提供）
5. `feat(presets)`: autonomous agent +3 条约束规则
6. `fix(presets)`: autonomous persona config text -> prefix

## 怎么装回去

```bash
# 方式 A：直接放回文件
cp -r presets/autonomous <harness>/packages/preset/agent-presets/presets/

# 方式 B：把 6 个改动作为提交应用（想保留演进历史时）
cd <harness> && git am /path/to/patches/*.patch

# 「灰度模式」只在本机生效的那份
mkdir -p ~/.dsh/.agent-presets/gray-mode
cp local-presets/gray-mode/* ~/.dsh/.agent-presets/gray-mode/
chmod 600 ~/.dsh/.agent-presets/gray-mode/*
```

## 让它成为默认

`~/.dsh/settings.yaml`：

```yaml
agent-presets:
  default: autonomous
```

改完重启 `dsh-desktop-x` 生效。

## 环境

- dsh 版本：0.1.5-rc.1（合并 upstream rc.1 后的本地状态，上游 `deepseek-ai/deepseek-harness`）
- 完整复原步骤见 `Mlte0907/dsh-desktop-x` 仓库的 `RESTORE.md`
