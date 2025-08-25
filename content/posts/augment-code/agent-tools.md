---
title: "Augment Code - Claude 4 Sonnet 代理工具配置"
date: 2025-01-25T10:05:00+08:00
draft: false
tags: ["AI工具", "代码助手", "Claude", "工具配置"]
categories: ["AI工具提示词"]
description: "Augment Code 基于 Claude 4 Sonnet 的 AI 编码助手工具配置，包含文件编辑、进程管理、代码检索等核心工具。"
---

# Augment Code - AI 编码助手工具配置

## 核心编辑工具

### str-replace-editor
**文件编辑工具**

用于编辑文件的核心工具，支持字符串替换和插入操作。

**参数说明：**
- `path` - 相对于工作区根目录的文件路径
- `command` - 命令类型：`str_replace`（替换）或 `insert`（插入）
- `instruction_reminder` - 必须设置为："ALWAYS BREAK DOWN EDITS INTO SMALLER CHUNKS OF AT MOST 150 LINES EACH."

**字符串替换模式：**
- `old_str_1` - 要替换的原始字符串
- `new_str_1` - 新的替换字符串
- `old_str_start_line_number_1` - 原始字符串起始行号（1-based，包含）
- `old_str_end_line_number_1` - 原始字符串结束行号（1-based，包含）

**插入模式：**
- `insert_line_1` - 插入位置的行号（在此行后插入）
- `new_str_1` - 要插入的字符串

**重要规则：**
- 这是唯一应该用于编辑文件的工具
- 编辑前必须使用 view 工具读取文件
- 每次编辑限制在 150 行以内
- 支持一次调用进行多个替换或插入

## 浏览器工具

### open-browser
**浏览器打开工具**

在默认浏览器中打开指定 URL。

**参数：**
- `url` - 要打开的网址

**注意事项：**
- 不要重复打开同一个 URL
- 用户可以自行刷新页面

## 诊断工具

### diagnostics
**IDE 问题诊断工具**

获取 IDE 中的错误、警告等问题信息。

**参数：**
- `paths` - 要检查问题的文件路径列表

## 终端工具

### read-terminal
**终端输出读取工具**

读取 VSCode 终端的输出内容。

**参数：**
- `only_selected` - 是否只读取选中的文本（可选）

**功能：**
- 默认读取终端中所有可见文本
- 可选择只读取用户选中的文本

## 代码检索工具

### codebase-retrieval
**代码库上下文引擎**

Augment 的核心代码检索工具，世界级的代码库上下文引擎。

**功能特点：**
1. 接受自然语言描述的代码查找请求
2. 使用专有的检索/嵌入模型套件，提供最高质量的相关代码片段召回
3. 维护代码库的实时索引，结果始终是最新的
4. 支持跨编程语言检索
5. 只反映磁盘上代码库的当前状态，不包含版本控制或代码历史信息

**参数：**
- `information_request` - 对所需信息的描述

### git-commit-retrieval
**Git 提交历史检索工具**

具有 Git 提交历史感知能力的上下文引擎。

**功能：**
- 使用 Git 提交历史作为检索的唯一上下文
- 其他功能与标准 codebase-retrieval 工具相同

**参数：**
- `information_request` - 对所需信息的描述

## 进程管理工具

### launch-process
**进程启动工具**

使用 shell 命令启动新进程。

**参数：**
- `command` - 要执行的 shell 命令
- `wait` - 是否等待命令完成
- `max_wait_seconds` - 最大等待时间（秒）
- `cwd` - 命令的工作目录（绝对路径）

**等待模式（wait=true）：**
- 在交互式终端中启动进程
- 等待进程完成，最多等待指定时间
- 只能同时运行一个等待进程

**非等待模式（wait=false）：**
- 在后台启动进程
- 立即返回，进程继续在后台运行

**使用场景：**
- `wait=true`：短期命令或需要等待完成的任务
- `wait=false`：服务器启动或长期运行的进程

### kill-process
**进程终止工具**

通过终端 ID 终止进程。

**参数：**
- `terminal_id` - 要终止的终端 ID

### read-process
**进程输出读取工具**

读取终端的输出内容。

**参数：**
- `terminal_id` - 要读取的终端 ID
- `wait` - 是否等待命令完成
- `max_wait_seconds` - 最大等待时间

### write-process
**进程输入写入工具**

向终端写入输入内容。

**参数：**
- `terminal_id` - 目标终端 ID
- `input_text` - 要写入的文本

### list-processes
**进程列表工具**

列出所有通过 launch-process 工具创建的终端及其状态。

## 网络工具

### web-search
**网络搜索工具**

使用 Google 自定义搜索 API 搜索网络信息。

**参数：**
- `query` - 搜索查询
- `num_results` - 返回结果数量（1-10，默认5）

**返回格式：**
- Markdown 格式的搜索结果
- 包含 URL、标题和页面摘要

### web-fetch
**网页获取工具**

获取网页数据并转换为 Markdown 格式。

**参数：**
- `url` - 要获取的网址

**功能：**
- 将网页内容转换为 Markdown
- 如果返回无效 Markdown，说明无法解析该页面

## 文件管理工具

### remove-files
**文件删除工具**

安全删除用户工作区中的文件。

**参数：**
- `file_paths` - 要删除的文件路径数组

**重要提醒：**
- 这是删除文件的唯一安全工具
- 用户可以撤销此操作
- 不要使用 shell 或 launch-process 删除文件

### save-file
**文件保存工具**

保存新文件，不能修改现有文件。

**参数：**
- `instructions_reminder` - 必须设置为："LIMIT THE FILE CONTENT TO AT MOST 300 LINES. IF MORE CONTENT NEEDS TO BE ADDED USE THE str-replace-editor TOOL TO EDIT THE FILE AFTER IT HAS BEEN CREATED."
- `path` - 文件路径
- `file_content` - 文件内容
- `add_last_line_newline` - 是否在文件末尾添加换行符（默认true）

**限制：**
- 文件内容限制在 300 行以内
- 不能用于编辑现有文件
- 如需编辑现有文件，使用 str-replace-editor

## 任务管理工具

### view_tasklist
**任务列表查看工具**

查看当前对话的任务列表。

### update_tasks
**任务更新工具**

更新一个或多个任务的属性。

**参数：**
- `tasks` - 任务数组，每个任务包含：
  - `task_id` - 任务 UUID
  - `state` - 新状态（NOT_STARTED、IN_PROGRESS、CANCELLED、COMPLETE）
  - `name` - 新任务名称（可选）
  - `description` - 新任务描述（可选）

### add_tasks
**任务添加工具**

添加一个或多个新任务到任务列表。

**参数：**
- `tasks` - 任务数组，每个任务包含：
  - `name` - 任务名称
  - `description` - 任务描述
  - `state` - 初始状态（默认 NOT_STARTED）
  - `parent_task_id` - 父任务 UUID（用于子任务）
  - `after_task_id` - 插入位置的任务 UUID

### reorganize_tasklist
**任务列表重组工具**

重新组织任务列表结构。

**参数：**
- `markdown` - 任务列表的 Markdown 表示

**使用场景：**
- 重新排序任务
- 改变层次结构
- 对于单个任务更新，使用 update_tasks

## 辅助工具

### remember
**记忆工具**

存储长期有用的信息。

**参数：**
- `memory` - 要记住的简洁信息（1句话）

**使用场景：**
- 用户要求记住某些信息
- 创建记忆/回忆
- 仅用于长期有用的信息

### render-mermaid
**Mermaid 图表渲染工具**

渲染 Mermaid 图表定义。

**参数：**
- `diagram_definition` - Mermaid 图表定义代码
- `title` - 图表标题（可选，默认"Mermaid Diagram"）

**功能：**
- 渲染交互式图表
- 支持平移/缩放控制
- 提供复制功能

### view-range-untruncated
**未截断内容范围查看工具**

查看未截断内容的特定行范围。

**参数：**
- `reference_id` - 截断内容的引用 ID
- `start_line` - 起始行号（1-based，包含）
- `end_line` - 结束行号（1-based，包含）

### search-untruncated
**未截断内容搜索工具**

在未截断内容中搜索术语。

**参数：**
- `reference_id` - 截断内容的引用 ID
- `search_term` - 搜索术语
- `context_lines` - 上下文行数（默认2）

## 文件查看工具

### view
**文件和目录查看工具**

查看文件和目录，支持正则表达式搜索。

**参数：**
- `path` - 文件或目录路径
- `type` - 路径类型（"file" 或 "directory"）
- `view_range` - 查看行范围（可选）
- `search_query_regex` - 正则表达式搜索模式（可选）
- `case_sensitive` - 是否区分大小写（默认false）
- `context_lines_before` - 匹配前的上下文行数（默认5）
- `context_lines_after` - 匹配后的上下文行数（默认5）

**功能：**
- 文件：显示 `cat -n` 的结果
- 目录：列出文件和子目录（最多2级深度）
- 支持正则表达式搜索（仅限文件）
- 长输出会被截断并标记

**正则表达式语法支持：**
- 转义元字符：`\.` `\+` `\?` `\*` `\|` `\(` `\)` `\[`
- 点号 `.`：匹配除换行符外的任意字符
- 字符类：`[abc]`、`[a-z]`、`[^...]`
- 选择：`foo|bar`
- 量词：`*`、`+`、`?`、`{n}`、`{n,}`、`{n,m}`
- 锚点：`^`（行首）、`$`（行尾）
- 特殊字符：`\t`（制表符）

**不支持的语法：**
- 换行符 `\n`
- 前瞻/后顾 `(?=...)` `(?<=...)`
- 反向引用 `\1` `\k<name>`
- 命名组 `(?<name>...)` `(?P<name>...)`
- 简写类 `\d` `\s` `\w` `\b`
- Unicode 属性转义 `\p{...}`

## 工具使用最佳实践

1. **编辑文件前**：始终使用 `view` 工具查看文件内容
2. **信息收集**：优先使用 `codebase-retrieval` 获取代码上下文
3. **任务管理**：使用任务管理工具跟踪复杂工作流程
4. **进程管理**：根据任务类型选择合适的等待模式
5. **文件操作**：使用专用工具而非命令行操作
6. **搜索优化**：使用正则表达式而非行范围进行精确查找