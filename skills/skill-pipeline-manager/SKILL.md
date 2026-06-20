---
name: skill-pipeline-manager
description: 从 SkillHub 搜索 Skill → 安装到本地仓库 → 推送到 GitHub → 安装到 Codex
metadata:
  short-description: SkillHub 搜索 + 全流程安装/删除
---

# Skill Pipeline Manager

## 触发场景

用户想要管理 Skill 时触发：
- "帮我搜索 xxx 的 skill"
- "帮我安装 xxx 的 skill"
- "帮我卸载/删除 xxx 的 skill"
- "记录 xxx 的 skill"

## 安装流程

### 第1步：搜索 SkillHub

用 `skillhub search <关键词>` 搜索并展示结果（名称/描述/版本）。

### 第2步：确认选择

列出 1-3 个候选 Skill，让用户确认后再继续。

### 第3步：安装到本地仓库

```powershell
# 3a. 从 SkillHub 下载到本地仓库
& "$env:USERPROFILE\.skillhub\skillhub.cmd" install <slug> --dir D:\Skills\my-codex-skills

# 3b. 移动到 skills/ 目录并创建 .index
Move-Item D:\Skills\my-codex-skills\<slug> D:\Skills\my-codex-skills\skills\<slug> -Force
New-Item -ItemType Directory -Path "D:\Skills\my-codex-skills\skills\.index\<slug>" -Force
New-Item -ItemType File -Path "D:\Skills\my-codex-skills\skills\.index\<slug>\.gitkeep" -Force
```

### 第4步：推送到 GitHub

```powershell
cd D:\Skills\my-codex-skills
git add -A
git commit -m "add: <slug> skill"
git push
```

### 第5步：从 GitHub 安装到 Codex

```powershell
python "$env:USERPROFILE\.codex\skills\.system\skill-installer\scripts\install-skill-from-github.py" --repo xtj664/my-codex-skills --path skills/<slug>
```

## 删除流程

当用户要求卸载/删除某个 Skill 时，必须三步同步删除。

### 第1步：从 Codex 删除

```powershell
Remove-Item -Path "$env:USERPROFILE\.codex\skills\<slug>" -Recurse -Force
```

### 第2步：从本地仓库删除并推送 GitHub

```powershell
cd D:\Skills\my-codex-skills
Remove-Item -Path "skills\<slug>" -Recurse -Force
Remove-Item -Path "skills\.index\<slug>" -Recurse -Force
git add -A
git commit -m "remove: <slug> skill"
git push
```

### 第3步：验证

确认以下位置均已删除：
- Codex：C:\Users\谢泰俊\.codex\skills\<slug>
- 本地仓库：D:\Skills\my-codex-skills\skills\<slug> 和 skills\.index\<slug>
- 远程仓库：已通过 git push 同步删除

## 目录与路径

- 本地仓库：D:\Skills\my-codex-skills
- GitHub 仓库：xtj664/my-codex-skills
- SkillHub CLI：$env:USERPROFILE\.skillhub\skillhub.cmd
- Codex Skill 安装脚本：$env:USERPROFILE\.codex\skills\.system\skill-installer\scripts\install-skill-from-github.py
- Codex 技能目录：$env:USERPROFILE\.codex\skills
