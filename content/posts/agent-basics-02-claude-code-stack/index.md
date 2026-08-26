---
title: "Agent 入门 02｜模型、Harness、Agent、Tool 与 Skill 如何协作"
date: 2026-08-19T09:20:00+08:00
description: "把模型服务、Agent Harness、Agent、Tool 与 Skill 放回各自层级，并用 DSH 看懂一次任务怎样流过整套系统。"
tags: ["Agent", "DSH", "Harness", "Tool", "Skill", "模型服务"]
categories: "Agent 入门"
comment: true
draft: false
---

> 系列导读：[Agent 入门｜从会回答，到能完成任务](/posts/agent-basics/)

当一个 Agent 读完文件、修改内容并报告结果时，我们看到的是一段连续过程。系统内部却不是一个东西从头做到尾：模型服务负责推理，Harness 负责调度，Tool 负责行动，Skill 提供做事方法，Agent 则是这些部分围绕目标运行起来后的整体。

**理解 Agent 的关键，不是记住更多产品名，而是看清信息和动作怎样在这些层之间流动。**

![模型服务、DSH、Agent、Tool 与 Skill 的分层关系](windows-agent-stack.png)

## 先分清“部件”和“运行中的整体”

| 层级 | 它负责什么 |
|---|---|
| 人与任务 | 给出目标、材料、范围和完成标准 |
| Agent | 在一次会话中围绕目标持续判断和行动 |
| Agent Harness | 管理会话、上下文、模型调用和工具执行 |
| 模型服务 | 运行模型推理，返回文字或工具调用意图 |
| Tool | 读取或改变文件、命令、网页和业务系统 |
| Skill | 为某类任务提供知识、步骤、模板和边界 |
| 工作环境 | 保存文件、程序状态和工具真正接触的对象 |

Agent 不是表格里又一个可以单独下载的零件。它更像“运行中的工作单元”：某个目标进入 Harness 后，模型、指令和工具被组织起来，在一个会话里持续工作。任务结束，或者会话停止，这一次 Agent 运行也就结束了。

## 模型服务只负责一次次推理

模型服务接收一包输入，其中可能包含人的要求、对话历史、文件片段、Skill 说明和上一步工具结果。模型根据这些信息生成回答，或者提出下一步工具调用。

但模型服务通常不直接替你移动本机文件，也不天然知道一个命令是否成功。它只能根据 Harness 送来的上下文作判断。工具执行以后，Harness 必须把真实结果再次送回，模型才能继续。

模型服务可以运行在本机、内网或云端；所在位置会改变连接方式和数据路径，却不改变它在结构中的职责：提供推理，而不是独自承担完整的 Agent 循环。

## Harness 把分散部件接成循环

Harness 是这套系统的调度层。它保存会话，选择本轮要交给模型的上下文，向模型说明有哪些工具可用，执行模型发出的工具调用，再把结果放回下一轮。

DSH 是一个开源 Agent Harness。它采用插件化结构，把模型接入、工具、Skill、会话能力和界面等组成部分装配在一起。[DSH 项目说明](https://github.com/deepseek-ai/deepseek-harness)

在 DSH 中，Agent Preset 用来定义一类 Agent 在单个会话中采用的身份、提示和工具组合。选择不同 Preset，不是在更换“另一个模型名字”，而是在改变这次 Agent 运行时可见的说明和能力集合。[Agent Preset 的作用](https://github.com/deepseek-ai/deepseek-harness/tree/master/packages/preset)

DSH 只是 Harness 的一个具体例子。其他系统可以用不同的界面、配置方式和扩展机制，但只要它承担上下文管理、模型调度和工具回传，就处在同一层。

## Tool 是动作，Skill 是方法

Tool 和 Skill 经常一起出现，却解决不同问题。

Tool 回答“系统能做什么动作”。文件工具可以读取和写入，网页工具可以打开页面，业务工具可以查询或提交数据。它接收明确参数，执行后返回执行结果或现场证据；这些结果仍需要结合来源和任务目标核对。

Skill 回答“面对一类任务，应该怎样做”。它可以要求先取哪些信息、按什么顺序判断、使用什么模板、最后怎样验收。Skill 可能指导 Agent 调用多个 Tool，却不等于那些 Tool 本身。

例如，查询订单状态的是 Tool；“先核对订单范围，再归纳异常，最后生成一份待审摘要”是 Skill。只有 Tool，Agent 有手但未必懂这类工作的做法；只有 Skill，没有相应 Tool，它知道步骤却无法取得真实数据。

DSH 的 Skill 机制也遵循这条分工：系统先向模型提供可用 Skill 的目录，任务需要时再加载具体内容。加载发生在会话和上下文层，不会修改基础模型的参数。[DSH Skills 说明](https://github.com/deepseek-ai/deepseek-harness/blob/master/docs/subsystems/skills.md)

## 一次任务怎样流过系统

把“读取三份材料，生成差异清单并核对数量”交给 Agent 后，典型链路是：

```text
人给出目标与材料
  -> Harness 组织本轮上下文
  -> 模型服务判断下一步
  -> Harness 调用读取工具
  -> 工具返回文件内容
  -> 模型根据内容生成清单
  -> Harness 调用写入工具
  -> 工具返回写入结果
  -> Agent 重新读取并核对数量
```

这条链路也解释了为什么“模型回答正常”不能证明整个 Agent 正常。模型服务能返回文字，只说明推理通道可用；读不到文件，要查工作环境和文件 Tool；知道步骤却没有按要求执行，要看 Skill 是否进入上下文；工具执行后不再核对，则要看 Harness 是否继续了循环。

分层不是为了多造术语，而是为了让问题有归属。模型决定得不好、工具没有执行、Skill 没被采用、会话丢了上下文，是四类不同故障。把它们都叫作“Agent 不行”，就失去了定位问题的入口。

最终可以把整套关系收成一句话：**模型服务提供判断，Tool 提供动作，Skill 提供方法，Harness 让三者围绕目标持续协作；这段运行中的协作，就是我们所说的 Agent。**

---

上一篇：[Agent 入门 01｜Chat 和 Agent，差别究竟在哪里](/posts/agent-basics-01-chat-agent/) ｜ 下一篇：[Agent 入门 03｜DSH 是什么：Agent 如何被组合出来](/posts/agent-basics-03-windows-setup/)
