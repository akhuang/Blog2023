---
title: "asciinema 使用指南"
date: 2023-12-09T11:31:09+08:00
description: "asciinema 基本操作及搭配 tmux 录制多个屏幕"
tags: [工具]
featured_image: ""
# images is optional, but needed for showing Twitter Card
images: []
categories:
comment: true
draft: false
---

### 安装
```bash
# debian
sudo apt-get install asciinema

# mac
brew install asciinema

```

See other [installation options](https://asciinema.org/docs/installation#installing-on-linux)

### 基本使用
```bash
# 开始录制
asciinema rec

# 结束录制 
exit 
# 或者 ctrl+d

# 播放录制
asciinema play /path/to/asciicast.cast


### 多屏
```bash

# debian
apt install tmux
# mac
brew install tmux

# 开始录制
asciinema rec 
tmux
```

### tmux 基本操作
创建新窗口: `ctrl+b c`  
切换窗口: `ctrl+b 0-9`  
分屏: `ctrl+b %` 或 `ctrl+b "`  
切换分屏: `ctrl+b 方向键`   
关闭分屏: `ctrl+b x`    
attach: `tmux attach-session -t 0`  
ls: `tmux ls`   
kill: `tmux kill-session -t 0`  


