# TRPG 规则网站工作流手册

## 工具与技术栈

| 工具            | 用途                      |
| --------------- | ------------------------- |
| MkDocs Material | 将 `.md` 文件生成静态网站 |
| GitHub          | 托管代码与内容            |
| GitHub Actions  | 推送后自动部署            |
| GitHub Pages    | 免费网站托管              |

---

## 一、从零搭建项目

### 1. 安装依赖(跳过)


### 2. 创建项目结构

在项目的位置，手动创建以下文件夹和文件：

```
parent_folder/
├── mkdocs.yml
├── docs/
│   ├── chapXX_title.md
│   ├── chapXX_title.md
│   ├── stylesheets/
│   │   └── parchment.css
│   └── javascripts/
│       └── extra.js
└── .github/
    └── workflows/
        └── deploy.yml
```


---

### 3. 创建 `mkdocs.yml`（网站配置）

在项目根目录创建 `mkdocs.yml`，内容应保持简洁，易于之后修改：

网站配置需要包含以下约定：

- 使用 MkDocs Material 的深色模式，并开启顶部大章节导航。
- 禁用 Material 默认在线字体，由本地样式文件统一指定中文字体。
- 顶部导航按大章节组织：首页、同人介绍、基本规则、修文习武、角色创建、江湖、装备道具、范例模组。
- `chap00` 归入“同人介绍”，“基本规则”下放置 `chap01` 相关页面。
- `chap02` 及之后若正文尚未整理完成，也应先创建极简占位页，保证顶部大章节可点击。
- 启用常用 Markdown 扩展，包括提示框、折叠详情、代码高亮、标签页与 Material 图标。

> **每次新增章节**，在 `nav:` 下添加一行即可，例如：
> ```yaml
>     - 名字与背景: chapXX_names.md
> ```

---

### 4. 创建 `parchment.css`（且符合主题）

创建 `docs/stylesheets/parchment.css` ，整体架构保持简洁，可参考mkdocs自己的网页和相应代码：https://squidfunk.github.io/mkdocs-material/getting-started/

整体配色应为暗色，参考太吾绘卷wiki的配色、字体风格：https://taiwu.huijiwiki.com/wiki/%E9%A6%96%E9%A1%B5

当前样式规范如下：

- 全站中文字体统一使用微软雅黑，并为不同系统准备相近的中文无衬线字体回退。正文、标题、导航、表格、提示框、文内代码与代码块都应保持同一字体风格。
- 页面背景使用固定的暗色渐变底色，避免长页面向下滚动时出现背景颜色断层。
- 正文整体字号和行距应偏紧凑，便于阅读长篇规则文本。
- Markdown 中的加粗文本使用接近参考图的橙色，用于突出关键规则名词。
- Markdown 中的文内代码与代码块使用接近参考图的浅黄色，背景仍保持深色。
- 一级标题不加分割线；二级标题添加暖金色下分割线；三级标题左右添加小箭头装饰。
- Material 内联图标需要单独调整基线，使图标与中文正文视觉居中。
- 普通提示框、详情块等重点块字号应略小于正文，形成补充说明的层级感。
- `formula` 提示框用于公式，字号略大于正文并居中显示。
- `example` 提示框用于规则示例，整体使用斜体。

---

### 5. 创建 `extra.js`（装饰脚本，暂时不需要）

创建 `docs/javascripts/extra.js`，但是不需要装饰，不影响显示即可。


---

### 6. 创建 `deploy.yml`（自动部署）

在 `.github/workflows/deploy.yml` 中粘贴：

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches:
      - main

permissions:
  contents: write

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.x'

      - name: Install MkDocs
        run: pip install mkdocs-material

      - name: Deploy
        run: mkdocs gh-deploy --force
```

> 这个文件创建后**不需要再改动**。每次推送 `main` 分支，网站自动更新。

---

### 7. 创建首页 `docs/index.md`

```markdown
# 失落王国编年史

> *愿每一次骰子落下，都成为一段传奇的开始。*

欢迎来到本规则手册。

## 本书结构

| 章节   | 内容     |
| ------ | -------- |
| 第三章 | 创建角色 |
```

---

### 8. 处理 `.md` 内容（AI 辅助）

将原始 `.md` 草稿发给 Claude，让它：

- 重构标题层级（`#` / `##` / `###`）
- 将数值、规则按要求整理为表格
- 按要求用提示框标注要点：
  ```markdown
  !!! note "GM提示"
      内容写在这里，缩进4格。

  !!! formula ""
      常用于公式计算。

  !!! example ""
      重要的规则运行范例。
  ```
- 给出对应的 `nav:` 配置行

处理完直接替换到 `docs/对应章节.md`。

---

### 9. 本地预览

```bash
mkdocs serve
# 浏览器打开 http://localhost:8000
```

---

## 二、首次上线

### 1. 在 GitHub 创建仓库

登录 github.com → **+** → **New repository** → 填仓库名 → 选 **Public** → **不勾选**任何初始化选项 → **Create repository**

### 2. 推送到 GitHub

```bash
git init
git add .
git commit -m "初始提交"
git branch -M main
git remote add origin https://github.com/你的用户名/ttrpg-rules.git
git push -u origin main
```

### 3. 手动发布一次

```bash
mkdocs gh-deploy
```

### 4. 开启 GitHub Pages

仓库网页 → **Settings → Pages → Branch 选 `gh-pages` → Save**

等 1-2 分钟，网站上线：`https://你的用户名.github.io/ttrpg-rules/`

---

## 三、自定义域名（可选）

1. 购买域名（Cloudflare / 阿里云 / 腾讯云）
2. DNS 添加记录：
   ```
   类型: CNAME  名称: @  值: 你的用户名.github.io
   ```
3. 在 `docs/` 下新建 `CNAME` 文件，内容为域名：
   ```
   yourdomain.com
   ```
4. 仓库 → Settings → Pages → Custom domain 填入域名，勾选 **Enforce HTTPS**

---

## 四、日常更新

### 修改已有章节

编辑对应 `.md` 文件后：

```bash
git add .
git commit -m "修改第三章：补充降级规则"
git push
```

### 添加新章节

1. 新建对应的 `docs/chapXX_title.md`，粘贴整理好的内容。
2. 根据章节归属，将页面加入 `mkdocs.yml` 的对应顶部大章节下；若该大章节尚无正式内容，先保留或更新占位页。
3. 顶部大章节本身应保持稳定，不随普通小节频繁增删；新增内容优先放入已有大章节分组。
4. 推送：
   ```bash
   git add .
   git commit -m "新增第四章"
   git push
   ```

推送后约 1-2 分钟，网站自动更新。

---

## 五、常用命令速查

| 操作         | 命令                                            |
| ------------ | ----------------------------------------------- |
| 安装依赖     | `pip install mkdocs-material`                   |
| 本地预览     | `mkdocs serve`                                  |
| 首次手动发布 | `mkdocs gh-deploy`                              |
| 日常推送更新 | `git add . && git commit -m "说明" && git push` |
