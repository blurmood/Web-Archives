# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 仓库概述

这是一个网页归档仓库，用于保存和管理网页快照的 HTML 文件。仓库通过 GitHub Actions 自动维护一个索引页面，列出所有已归档的网页。

## 核心架构

### 自动化工作流

- **触发条件**：每次推送到 main 分支时自动运行
- **工作流文件**：`update-index.yml`（注意：这个文件应该放在 `.github/workflows/` 目录下才能被 GitHub Actions 识别）
- **自动化任务**：
  1. 扫描仓库中所有 `.html` 文件（排除 `index.html`）
  2. 生成新的 `index.html` 文件，包含所有归档文件的链接
  3. 自动提交并推送更新

### 文件结构

```
.
├── index.html                    # 自动生成的索引页面
├── update-index.yml              # GitHub Actions 工作流配置
└── [归档的网页].html             # 保存的网页快照
```

## 常用操作

### 添加新的网页归档

1. 将保存的 HTML 文件放到仓库根目录
2. 提交并推送到 main 分支
3. GitHub Actions 会自动更新 `index.html`

### 文件命名约定

归档文件使用格式：`[标题] - [来源] ([日期时间]).html`

示例：
- `通知 - 小红书 (2026_5_24 16：48：35).html`
- `研究发现：吃隔夜饭，反而更健康？ - 今日头条 (2026_5_24 16：52：59).html`

### 手动更新索引

如果需要手动重新生成 `index.html`：

```bash
echo '<!DOCTYPE html><html lang="zh"><head><meta charset="UTF-8">
<title>文章列表</title>
<style>body{font-family:sans-serif;max-width:700px;margin:40px auto;padding:0 20px}
a{color:#0969da;font-size:1.1em;text-decoration:none}
a:hover{text-decoration:underline}li{margin:12px 0}</style>
</head><body><h1>📄 文章列表</h1><ul>' > index.html

for f in *.html; do
  [ "$f" = "index.html" ] && continue
  echo "<li><a href=\"$f\">$f</a></li>" >> index.html
done

echo '</ul></body></html>' >> index.html
```

## 重要注意事项

### GitHub Actions 配置问题

当前 `update-index.yml` 文件位于根目录，但 GitHub Actions 要求工作流文件必须放在 `.github/workflows/` 目录下。要修复此问题：

```bash
mkdir -p .github/workflows
mv update-index.yml .github/workflows/update-index.yml
git add .github/workflows/update-index.yml
git rm update-index.yml
git commit -m "fix: move workflow to correct directory"
git push
```

### 不要手动编辑 index.html

`index.html` 是自动生成的文件，手动修改会在下次推送时被覆盖。如需自定义索引页面样式或结构，应修改 `update-index.yml` 中的生成逻辑。

## 部署

这个仓库可以通过 GitHub Pages 部署为静态网站：

1. 进入仓库的 Settings → Pages
2. 选择 Source: Deploy from a branch
3. 选择 Branch: main, 目录: / (root)
4. 保存后即可通过 `https://[username].github.io/Web-Archives/` 访问
