---
name: skill-pipeline-manager
description: 用户提出需求时，搜索 SkillHub 找到最适合的 Skill，走标准化流水线安装到本地仓库并推送到 Codex
metadata:
  short-description: SkillHub 搜索 + 标准化安装流水线
---

# Skill Pipeline Manager

## 触发条件

用户提出类似以下需求时触发此 Skill：
- "我需要一个能 xxx 的 skill"
- "有没有可以 xxx 的 skill"
- "帮我找一个能做 xxx 的 skill"
- "装一个 xxx 的 skill"

## 流水线步骤

### 第一步：搜索 SkillHub

用 `skillhub search <关键词>` 搜索，关键词根据用户需求提炼（中文/英文/组合）。

### 第二步：推荐最佳匹配

从搜索结果中筛选最对口的 1-3 个 Skill，简要说明各自的适用场景，推荐给用户确认。

### 第三步：安装到本地仓库

用户确认后，对每个 Skill：

```powershell
# 3a. 从 SkillHub 下载到仓库根目录
& "$env:USERPROFILE\.skillhub\skillhub.cmd" install <slug> --dir D:\Skills\my-codex-skills

# 3b. 移入 skills/ 目录并更新 .index
Move-Item <slug> skills/<slug> -Force
New-Item -ItemType Directory -Path "skills\.index\<slug>" -Force
New-Item -ItemType File -Path "skills\.index\<slug>\.gitkeep" -Force
```

### 第四步：提交并推送到 GitHub

```powershell
cd D:\Skills\my-codex-skills
git add -A
git commit -m "add: <slug> skill"
git push
```

### 第五步：从自己仓库安装到 Codex

```powershell
python "$env:USERPROFILE\.codex\skills\.system\skill-installer\scripts\install-skill-from-github.py" --repo xtj664/my-codex-skills --path skills/<slug>
```

### 第六步：收尾

- 告知用户安装完成，需要重启 Codex 生效
- 汇总当前仓库 Skill 列表
- 询问是否还有其它需求

## 基础设施信息

- 本地仓库路径: `D:\Skills\my-codex-skills`
- GitHub 仓库: `xtj664/my-codex-skills`
- SkillHub CLI: `$env:USERPROFILE\.skillhub\skillhub.cmd`
- Codex Skill 安装器: `$env:USERPROFILE\.codex\skills\.system\skill-installer\scripts\install-skill-from-github.py`
