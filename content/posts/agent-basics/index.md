---
title: "Agent 入门｜给第一次使用 AI Agent 的同事"
date: 2026-08-19T10:20:00+08:00
description: "从模型、Chat 和 Agent 讲起，分清 Harness、Tool 与 Skill，并完成一个可以亲手检查结果的任务。"
tags: [Agent, AI, Harness, Tool, Skill]
featured_image: ""
images: []
categories: "Agent 入门"
comment: true
draft: false
---

在常见的线上聊天产品里输入一个问题，我们通常期待得到一段回答：解释一件事、整理一份摘要、给出几个建议。

使用 Agent 时，期待会不一样。我们不只想让它回答，还想让它读取材料、调用工具、处理文件，最后留下一个可以检查的结果。

两边都可能使用大语言模型，差别却不在模型名字。**Chat 是人与 AI 交互的一种形态；Agent 是模型在 Harness 和 Tool 支持下完成多步任务的一种工作方式。**产品会不断变化，这条关系更值得先弄清。

## 先把六个词放回原位

| 名称 | 它负责什么 |
|---|---|
| 模型 | 根据当前输入和上下文完成推理 |
| Chat | 让人通过多轮消息与模型交互 |
| Agent Harness | 保存会话、组织上下文、调度模型与工具 |
| Agent | 围绕一个目标反复观察、行动和核对 |
| Tool | 真正读取文件、运行命令、查询系统或写入结果 |
| Skill | 把一类任务的知识、步骤和模板交给 Agent |

模型可以判断下一步该做什么，但它不会凭空长出一双手。真正接触文件和系统的是 Tool；决定何时把什么信息交给模型、怎样收回工具结果的是 Harness；Skill 则像一份可复用的工作说明。

一个产品当前是 Chat 还是 Agent，不应只看它有没有聊天界面。更可靠的判断是：它能否取得真实现场，能否调用工具，能否观察结果，并根据结果继续工作。同一个产品也可能同时提供 Chat 与 Agent 能力。

## 看完以后，你应该能做到什么

- 用自己的话说清模型、Chat、Agent、Harness、Tool 和 Skill 的区别；
- 看懂模型推理与工具执行可能发生在不同位置；
- 判断一个产品当前提供的是聊天回答，还是可以继续执行的 Agent；
- 启动一个 Agent Harness，完成一次真实请求；
- 让 Agent 读取材料、创建结果，再由自己亲手验收；
- 给任务写清目标、范围、交付物和完成标准。

## 系列目录

### 第一部分：先把概念分开

1. [00｜模型、训练、推理和聊天是什么](/posts/agent-basics-00-model-chat/)
2. [01｜聊天机器人和 Agent 有什么不同](/posts/agent-basics-01-chat-agent/)
3. [02｜模型、Agent Harness、Tool、Skill 和模型服务分别在哪一层](/posts/agent-basics-02-claude-code-stack/)

### 第二部分：让 Agent 真正做一件事

4. [03｜在 Windows 上启动 Agent 环境](/posts/agent-basics-03-windows-setup/)
5. [04｜让 Agent 完成第一个真实任务](/posts/agent-basics-04-first-task/)

### 第三部分：从“能用”走到“会用”

6. [05｜Skill 是什么，它是怎么运行的](/posts/agent-basics-05-skill/)
7. [06｜普通同事可以用 Agent 做什么](/posts/agent-basics-06-scenarios/)
8. [07｜怎样给 Agent 交代清楚任务](/posts/agent-basics-07-task-brief/)

## 先记住三句话

**模型负责推理，工具负责行动。** 模型生成回答或动作建议，工具才真正接触文件、命令和业务系统。

**Agent Harness 不是模型。** 它把任务、上下文、模型、Tool 和 Skill 组织起来，让一次回答变成可以继续观察和执行的过程。

**结果需要验收。** Agent 说“完成了”不算证据；重新打开文件、检查系统状态或核对工具返回，才知道事情是否真的做成。

## 这套文章采用的实践边界

概念部分不绑定某个产品。实践部分以 Windows 和 DSH 为例，因为它能清楚展示 Web UI、Agent Preset、Tool 与 Skill 怎样组成一个 Agent。DSH 当前通过 Node.js 运行；Python 不是基础前提，只有某个 Skill 明确依赖 Python 脚本时才需要安装。

无论使用云端还是内部模型服务，模型服务都属于推理层，不是 Agent，也不是 Harness。模型服务负责推理；Harness 负责运行 Agent；Tool 负责接触真实环境。把这几层分开，换成别的模型、别的 Harness 或别的业务场景时，仍然能看懂整条链路。

准备好以后，从 [第 00 篇](/posts/agent-basics-00-model-chat/) 开始。
