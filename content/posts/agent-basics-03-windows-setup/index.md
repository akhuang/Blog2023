---
title: "Agent 入门 03｜Windows 原生环境：装好 Claude Code、CC Switch 与内部 XLB"
date: 2026-08-19T09:30:00+08:00
description: "面向 Windows 新手的原生安装路线：Claude Code、CC Switch、内部 XLB Provider 与三步验证。"
tags: [Agent, Claude Code, CC Switch, Windows]
featured_image: ""
images: []
categories: "Agent 入门"
comment: true
draft: false
---

> 系列导读：[Agent 入门：给 Windows 小白的第一套地图](/posts/agent-basics/)

这一篇只走 **Windows 原生路径**，不装 WSL，也不先搭一套开发环境。我们要接通的是三层东西：Claude Code 是在电脑上执行任务的 Agent；CC Switch 管理 Claude Code 使用哪套 Provider 配置；XLB 是部署在公司内部算力上的模型服务，公司内部简称为“Qwen3 35B”。这个简称不是公开标准型号，也不是可以照抄的模型 ID，准确标识必须来自内部配置卡。这里确认的是模型服务的部署和推理位置，不代表模型训练也发生在公司内部。它们不是同一个软件，更不是把 XLB 模型下载到你的 Windows 电脑里。

还要先说清产品边界：内部所称的 Qwen 模型不是 Claude 模型。这是公司内部的兼容接入方案，不是 Anthropic 官方支持的模型组合。一次消息返回成功，只证明最小请求能够往返；工具调用、流式输出、长上下文等能力是否完整兼容，还要继续用真实任务确认。

目标很具体：安装完成后，你能启动 Claude Code，确认它正在使用预期的 XLB 配置，并发出一条真实消息得到回答。

安装顺序也不要颠倒。先装 Claude Code，是为了得到真正执行任务的客户端；再装 CC Switch，是为了给这个客户端切换配置；最后按内部说明填写 XLB Provider，才形成公司要求的请求路径。只装 CC Switch 不会自动得到 Agent，只装 Claude Code 也不会自动知道公司内部通道在哪里。

## 开始前：只打开 Windows Terminal

本文适用于 Windows 10 版本 1809 及以上，或 Windows 11。按 `Win` 键，搜索并打开 **Windows Terminal**。如果电脑里没有它，可以从 Microsoft Store 安装；公司电脑无法使用商店时，再使用系统已有的终端。

Windows Terminal 是装命令窗口的应用，里面默认打开的标签可能叫 PowerShell。你不需要先学 PowerShell 语法：本文让你输入的主要是 `winget`、`git` 和 `claude` 这些普通命令，在 Terminal 的默认标签中就能运行。

基础环境不要求 Python，也不要求 Node.js。Claude Code 已提供 Windows 安装包，不需要先用 `npm` 安装。以后某个 Skill 明确包含 Python 脚本时，再按那个 Skill 的说明补装 Python；不要让 Agent 在第一次配置时自行决定安装一串语言环境。

本文也不使用 WSL。WSL 是 Windows 里的 Linux 环境，适合本来就在 Linux 工具链中工作的项目，但它会引入另一套目录和命令。小白第一次接通 Agent 时，Windows 文件就留在 Windows 目录，命令就在 Windows Terminal 执行，更容易知道自己正在操作哪台电脑上的哪个文件。

## 第一步：Git for Windows 可选，但推荐安装

Claude Code 在没有 Git for Windows 时也能执行本地命令，所以 Git 不是启动它的硬门槛。**只完成本系列的文件练习，可以直接跳到第二步。**以后要让 Agent 进入用 Git 管理的项目，再安装 Git for Windows；官方推荐它，是因为很多项目使用 Git 管理文件，Claude Code 也可以使用随 Git 提供的 Bash 工具。

如果你已经安装过 Git，可以在 Terminal 输入：

```console
git --version
```

出现版本号就说明 Git 已可用；提示找不到 `git` 也不影响本系列后续步骤。需要进入 Git 项目时，再从 [Git for Windows 官方页面](https://git-scm.com/downloads/win)下载安装，安装界面保持默认选项即可。完成后关闭 Terminal，再重新打开一次。

## 第二步：用 WinGet 安装 Claude Code

先输入 `winget --version`。看到版本号就继续；如果提示找不到 `winget`，请在 Microsoft Store 更新“应用安装程序（App Installer）”，公司电脑无法使用商店时则联系内部支持，不要改回网上找到的下载脚本。

然后在 Windows Terminal 输入下面这行，按回车：

```console
winget install Anthropic.ClaudeCode
```

这是 [Claude Code 官方 Windows 安装文档](https://code.claude.com/docs/en/installation) 提供的 WinGet 安装方式。它比复制一段 PowerShell 下载脚本更容易读：`winget` 是 Windows 包管理器，后面是官方包名。若 WinGet 首次询问是否接受软件源条款，阅读后按界面提示确认。

安装结束后，关闭并重新打开 Windows Terminal，然后检查版本：

```console
claude --version
```

看到版本号，只能说明 **Claude Code 客户端已经装好**，还不能说明内部 XLB 通道已经接通。还可以运行 `claude doctor`，让官方自带的只读诊断检查安装和配置文件。WinGet 安装不会由 Claude Code 自动升级，以后需要更新时运行 `winget upgrade Anthropic.ClaudeCode`。如果安装源被公司网络策略拦截，请向内部支持人员获取批准的安装方式，不要自行寻找来路不明的脚本或镜像。

## 第三步：先拿到内部配置卡

打开 CC Switch 之前，讲师或内部管理员必须提供一张已经在目标 Windows 电脑上验证过的配置卡。小白无法从“XLB”或“Qwen3 35B”这两个名字推断接口格式，更不能靠试错猜认证方式。缺少下面任一关键项时，应先补齐配置卡，不要继续照着网上截图乱填。

| 配置卡字段 | 应填写的内容 |
|---|---|
| 已验证的 CC Switch 版本 | `<例如 v3.x.x，以培训当天实测为准>` |
| Provider 显示名 | `XLB-内部` |
| 精确模型 ID | `<内部管理员提供，不能写“Qwen3 35B”代替>` |
| API 格式 | `<Anthropic Messages / OpenAI Chat Completions / OpenAI Responses>` |
| Base URL 或完整接口 URL | `<内部管理员提供>` |
| 认证字段 | `<API Key / Auth Token / 其他，以实测为准>` |
| Full URL 模式 | `<开 / 关>` |
| 本地 Routing | `<开 / 关>` |
| Claude Code takeover（接管） | `<开 / 关>` |
| fallback、Sonnet / Opus / Haiku 角色映射 | `<路线 A/B 是否需要；若只有一个模型，是否全部映射到同一 ID>` |
| 切换后是否重启 Claude Code | `<是 / 否>` |
| 内部支持入口 | `<群组或负责人，不写个人密钥>` |

这些尖括号内容是占位符，不能原样照填。真实内网地址和个人密钥只填进 CC Switch 对应输入框，不要写进博客、群聊或培训截图。配置卡必须标出下面两条路线中的一条。

## 第四步：按配置卡安装 CC Switch 的 MSI

打开 [farion1231/cc-switch 官方 Releases](https://github.com/farion1231/cc-switch/releases)，找到配置卡写明的精确版本。例如卡片写 `v3.x.x`，就进入对应 tag 的发布页，不要自行改装当日最新版；只有配置卡明确写“使用最新版”时，才使用 [`releases/latest`](https://github.com/farion1231/cc-switch/releases/latest)。在该版本的 Assets 中选择文件名类似下面这一项：

```text
CC-Switch-v{配置卡版本号}-Windows.msi
```

官方项目把 MSI 列为 Windows 推荐安装包。普通 Intel/AMD Windows 电脑选择常规 Windows MSI；Windows ARM 设备选择文件名带 `arm64` 的制品。下载后双击，按安装向导完成即可。安装完成后核对“关于”或版本信息，必须与配置卡一致，才能继续使用同一套字段和截图。

## 第五步：按 API 格式选择一条配置路线

打开 CC Switch，进入 Claude Code 的 Provider 管理，选择“添加 Provider”或自定义配置。不同版本的字段名称可能略有变化，因此培训时要使用配置卡写明的已验证版本。

### 路线 A：XLB 直接兼容 Anthropic Messages

选择 `Anthropic Messages`，依次填写配置卡中的地址、认证字段和精确模型 ID。若配置卡要求设置 fallback、Sonnet、Opus、Haiku 角色，也必须逐项填写；如果 XLB 只有一个内部模型，是否把所有角色映射到同一 ID 仍由实测配置卡决定，不能自行省略。保存并启用 `XLB-内部`。这条路线由 Claude Code 直接请求内部兼容接口，不需要 CC Switch 在本机把另一种 API 格式转换成 Anthropic Messages。

### 路线 B：XLB 提供 OpenAI Chat 或 OpenAI Responses

选择配置卡指定的 `OpenAI Chat Completions` 或 `OpenAI Responses`，填写地址、认证字段和精确模型 ID，并按卡片设置 Full URL 与 fallback、Sonnet、Opus、Haiku 角色映射。然后进入 CC Switch 的 Local Routing 页面，启动本地路由，并为 Claude Code 开启 takeover（接管）。这时 Claude Code 先连接本机路由，再由 CC Switch 转换请求格式并转发到 XLB；只保存 Provider、不启动路由和接管，链路不会完整工作。使用期间要让本地 Routing 持续运行，可以把 CC Switch 最小化到托盘，但不要退出程序或停止路由。[CC Switch Provider 配置说明](https://github.com/farion1231/cc-switch-website/blob/main/public/docs/en/2-providers/2.1-add.md#advanced-options)

三个最容易填混的字段可以这样理解：Base URL 是服务入口，像办公楼地址；API Key 或 Token 是进入服务的凭证，像门禁卡；Model 是要把请求交给哪一个模型，像具体房间号。地址正确但凭证不对，请求会被拒绝；地址和凭证都正确但模型标识写错，也可能找不到目标模型。这里必须逐项采用内部配置卡，不能从网上找一个看起来相似的值代替。

## 第六步：按所选路线验证模型通道

重新打开一个 Windows Terminal，在任意普通文件夹运行：

```console
claude
```

首次启动时，如果界面询问是否批准当前 API Key，按内部说明确认即可，这是使用环境凭据时可能出现的正常步骤；如果它跳转到个人 Claude 账号登录，则通常表示内部凭据没有生效。真正进入会话后，再继续下面的检查。

进入 Claude Code 后，先输入：

```text
/status
```

如果你走的是路线 A，检查状态中的 `Anthropic base URL`、`Auth token` 或 `API key` 来源，以及当前模型是否与内部配置卡一致。如果启动后仍要求使用个人 Claude 账号登录，通常说明内部凭据没有正确传给 Claude Code，应先回到 CC Switch 和配置卡检查。

如果你走的是路线 B，`/status` 中出现 `127.0.0.1` 一类本地地址反而是正常现象，因为 Claude Code 此时先连 CC Switch 本地路由。再输入 `/model` 查看 Claude 角色映射，然后打开 CC Switch 的 Routing 页面准备观察。首个请求发出前，`Current Provider` 仍可能处于等待状态，这是正常的。

最后发送一条真实消息：

```text
请只回答“连接成功”。不要读取、创建或修改本地文件。
```

路线 B 的用户此时再回到 Routing 页面：确认 `Current Provider` 变为 `XLB-内部`，并且请求计数增加。只有把本地地址、角色映射、真实请求、当前 Provider 和计数连起来，才能证明请求从 Claude Code 进入本地路由并继续发往 XLB。

现在你有三份不同层级的证据：

1. `claude --version` 有输出：客户端安装成功。
2. 路线 A 的 `/status` 与配置卡一致，或路线 B 的本地地址、`/model`、当前 Provider 与请求计数相互吻合：Claude Code 实际采用了预期路径。
3. 普通消息得到合理回答：一次真实模型请求已经往返成功；这一步本身不能单独证明上游模型身份或全部能力都兼容。

只看到欢迎界面不算全部完成，只看到 CC Switch 中“已启用”也不算。三层证据都成立，只表示**模型通道的最小连通性已经通过**。下一篇还会用一次真实文件任务，验证读取、写入和结果回流；那时才能判断这套兼容接入的基础 Agent 工具链是否可用。

如果三步中某一步失败，就停在那一层描述现象：是 `claude` 命令不存在，还是 `/status` 配置不符，还是发送消息后请求报错。把这三种问题分开，内部支持人员才能判断该检查安装、配置还是模型通道，而不是从头把所有软件重装一遍。

---

上一篇：[Agent 入门 02｜Claude Code 这一套，到底谁在哪里运行](/posts/agent-basics-02-claude-code-stack/) ｜ 下一篇：[Agent 入门 04｜第一个真实任务](/posts/agent-basics-04-first-task/)
