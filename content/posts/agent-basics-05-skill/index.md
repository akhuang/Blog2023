---
title: "Agent 入门 05｜Skill 到底是什么：把一套做法交给 Agent"
date: 2026-08-19T09:50:00+08:00
description: "分清模型、Agent、Skill、工具与脚本，并看懂 Skill 在一次 Claude Code 任务中的加载和执行路径。"
tags: [Agent, Claude Code, Skill]
featured_image: ""
images: []
categories: "Agent 入门"
comment: true
draft: false
---

> 系列导读：[Agent 入门：给 Windows 小白的第一套地图](/posts/agent-basics/)

上一篇里，我们临时告诉 Agent 怎样读取三份材料、怎样生成清单、怎样检查。下一次遇到同类任务，还得再说一遍。如果一套做法会反复使用，就可以把它整理成 **Skill**。

Skill 不是一个新模型，也不是另一个 Agent。它更像放在 Agent 工具箱里的一份“作业说明书”：写清什么时候该用、按什么步骤做、可以参考哪些模板，以及必要时运行哪个现成脚本。

## 先把五个容易混在一起的词拆开

| 名称 | 它负责什么 |
|---|---|
| 模型 | 理解文字、推理并生成下一步内容 |
| Agent | 围绕目标反复调用模型和工具，把多步任务做完 |
| Skill | 给 Agent 一套可复用的领域知识或工作步骤 |
| 工具 | 真正读取文件、搜索内容、执行命令或写入结果 |
| 脚本 | Skill 可选的辅助程序，用来完成确定性的处理 |

因此，“我装了一个 Skill”不等于换了更聪明的模型。它改变的是 Agent 做某类事情的方法。就像同一个新同事拿到一份清楚的交付模板后，工作方式会更稳定，但人还是那个人。

## 一次 Skill 是怎样运行起来的

Claude Code 的 Skill 通常以一个目录存在，入口文件叫 `SKILL.md`。最小结构如下：

```text
meeting-summary/
└── SKILL.md
```

复杂一点的 Skill 还可以带模板、示例、参考资料或脚本：

```text
meeting-summary/
├── SKILL.md
├── template.md
├── examples/
│   └── sample.md
└── scripts/
    └── normalize.py
```

`SKILL.md` 顶部通常有一段 YAML，其中最关键的是 `description`。它不只是给人看的简介，也是 Agent 判断“这次任务是否需要这项 Skill”的线索。正文才是完整步骤，例如先读哪些材料、输出采用什么格式、什么时候引用模板。

这里有个很实用的设计：默认允许模型自动调用的 Skill，平时只把名称和简短描述交给 Agent，不必把完整说明一次塞进会话。只有描述与任务匹配时，正文才按需加载。若 Skill 设置了 `disable-model-invocation: true`，它不会参加自动匹配，只能由用户手动调用。这样工具箱可以逐渐变大，当前任务却仍只拿到真正相关的做法。

![Skill 在一次任务中的运行路径](skill-runtime.png)

把运行过程展开，就是下面四步：

1. **发现**：会话开始时，Claude Code 看见默认允许自动调用的 Skill 名称和 `description`，知道工具箱里有哪些能力。
2. **按需加载**：你的任务与某个描述匹配，或你直接输入对应的 `/skill-name` 后，完整 `SKILL.md` 才进入当前对话。
3. **执行**：Agent 按说明调用已有工具，例如读取本地文件、搜索项目内容、创建文档；如果 Skill 指定了脚本，也可以调用脚本。
4. **返回**：工具或脚本把实际结果交回模型，模型再组织成你看到的回答，并继续下一步。

这条链路里，Skill 不会自己“思考”，脚本也不会自己变成 Agent。Agent 负责调度，工具负责接触真实环境，模型负责理解结果。

所以，看到最终回答时，不要把所有功劳都归给 Skill。回答来自模型，文件事实来自工具，固定转换可能来自脚本，Skill 的贡献是把这些部件按团队认可的次序组织起来。少了任何一层，任务仍可能完成，但过程和结果就不一定相同。

## 用“会议纪要 Skill”跑一遍

假设团队经常把会议记录整理成行动清单。这个 Skill 的 `description` 可以写成：

```yaml
description: 将会议记录整理为包含事项、负责人、截止时间和待确认项的行动清单。用户要求整理会议纪要或提取待办时使用。
```

当你说“把这个文件夹里的会议纪要整理成待办”，Agent 会发现描述匹配，于是读取完整 `SKILL.md`。说明书要求它先找事实，再套用 `template.md`，最后检查人名和日期。Agent 随后用已有的文件读取和写入工具完成任务，结果回到模型，由模型告诉你生成了哪个文件、还有哪些信息待确认。

如果记录来自一种特殊格式，Skill 也许会附带 `normalize.py`，先把原始内容转换成统一文本。但这是 **这个 Skill 的实现需要 Python**，不是所有 Skill 都需要 Python。很多 Skill 只有 Markdown 说明和模板，直接使用 Claude Code 已有工具就能完成。基础环境阶段不必预装 Python；真正调用一个含 Python 脚本的 Skill 时，再看它的安装说明。

脚本也不一定是 Python。它可能是 PowerShell、Shell，甚至一个已经编译好的程序。判断依赖的依据应当是 Skill 中写明的要求，而不是让 Agent 看到一个新任务就自行扩充电脑环境。

## Skill 放在哪里

按照 [Claude Code 官方 Skills 文档](https://code.claude.com/docs/en/skills)，个人 Skill 可以放在：

```text
~/.claude/skills/<skill-name>/SKILL.md
```

项目团队共用的 Skill 可以放在项目目录：

```text
.claude/skills/<skill-name>/SKILL.md
```

在 Windows 中，`~` 表示当前用户的主目录。个人 Skill 可用于你的多个项目；项目 Skill 跟着项目文件走，适合团队共享同一套规则。[CC Switch](https://github.com/farion1231/cc-switch) 也提供第三方的 Skill 管理和同步界面，但它没有改变 Skill 的本质：最终仍是一组会被 Claude Code 发现并按需加载的文件。

## 什么时候值得做成 Skill

判断标准不是“这段提示词很长”，而是这套方法会不会重复出现。每周都要按同一格式整理会议纪要，适合做 Skill；只发生一次、输入输出还没想清楚的临时任务，先直接和 Agent 对话更快。

Skill 也不能弥补缺失的事实。会议记录没有开始时间，再详细的 Skill 也只能把它列为待确认，不能替团队决定一个时间。Skill 固化的是做事方法，不是凭空补齐业务信息。

一个好 Skill 让同类任务少解释一遍，也让步骤、模板和必要工具留在同一个地方。它不会替模型增加智力，却能把团队已经验证过的做法，在需要时准确地交给 Agent。

---

上一篇：[Agent 入门 04｜第一个真实任务](/posts/agent-basics-04-first-task/) ｜ 下一篇：[Agent 入门 06｜普通同事可以用 Agent 做什么](/posts/agent-basics-06-scenarios/)
