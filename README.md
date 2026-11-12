# 个人主页维护说明

这是使用 jemdoc 生成的单页主页，发布在 [GitHub Pages](https://duanwenhong.github.io/whduan_sdu.github.io/index.html)。

## 文件用途

| 文件 | 用途 |
| --- | --- |
| `index.jemdoc` | 姓名、简介、教育经历、奖项和论文的源文件 |
| `jemdoc.py` | 使用 Python 3 生成网页 |
| `index.html` | 生成的网页，随源文件一起提交 |
| `jemdoc.css` | 字体、颜色、间距和布局 |
| `photos/profile.jpg` | 个人照片 |

## 修改文本

用 VS Code 等编辑器打开 `index.jemdoc`，以 UTF-8 编码保存。

- 第一行的 `title{Wenhong Duan}` 控制浏览器标签页标题，第二行的 `= Wenhong Duan` 控制网页姓名。
- 简介和邮箱直接修改文字，空行分隔段落，中文可以直接输入。
- 教育经历以 `- ` 开头，奖项和论文以 `. ` 开头，网页会自动编号。
- `*文字*` 表示加粗，`\n` 表示网页中的换行，`[网址 显示文字]` 表示链接。
- 修改模块标题时，保留其中的 `id="education"`、`id="awards"`、`id="publications"`，以维持定位链接。

新增论文时，可以复制现有条目后替换内容。

```text
. Paper title\n
  First Author, *W Duan*, Other Authors\n
  Journal or Conference, Year.
```

Services 已用 `#` 注释隐藏。恢复时，去掉该模块每行开头的注释标记并重新生成网页。照片和统计脚本周围的 `~~~`、`{}{raw}` 及 HTML 标签用于布局，编辑正文时保留。

## 生成与预览 HTML

安装 Python 3，确认 `python --version` 能正常运行。在仓库目录执行以下命令。

```powershell
python jemdoc.py index.jemdoc
```

命令会覆盖 `index.html`。以后从 `index.jemdoc` 修改正文再生成，确保源文件和网页同步。用浏览器打开本地 `index.html` 查看效果，已打开的页面可按 `Ctrl + F5` 刷新。

## 提交与部署

在 `main` 分支完成修改、生成和本地预览后，提交并推送。

```powershell
git status
git diff --check
git add index.jemdoc index.html
git commit -m "Update homepage content"
git push origin main
```

修改了样式、照片或 README 时，将对应文件一并加入 `git add`。首次提交前，需在 Git 中配置自己的 `user.name` 和 `user.email`。

本机已验证可通过 Windows OpenSSH 和以下密钥访问 GitHub。若默认 SSH 验证失败，在当前 PowerShell 窗口设置后重试推送。

```powershell
$env:GIT_SSH_COMMAND = 'C:/Windows/System32/OpenSSH/ssh.exe -i C:/Users/Wilson/.ssh/id_ed25519 -o IdentitiesOnly=yes'
git push origin main
```

推送 `main` 后，GitHub Pages 会自动启动部署。

1. 打开仓库的 [Actions 页面](https://github.com/duanwenhong/whduan_sdu.github.io/actions)。
2. 找到本次提交对应的 `pages build and deployment`，等待显示成功。
3. 打开 [公开主页](https://duanwenhong.github.io/whduan_sdu.github.io/index.html)，刷新并核对修改。
4. 如果部署失败，打开失败任务查看日志，修复后提交推送，或在 Actions 中重新运行失败任务。

## 同步 2026 标签

`2025` 标签保存原版主页，`2026` 标签记录当前维护版本。部署由推送 `main` 触发。需要同时更新 `2026` 时，在提交完成后执行以下命令。

```powershell
$previous2026 = git rev-parse refs/tags/2026
git tag -f -a 2026 HEAD -m "Update 2026 homepage"
git push --atomic "--force-with-lease=refs/tags/2026:$previous2026" origin main refs/tags/2026
```

此操作移动已有的 `2026` 标签。如果远程标签已被其他人更新，推送会被拒绝，需要先核对远程版本。
