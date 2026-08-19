---
title: "Agent 入门｜给第一次使用 AI Agent 的同事"
date: 2026-08-19T10:20:00+08:00
description: "从模型、聊天和 Agent 讲起，在 Windows 上安装 Claude Code 与 CC Switch，并用内部模型服务跑通第一个任务。"
tags: [Agent, Claude Code, CC Switch, Windows]
featured_image: ""
images: []
categories: "Agent 入门"
comment: true
draft: false
---

你不需要会编程，也不需要先学 Python。

这套文章写给第一次接触 AI Agent 的同事。我们从最容易混在一起的几个词讲起：模型是什么，聊天是什么，Agent 为什么能修改文件，Claude Code、CC Switch 和公司内部模型服务又分别处在哪一层。概念弄清以后，再在 Windows 上安装工具，完成一个可以亲手检查结果的小任务。

这不是一套“背术语”的课程。每一篇只解决一个具体问题：前三篇先看懂，后五篇再照着做。

## 学完以后，你应该能做到什么

- 用自己的话说清模型、聊天、Agent、工具和 Skill 的区别；
- 看懂一次任务是在自己的 Windows 电脑上运行，还是在远端模型服务中推理；
- 在 Windows 上安装 Claude Code 和 CC Switch，不为基础环境额外安装 Python 或 Node.js；
- 接入公司批准的模型服务，并通过真实请求验证，而不是只看“切换成功”的界面；
- 让 Agent 阅读、创建和修改一个练习目录里的文件；
- 给任务写清范围、交付物和验收标准，最后由人检查结果。

## 系列目录

### 第一部分：先把几个词分开

1. [00｜模型、训练、推理和聊天是什么](/posts/agent-basics-00-model-chat/)
2. [01｜聊天机器人和 Agent 有什么不同](/posts/agent-basics-01-chat-agent/)
3. [02｜Claude、Claude Code、CC Switch、XLB 分别是什么](/posts/agent-basics-02-claude-code-stack/)

### 第二部分：在 Windows 上跑起来

4. [03｜Windows 从零安装内部 Agent 环境](/posts/agent-basics-03-windows-setup/)
5. [04｜让 Agent 完成第一个真实任务](/posts/agent-basics-04-first-task/)

### 第三部分：从“能用”走到“会用”

6. [05｜Skill 是什么，它是怎么运行的](/posts/agent-basics-05-skill/)
7. [06｜普通同事可以用 Agent 做什么](/posts/agent-basics-06-scenarios/)
8. [07｜怎样给 Agent 交代清楚任务](/posts/agent-basics-07-task-brief/)

## 先记住三句话

**模型负责推理，工具负责行动。** 模型可以判断下一步该做什么，但真正读取文件、运行命令和保存结果的是工具。

**Claude Code 是 Agent 程序，不是模型本身。** 它运行在你的 Windows 电脑上，把任务、文件上下文和工具结果交给模型，再执行模型提出的下一步动作。

**Skill 是工作说明，不是另一个 AI。** 它告诉 Agent 某类任务应该按什么方法做；如果 Skill 附带脚本，真正运行脚本的仍是 Claude Code 所调用的本地工具。

## 这套文章采用的环境边界

安装篇只讲 **Windows 原生环境**，不讲 macOS，也不把 WSL、Python 或 Node.js 当作入门前提。以后某个具体 Skill 确实需要 Python 时，再按该 Skill 的固定版本和安装说明补装。

CC Switch 是第三方开源配置工具，不是 Anthropic 官方产品。本系列中的 **XLB**，专指部署在公司内部算力上的模型服务；“Qwen3 35B”采用公司内部简称，不把它当作公开标准型号或可直接填写的模型 ID，准确标识以内部配置卡为准。它不是安装在个人 Windows 电脑里的模型，也不是 Claude Code 的组成部分。这是公司内部的兼容接入方案，不是 Anthropic 官方支持的模型组合；一条消息能正常返回，也不代表 Claude Code 的全部能力都已兼容。正文会把确定的安装和验证路径写清；涉及内部地址、密钥来源、模型 ID 的部分，只使用占位符，由内部管理员提供，绝不会发布真实凭据。

准备好以后，从 [第 00 篇](/posts/agent-basics-00-model-chat/) 开始。
