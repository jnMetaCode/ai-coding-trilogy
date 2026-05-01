# 构建说明

> 这个仓库**刻意不放 markdown 源**——只放最终 PDF + 产品页。
> 写作和构建在 [ai-coding-guide](https://github.com/jnMetaCode/ai-coding-guide) 仓库的 `book/` 目录里完成，导出 PDF 后复制到这里、push 一次即可。

## 上游 markdown 源

mdbook 源代码：[ai-coding-guide/book/src/](https://github.com/jnMetaCode/ai-coding-guide/tree/main/book/src)

在线阅读版：[jnmetacode.github.io/ai-coding-guide](https://jnmetacode.github.io/ai-coding-guide/)

## 在本机生成 PDF

### 方案 A：mdbook + Chrome 打印（推荐，零配置）

最简单的路径：mdbook 已经生成"全书一页"的打印页，Chrome 打印到 PDF 即可。

```bash
# 1. 安装 mdbook（一次性）
brew install mdbook

# 2. 跑起来本地预览服务
cd /path/to/ai-coding-guide/book
mdbook serve --port 3030

# 3. 浏览器打开 http://127.0.0.1:3030/print.html
#    Cmd+P → 目标"另存为 PDF" → 保存为 AI-Coding-Trilogy-zh-v1.0.pdf

# 4. 复制到本仓库
cp ~/Downloads/AI-Coding-Trilogy-zh-v1.0.pdf /path/to/ai-coding-trilogy/
cd /path/to/ai-coding-trilogy
git add *.pdf
git commit -m "docs: ship v1.0 PDF (zh)"
git push
```

**优点**：Chrome 自带中文字体支持，零配置出 PDF
**缺点**：手动操作，分页/排版控制弱（但够用）

### 方案 B：pandoc + xelatex（适合精排版）

适合：你想要更专业的排版（自定义字体、章节分页、页眉页脚）。

```bash
# 1. 装 pandoc + tinytex（一次性）
brew install pandoc
brew install --cask mactex-no-gui   # 完整版 ~5GB；或 brew install basictex（~100MB）

# 2. 装中文字体（如果系统没有）
#    macOS 自带 PingFang SC，Linux 用 Noto Serif CJK SC
fc-list :lang=zh | head -5   # 看本机有什么中文字体

# 3. 跑导出脚本
cd /path/to/ai-coding-guide/book
pandoc src/intro.md src/v1-intro.md src/cheatsheet.md ... \
  -o AI-Coding-Trilogy-zh-v1.0.pdf \
  --pdf-engine=xelatex \
  -V mainfont="PingFang SC" \
  -V CJKmainfont="PingFang SC" \
  -V monofont="JetBrains Mono" \
  --toc --toc-depth=2 \
  --top-level-division=part \
  -V geometry:margin=2cm
```

**优点**：可定制排版、中文字体精控、可加页眉页脚
**缺点**：xelatex 装起来重，初次配置费时

### 方案 C：mdbook-pdf 插件（自动化）

```bash
cargo install mdbook-pdf

# 在 book.toml 加一段
# [output.pdf]

mdbook build   # 自动出 book/book/output.pdf
```

**优点**：可写入 CI 自动化
**缺点**：依赖 Chrome（需要 puppeteer 等待页面渲染），CI 配置稍复杂

## 推荐工作流（最实在的）

写作和迭代时用 **方案 A**（Chrome 打印），快、零成本，新版本随时出。

等到要做出版社级排版（纸书 / Leanpub 精装版）时，再切方案 B 重新做精排。

## 命名约定

```
AI-Coding-Trilogy-zh-v1.0.pdf      中文版 v1.0
AI-Coding-Trilogy-en-v1.0.pdf      英文版 v1.0
AI-Coding-Trilogy-zh-v1.1.pdf      内容修订
AI-Coding-Trilogy-zh-v2.0.pdf      重大重写
```

每个新版本：
1. 仓库根目录覆盖旧版（旧版进 git history 不丢）
2. 更新 README 中"下载链接 + 大小"那一行
3. 在 [CHANGELOG.md](./CHANGELOG.md)（如果有）写一句版本变化
4. push

## 字体备忘

如果出现"PDF 中文显示乱码方块"——最常见原因：

| 问题 | 修复 |
|------|------|
| 系统没装中文字体 | macOS 自带 PingFang，Linux 装 `fonts-noto-cjk` |
| 字体名拼错 | `fc-list :lang=zh` 看本机实际可用字体名 |
| pandoc 用了 `pdflatex` 而不是 `xelatex` | 加 `--pdf-engine=xelatex` |
| 字体配置但 LaTeX 找不到 | `fc-cache -fv` 刷字体缓存 |

## 封面 / 横幅图

- `cover.png`：500×700 左右的书脊封面（README 顶部 hero）
- `banner.png`：1200×400 横幅（社交媒体分享缩略图）

可以在 [Canva](https://www.canva.com)、[Figma](https://www.figma.com) 自己设计；
或者参考[花叔的封面风格](https://github.com/alchaincyf/hermes-agent-orange-book)（橙皮书系列），
一致的视觉锚点能强化"系列书"的辨识度。
