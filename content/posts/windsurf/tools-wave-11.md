---
title: "Windsurf Cascade AI 工具配置 (Wave 11)"
date: 2025-01-25T11:00:00+08:00
draft: false
categories: ["AI工具", "工具配置"]
tags: ["Windsurf", "Cascade", "工具集", "开发工具", "浏览器集成"]
summary: "Windsurf Cascade AI 助手的完整工具配置文档 (Wave 11)，包含浏览器集成、代码搜索、文件操作、部署管理等核心功能。"
author: "AI翻译助手"
---

# Windsurf Cascade AI 工具配置 (Wave 11)

本文档详细描述了 Windsurf Cascade AI 助手的完整工具配置，涵盖浏览器集成、代码操作、部署管理等各种核心功能。

## 浏览器集成工具

### 浏览器预览 (browser_preview)
为Web服务器启动浏览器预览。允许用户正常与Web服务器交互，并向Cascade提供控制台日志和其他信息。

**参数：**
- `Name`: Web服务器的简短名称（3-5个词，标题格式）
- `Url`: 目标Web服务器的URL（包含协议、域名和端口）
- `toolSummary`: 工具操作的简要摘要

### 捕获浏览器控制台日志 (capture_browser_console_logs)
检索已在Windsurf浏览器中打开的页面的控制台日志。

**参数：**
- `PageId`: 要捕获控制台日志的浏览器页面ID
- `toolSummary`: 工具操作的简要摘要

### 捕获浏览器截图 (capture_browser_screenshot)
捕获已在Windsurf浏览器中打开的页面当前视口的截图。

**参数：**
- `PageId`: 要捕获截图的浏览器页面ID
- `toolSummary`: 工具操作的简要摘要

### 获取DOM树 (get_dom_tree)
获取Windsurf浏览器中打开页面的DOM树。

**参数：**
- `PageId`: 要获取DOM树的浏览器页面ID
- `toolSummary`: 工具操作的简要摘要

### 列出浏览器页面 (list_browser_pages)
列出Windsurf浏览器中所有打开的页面及其元数据（页面ID、URL、标题、视口大小等）。

### 打开浏览器URL (open_browser_url)
在Windsurf浏览器中打开URL以查看页面的渲染内容。

**参数：**
- `Url`: 要在浏览器中打开的URL
- `toolSummary`: 工具操作的简要摘要

### 读取浏览器页面 (read_browser_page)
读取Windsurf浏览器中打开的页面内容。

**参数：**
- `PageId`: 要读取的浏览器页面ID
- `toolSummary`: 工具操作的简要摘要

## 代码搜索和分析工具

### 代码库搜索 (codebase_search)
从代码库中查找与搜索查询最相关的代码片段。当搜索查询更精确且与代码的功能或目的相关时，效果最佳。

**参数：**
- `Query`: 搜索查询
- `TargetDirectories`: 要搜索的目录的绝对路径列表
- `toolSummary`: 工具操作的简要摘要

**注意：**
- 如果尝试搜索超过500个文件，搜索结果质量会显著下降
- 只显示顶级项目的完整代码内容，其他项目只显示文档字符串和签名
- 使用 `view_code_item` 查看任何项目的完整代码内容

### 按名称查找 (find_by_name)
使用fd在指定目录中搜索文件和子目录。

**参数：**
- `SearchDirectory`: 要搜索的目录
- `Pattern`: 搜索模式（支持glob格式）
- `Extensions`: 要包含的文件扩展名
- `Excludes`: 要排除的glob模式
- `Type`: 类型过滤器（file/directory/any）
- `MaxDepth`: 最大搜索深度
- `FullPath`: 是否全路径匹配
- `toolSummary`: 工具操作的简要摘要

**特性：**
- 使用智能大小写匹配
- 默认忽略git忽略的文件
- 结果限制为50个匹配项
- 包含类型、大小、修改时间和相对路径

### Grep搜索 (grep_search)
使用ripgrep在文件或目录中查找精确的模式匹配。

**参数：**
- `Query`: 要查找的搜索词或模式
- `SearchPath`: 要搜索的路径（目录或文件）
- `IsRegex`: 是否将查询视为正则表达式
- `CaseInsensitive`: 是否执行不区分大小写的搜索
- `MatchPerLine`: 是否返回每个匹配行
- `Includes`: 用于过滤文件的Glob模式
- `toolSummary`: 工具操作的简要摘要

**特性：**
- 返回JSON格式结果
- 每个匹配包含文件名、行号、行内容
- 总结果限制为50个匹配项

## 文件操作工具

### 查看文件 (view_file)
查看文件的内容。文件行从1开始索引，输出包含从StartLine到EndLine的文件内容。

**参数：**
- `AbsolutePath`: 要查看的文件的绝对路径
- `StartLine`: 开始行（1索引）
- `EndLine`: 结束行（1索引，包含）
- `IncludeSummaryOfOtherLines`: 是否包含其他行的摘要
- `toolSummary`: 工具操作的简要摘要

**注意：**
- 一次最多可查看400行
- 确保获得完整上下文是你的责任
- 如果内容不足，主动再次调用工具查看更多行

### 查看代码项 (view_code_item)
查看文件中最多5个代码项节点的内容，每个作为类或函数。

**参数：**
- `File`: 要查看的节点的绝对路径
- `NodePaths`: 文件中节点的路径（如package.class.FunctionName）
- `toolSummary`: 工具操作的简要摘要

**注意：**
- 必须使用完全限定的代码项名称
- 如果符号未找到，返回空字符串

### 替换文件内容 (replace_file_content)
编辑现有文件的内容。

**参数：**
- `TargetFile`: 要修改的目标文件（必须首先指定）
- `ReplacementChunks`: 要替换的块列表
- `Instruction`: 对文件所做更改的描述
- `CodeMarkdownLanguage`: 代码块的Markdown语言
- `TargetLintErrorIds`: 此编辑旨在修复的lint错误ID
- `toolSummary`: 工具操作的简要摘要

**规则：**
1. 不要对同一文件进行多个并行调用
2. 对于同一文件中的多个非相邻行编辑，进行单次调用
3. 每个ReplacementChunk指定TargetContent和ReplacementContent
4. TargetContent必须与现有文件内容完全匹配
5. 不能编辑.ipynb文件扩展名

### 写入文件 (write_to_file)
创建新文件。如果文件和任何父目录不存在，将为你创建。

**参数：**
- `TargetFile`: 要创建和写入代码的目标文件（必须第二个指定）
- `CodeContent`: 要写入文件的代码内容
- `EmptyFile`: 设置为true以创建空文件
- `toolSummary`: 工具操作的简要摘要（必须首先指定）

**重要：**
- 绝不要使用此工具修改或覆盖现有文件
- 调用此工具前始终确认TargetFile不存在

### 列出目录 (list_dir)
列出目录的内容。

**参数：**
- `DirectoryPath`: 要列出内容的路径（应为存在的目录的绝对路径）
- `toolSummary`: 工具操作的简要摘要

**输出：**
- 相对于目录的路径
- 是否为目录或文件
- 文件的字节大小
- 目录的子项数量（递归）

## 命令执行工具

### 运行命令 (run_command)
代表用户提议运行命令。

**参数：**
- `CommandLine`: 要执行的确切命令行字符串
- `Cwd`: 命令的当前工作目录
- `Blocking`: 命令是否阻塞直到完全完成
- `SafeToAutoRun`: 命令是否安全，可在没有用户批准的情况下运行
- `WaitMsBeforeAsync`: 仅当Blocking为false时适用的等待毫秒数
- `toolSummary`: 工具操作的简要摘要

**重要规则：**
- **绝不要提议cd命令**
- 用户必须批准命令才能执行
- 只有在极其确信安全时才设置SafeToAutoRun为true
- 操作系统：Windows，Shell：PowerShell

### 命令状态 (command_status)
通过ID获取先前执行的终端命令的状态。

**参数：**
- `CommandId`: 要获取状态的命令ID
- `WaitDurationSeconds`: 获取状态前等待命令完成的秒数
- `OutputCharacterCount`: 要查看的字符数
- `toolSummary`: 工具操作的简要摘要

**注意：**
- 只尝试检查后台命令ID的状态
- 返回当前状态（运行中、完成）、输出行和任何错误

### 读取终端 (read_terminal)
读取给定进程ID的终端内容。

**参数：**
- `Name`: 要读取的终端名称
- `ProcessID`: 要读取的终端的进程ID
- `toolSummary`: 工具操作的简要摘要

## 部署管理工具

### 部署Web应用 (deploy_web_app)
将JavaScript Web应用程序部署到Netlify等部署提供商。

**参数：**
- `ProjectPath`: Web应用程序的完整绝对项目路径
- `Framework`: Web应用程序的框架
- `ProjectId`: Web应用程序的项目ID（如果存在）
- `Subdomain`: URL中使用的子域或项目名称
- `toolSummary`: 工具操作的简要摘要

**支持的框架：**
- eleventy, angular, astro, create-react-app, gatsby
- gridsome, grunt, hexo, hugo, hydrogen
- jekyll, middleman, mkdocs, nextjs, nuxtjs
- remix, sveltekit, svelte

**注意：**
- 站点不需要构建，只需要源文件
- 部署前确保运行read_deployment_config工具
- 新站点留空project_id，重新部署使用现有ID

### 检查部署状态 (check_deploy_status)
使用windsurf_deployment_id检查Web应用程序的部署状态。

**参数：**
- `WindsurfDeploymentId`: 要检查状态的部署的Windsurf部署ID
- `toolSummary`: 工具操作的简要摘要

**注意：**
- 只有在用户要求时才运行
- 必须在deploy_web_app工具调用后运行

### 读取部署配置 (read_deployment_config)
读取Web应用程序的部署配置并确定应用程序是否准备好部署。

**参数：**
- `ProjectPath`: Web应用程序的完整绝对项目路径
- `toolSummary`: 工具操作的简要摘要

**注意：**
- 应仅在准备deploy_web_app工具时使用

## 网络和资源工具

### 网络搜索 (search_web)
执行网络搜索以获取给定查询的相关网络文档列表。

**参数：**
- `query`: 搜索查询
- `domain`: 可选的域名以优先搜索
- `toolSummary`: 工具操作的简要摘要

### 读取URL内容 (read_url_content)
从URL读取内容。

**参数：**
- `Url`: 要读取内容的URL
- `toolSummary`: 工具操作的简要摘要

**注意：**
- URL必须是指向有效互联网资源的HTTP或HTTPS URL

### 列出资源 (list_resources)
列出MCP服务器的可用资源。

**参数：**
- `ServerName`: 要列出可用资源的服务器名称
- `toolSummary`: 工具操作的简要摘要

### 读取资源 (read_resource)
检索指定资源的内容。

**参数：**
- `ServerName`: 要从中读取资源的服务器名称
- `Uri`: 资源的唯一标识符
- `toolSummary`: 工具操作的简要摘要

## 内存和轨迹工具

### 创建内存 (create_memory)
将与用户及其任务相关的重要上下文保存到内存数据库。

**参数：**
- `Action`: 对内存采取的操作类型（create/update/delete）
- `Content`: 新内存或更新内存的内容
- `Title`: 内存的描述性标题
- `Tags`: 与内存关联的标签
- `CorpusNames`: 与内存关联的工作区的CorpusNames
- `Id`: 要更新或删除的现有内存的ID
- `UserTriggered`: 用户是否明确要求创建/修改此内存
- `toolSummary`: 工具操作的简要摘要

**要保存的上下文示例：**
- 用户偏好
- 用户明确要求记住的内容
- 重要代码片段
- 技术栈
- 项目结构
- 主要里程碑或功能
- 新设计模式和架构决策

### 轨迹搜索 (trajectory_search)
语义搜索或检索轨迹。轨迹是对话之一。

**参数：**
- `ID`: 要搜索或检索的轨迹ID
- `Query`: 在轨迹中搜索的查询字符串
- `SearchType`: 要搜索或检索的项目类型（cascade/user）
- `toolSummary`: 工具操作的简要摘要

**注意：**
- 当用户@提及@conversation时调用此工具
- 不要使用SearchType: 'user'调用此工具
- 忽略@activity提及

## 辅助工具

### 建议响应 (suggested_responses)
如果你没有调用其他工具并向用户提问，使用此工具提供少量可能的建议答案。

**参数：**
- `Suggestions`: 建议列表（每个最多几个词，不超过3个选项）
- `toolSummary`: 工具操作的简要摘要

**使用指南：**
- 谨慎使用，只有在确信会收到建议选项之一时才使用
- 如果下一个用户输入可能是详细的短或长形式响应，则不要提出建议
- 尽量不要连续多次使用

### 查看内容块 (view_content_chunk)
使用DocumentId和块位置查看特定的文档内容块。

**参数：**
- `document_id`: 块所属的文档ID
- `position`: 要查看的块的位置
- `toolSummary`: 工具操作的简要摘要

**注意：**
- DocumentId必须已通过read_url_content或read_knowledge_base_item工具读取

## 多工具并行执行

### 并行执行 (parallel)
同时运行多个工具，但仅当它们可以并行操作时。

**参数：**
- `tool_uses`: 要并行执行的工具列表
  - `recipient_name`: 要使用的工具名称
  - `parameters`: 传递给工具的参数

**注意：**
- 即使提示建议按顺序使用工具，也要这样做
- 只允许函数工具

## 工具使用最佳实践

1. **工具摘要**：所有工具都要求首先指定`toolSummary`参数
2. **文件操作**：编辑前始终确认文件存在性
3. **命令安全**：只有在极其确信安全时才自动运行命令
4. **浏览器集成**：充分利用浏览器工具进行Web开发和测试
5. **内存管理**：主动保存重要上下文以供将来参考
6. **并行执行**：在可能的情况下使用并行工具执行提高效率

这些工具共同构成了Windsurf Cascade AI助手的强大功能集，支持从代码开发到部署管理的完整工作流程。