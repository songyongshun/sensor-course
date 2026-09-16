# 智能传感器装调与测试 · 课程数字化讲义

把 `input/` 目录下的课程资料（课件 PPTX + 教案 PDF）自动转换成按“项目 / 任务”
组织的 Markdown 讲义，使用 [VitePress](https://vitepress.dev/) 生成静态网站。

## 目录结构

```
input/向楠-智能传感器装调与测试-978-7-111-80838-1/
  ├── 《智能传感器装调与测试》教案.pdf          全套课时教学设计（109 页）
  └── 智能传感器装调与测试-PPT/智能传感器装调与测试PPT/
      ├── 传感器-项目一/传感器-项目一-任务1 智能网联汽车认知.pptx
      ├── 传感器-项目二/……                      每项目若干任务
      └── 传感器-项目五/……
scripts/build_course_docs.py                    课件/教案 -> Markdown 转换脚本
docs/
  ├── index.md                                  站点首页
  ├── .vitepress/config.mts                     站点配置（导航、侧边栏、大纲）
  ├── .vitepress/sidebar-course.mts             侧边栏（脚本自动生成）
  ├── course/                                   讲义正文
  │   ├── index.md                              课程总览
  │   └── chapter-01/ ~ chapter-05/
  │       ├── index.md                          项目首页
  │       └── <任务>.md                         每个任务一页（共 15 页）
  ├── public/images/                            从课件中提取的幻灯片插图
  └── public/downloads/jiaoan.pdf               教案 PDF（供下载）
```

## 环境要求

* Windows + PowerShell 7
* Python 虚拟环境：`work python-docx`（该环境已安装 `python-pptx`）
* 系统命令 `pdftotext`（poppler，本机随 TeXLive 提供；用于解析教案 PDF）
* Node.js + npm（仅网站预览/构建需要）

## 常用命令

```powershell
# 1) 本地预览网站
npm run docs:dev

# 2) 重新生成讲义（课件/教案更新后执行）
work python-docx
python scripts\build_course_docs.py                  # 全量生成
python scripts\build_course_docs.py --clean          # 先清空旧输出再生成
python scripts\build_course_docs.py --chapter 3 -v   # 只生成项目三
python scripts\build_course_docs.py --no-images      # 不导出插图
python scripts\build_course_docs.py --no-jiaoan      # 不解析教案 PDF
```

> 注意：本项目当前**不执行** `npm run docs:build`（构建环境存在兼容问题），
> 预览请统一使用 `npm run docs:dev`。

## 转换规则

| 内容 | 处理方式 |
| --- | --- |
| 任务页 | 一份 PPTX 课件对应一页 Markdown，标题取自文件名（`传感器-项目X-任务N 名称.pptx`） |
| 项目页 | 目录名 `传感器-项目X` 对应一章；项目名称从教案目录中读取 |
| 封面 / 目录 / 结束页 | 自动丢弃 |
| 章节分隔页 | 形如 `02 超声波雷达结构与组成` 的整页分隔 → 二级标题（`## 二、超声波雷达结构与组成`） |
| 幻灯片小标题 | 页面上部的短文本（如 `1、车载超声波雷达的基本结构`）→ 三级标题，连续重复只保留一次 |
| 正文小标题 | 行内的 `1、xx` → 三级标题，`（1）xx` → 四级标题 |
| 表格 | PPTX 表格转为 Markdown 表格 |
| 插图 | 提取课件截图/示意图，按幻灯片顺序插入；装饰小图、整页底图与跨课件复用的装饰图剔除 |
| 栏目横幅 | “名师讲堂”“项目概述”等版面栏目名自动剔除，不作为正文 |
| 教学设计参考 | 教案按任务切分，抽取教学目标（素质/知识/能力）、教学重点与难点、教学策略、教学基本信息 |
| 教案 PDF | 复制为 `docs/public/downloads/jiaoan.pdf`，在课程总览与侧边栏提供下载 |

## 修改讲义的两种方式

1. **改课件/教案**（推荐）：更新 `input/` 下的文件，重新运行转换脚本，页面自动更新。
2. **直接改 Markdown**：直接编辑 `docs/course/**/*.md`；注意下次重新运行脚本会覆盖这些改动。

## 已知情况

* 课件里“安装与测试”“任务考查”等实操环节多以视频/实训工单形式存在，
  转换后只保留页面上的文字、表格与插图。
* 教案 PDF 是表格排版（标签竖向居中），脚本用“最近标签”规则归组条目，
  个别任务的条目归属可能与原表格有细微差异，可在页面上手工微调。
* 原课件的编号存在不一致（例如文件名写“超声波传感器”而课件内部写“超声波雷达”），
  讲义标题以文件名为准，未做改写。