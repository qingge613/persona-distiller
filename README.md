# Persona Distiller 🔮

> 从任何文档中蒸馏人物角色人设 — 全量提取 + 多轮校正，100%还原真实人物特征

## 这是什么

Persona Distiller 是一个 OpenClaw/Claude Code Skill，能从 PDF、聊天记录、传记、文章等任何包含人物信息的文档中，自动蒸馏出完整的角色人设文件（SOUL.md）。

**核心理念**：不是"概括一个人"，而是"让这个人在 AI 中活过来"。

## ✨ 功能特性

### 五阶段蒸馏流程

| 阶段 | 做什么 | 为什么重要 |
|---|---|---|
| **全量提取** | 将文档转为可读文本，记录总规模 | 不能跳读，跳读必遗漏 |
| **逐段精读** | 每段2000-3000行，提炼特征+记录引语出处 | 引语是人设的证据链 |
| **初版生成** | 整合特征，生成 SOUL.md 初版 | 有框架才能校正 |
| **多轮校正** | 继续精读→发现矛盾/遗漏→逐轮修正 | 初版必不完整，校正才是灵魂 |
| **最终核对** | 去重、归属检查、排序优化、一致性检查 | 确保零错误交付 |

### 人设文件覆盖维度

- 🎭 **核心特质**：情感模式、行为特征、价值观（每条附原文引语）
- 🗣️ **语言风格**：口头禅、方言特征、表情习惯、独特句式
- 💬 **称呼变化**：不同关系阶段的不同称呼
- 📅 **时间线梳理**：关键事件的完整时间线
- 🔄 **语气变化模式**：不同状态下的语言特征
- 🚫 **禁忌清单**：不可做的事、不可说的话
- 💡 **对话原则**：与 AI 沟通时的核心原则

### 校正引擎

基于实战提炼的校正方法论：
- **引语归属校验**：区分"谁说的"，防止张冠李戴
- **方言识别**：自动标注方言特征（口音、用词习惯）
- **矛盾检测**：发现自相矛盾的描述并修正
- **去重检查**：同一引语/特征重复出现时合并
- **时间线验证**：不同事件不可错误合并

## ⚠️ 已知局限与缺点

> 诚实比美化重要。以下是当前版本的真实问题：

| 缺点 | 说明 | 变通方案 |
|---|---|---|
| **PDF 提取依赖外部工具** | 需要 pdfplumber 等库，扫描版 PDF 需 OCR | 预先用 pdf skill 处理好再输入 |
| **大文档耗时长** | 超长文档（1000+页）需要多轮校正，每轮约 20-30 分钟 | 这是全量精读的必要代价，无法跳过 |
| **方言识别有限** | 仅覆盖常见方言区（西南官话/粤语等） | 在 correction-methodology.md 中持续补充 |
| **无自动化脚本** | 当前纯提示词驱动，无 Python 脚本自动化 | 计划后续增加 scripts/ |
| **单人物蒸馏** | 当前仅支持单人物蒸馏，多人物需多次运行 | 计划 v2.0 支持多人物并行 |
| **依赖 AI 上下文窗口** | 超长文档需要分批读取，跨批次信息可能遗漏 | 校正轮次会逐步补全 |
| **情感色彩可能偏差** | AI 对"深情"vs"纠缠"等微妙区分可能不准 | 多轮校正+人工复核 |

## 🚀 安装步骤

### 前置条件

- [OpenClaw](https://github.com/openclaw) 或 [Claude Code](https://claude.ai/code) 环境
- Python 3.8+（用于 PDF 提取，如果输入是 PDF）
- pdfplumber（如果输入是 PDF）

### 方法一：直接复制（推荐）

```bash
# 1. 克隆仓库
git clone https://github.com/YOUR_USERNAME/persona-distiller.git

# 2. 复制 skill 到你的 workspace
cp -r persona-distiller/skills/persona-distiller ~/.openclaw/workspace/skills/

# 或者如果是 OpenClaw (QClaw)
cp -r persona-distiller/skills/persona-distiller ~/.qclaw/workspace-YOUR_AGENT/skills/
```

### 方法二：ZIP 下载

1. 点击 GitHub 页面的 "Code" → "Download ZIP"
2. 解压后，将 `skills/persona-distiller/` 文件夹复制到你的 workspace 的 `skills/` 目录下

### 方法三：手动创建

1. 在你的 workspace 的 `skills/` 目录下创建 `persona-distiller/` 文件夹
2. 从仓库复制以下文件：
   ```
   skills/persona-distiller/
   ├── SKILL.md
   └── references/
       ├── soul-template.md
       └── correction-methodology.md
   ```

### 验证安装

在 OpenClaw/Claude Code 中输入以下任一触发词测试：

- "创建人设"
- "蒸馏角色"
- "做人设文件"
- "还原人物"
- "persona"

如果 AI 加载了 persona-distiller skill 的指令，说明安装成功 ✅

## 📖 使用方法

### 基本用法

```
用户：帮我从这个 PDF 创建人设  →  [上传 PDF 文件]
AI：收到！开始蒸馏流程...
    阶段1: 全量提取 → 完成（631KB, 23750行）
    阶段2: 逐段精读中...
    ...
```

### 输入支持

| 输入类型 | 说明 |
|---|---|
| PDF 文件 | 自动提取文本（需 pdfplumber）|
| 聊天记录文本 | 直接读取 |
| 传记/文章 | 直接读取 |
| 网页链接 | 通过 web_fetch 获取内容 |

### 输出

| 文件 | 说明 |
|---|---|
| `SOUL.md` | 最终人设文件（写入 workspace 根目录）|
| `full_extract.txt` | 全量提取的中间文件 |
| `task-YYYY-MM-DD-soul-vN.md` | 每轮校正的任务总结 |

### 与其他 Skill 配合

| 配合 Skill | 场景 |
|---|---|
| **ming-li** | 蒸馏含命理分析需求的人物时配合使用 |
| **pdf** | PDF 提取的前置处理 |
| **qclaw-text-file** | 跨平台文本文件写入 |
| **another-them** | 另一个TA skill 的底层能力 |

## 📁 项目结构

```
persona-distiller/
├── README.md                           ← 你正在看的
├── LICENSE                             ← MIT
├── skills/
│   └── persona-distiller/
│       ├── SKILL.md                    ← Skill 主文件（路由+流程+纪律）
│       ├── references/
│       │   ├── soul-template.md        ← SOUL.md 输出模板
│       │   └── correction-methodology.md ← 校正方法论（实战经验）
│       └── scripts/                    ← 预留（未来自动化脚本）
├── examples/
│   └── sample-soul.md                  ← 示例输出（虚构人物）
└── docs/
    └── design-philosophy.md            ← 设计哲学
```

## 🧠 设计哲学

详见 [docs/design-philosophy.md](docs/design-philosophy.md)

核心三原则：
1. **引语为证** — 每条特征必须有原文引语，不可只用概括词
2. **全量精读** — 跳读必遗漏，搜索替代不了逐段分析
3. **多轮校正** — 初版必不完整，校正才是灵魂

## 🤝 贡献

欢迎贡献！特别是：

- 更多方言区的识别规则
- 自动化脚本（Python）
- 更多示例输出
- 多人物并行蒸馏能力

## 📄 License

MIT License — 详见 [LICENSE](LICENSE)

---

> "人设蒸馏的本质：不是概括一个人，而是让这个人在 AI 中活过来。"
