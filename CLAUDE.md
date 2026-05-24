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

## 功能特性

### 预览功能

点击文件旁边的"预览"按钮，在弹出的模态框中查看 HTML 页面内容：
- 模态框尺寸：90% 宽度，80vh 高度
- 点击背景、关闭按钮（×）或按 ESC 键关闭
- 适合快速浏览归档内容而无需打开新标签页

### 编辑功能

点击"编辑"按钮可以重命名文件：
- 弹出输入框，输入新的文件名
- 文件名必须以 `.html` 结尾
- 使用 Git Tree API 在单个 commit 中完成重命名（原子操作）
- 重命名后立即更新页面显示

**技术细节**：编辑功能使用 Git Tree API 而非简单的创建+删除，避免触发两次 GitHub Actions 导致中间状态出现重复文件。

### 删除功能

点击"删除"按钮可以删除不需要的归档文件：
- 确认后通过 GitHub API 从仓库中删除文件
- 删除成功后立即从页面列表中移除
- 操作不可撤销，请谨慎使用

## 配置 GitHub Token

编辑和删除功能需要 GitHub Personal Access Token：

1. 访问 [GitHub Token 创建页面](https://github.com/settings/tokens/new?scopes=repo&description=Web-Archives-Manager)
2. 确保勾选 `repo` 权限
3. 生成 Token 并复制
4. 在 index.html 页面顶部的配置区域粘贴 Token 并点击"保存 Token"
5. Token 会保存在浏览器的 localStorage 中，下次访问时自动加载

**安全提示**：Token 仅保存在本地浏览器中，不会上传到服务器。但请妥善保管，不要分享给他人。

## 部署

这个仓库可以通过 GitHub Pages 部署为静态网站：

1. 进入仓库的 Settings → Pages
2. 选择 Source: Deploy from a branch
3. 选择 Branch: main, 目录: / (root)
4. 保存后即可通过 `https://[username].github.io/Web-Archives/` 访问
