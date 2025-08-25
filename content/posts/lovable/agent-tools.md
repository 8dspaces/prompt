---
title: "Lovable AI 代理工具配置"
date: 2024-01-20T10:00:00+08:00
draft: false
tags: ["Lovable", "AI工具", "工具配置", "API规范"]
categories: ["AI工具", "工具配置"]
author: "AI提示词收集"
description: "Lovable AI 代理的完整工具配置文档，包含所有可用工具的参数规范和使用说明。"
---

# Lovable AI 代理工具配置

## 依赖管理工具

### lov-add-dependency
**功能**: 向项目添加依赖包

**描述**: 使用此工具向项目添加依赖。依赖应该是有效的 npm 包名。

**使用格式**: `package-name@version`

**参数**:
- `package` (必需): 包名和版本
  - 类型: string
  - 示例: "lodash@latest"

### lov-remove-dependency
**功能**: 从项目中卸载包

**参数**:
- `package` (必需): 要卸载的包名
  - 类型: string
  - 示例: "lodash"

## 文件搜索工具

### lov-search-files
**功能**: 基于正则表达式的代码搜索，支持文件过滤和上下文

**描述**: 使用正则表达式模式在项目文件中进行搜索。

**参数**:
- `query` (必需): 要查找的正则表达式模式
  - 类型: string
  - 示例: "useEffect\\("
- `include_pattern` (必需): 使用 glob 语法包含的文件
  - 类型: string
  - 示例: "src/"
- `exclude_pattern` (可选): 使用 glob 语法排除的文件
  - 类型: string
  - 示例: "/*.test.tsx"
- `case_sensitive` (可选): 是否区分大小写
  - 类型: boolean
  - 默认值: false

**提示**: 使用 `\\` 转义正则表达式中的特殊字符。

## 文件操作工具

### lov-write
**功能**: 写入文件内容

**描述**: 用于写入文件。如果文件存在则覆盖。文件路径应相对于项目根目录。

**重要说明**:
- **最小化代码写入**: 优先使用 `lov-line-replace` 进行大多数更改，而不是重写整个文件
- **主要用途**: 创建新文件或作为 `lov-line-replace` 失败时的备选方案
- **保持现有代码**: 使用 `// ... keep existing code` 注释保持未修改的部分
- **仅写入必要部分**: 只写入需要更改的特定部分

**使用 "keep existing code" 的规则**:
- 超过 5 行的未更改代码块必须使用 `// ... keep existing code` 注释
- 注释必须包含确切的字符串 "... keep existing code"
- 示例: `// ... keep existing code (user interface components)`
- 绝不重写不需要更改的大段代码

**并行工具使用**:
- 如需创建多个文件，必须同时创建所有文件，而不是逐个创建

**参数**:
- `file_path` (必需): 文件路径（相对于项目根目录）
  - 类型: string
  - 示例: "src/main.ts"
- `content` (必需): 完整的文件内容
  - 类型: string
  - 示例: "console.log('Hello, World!')"

### lov-line-replace
**功能**: 基于行号的搜索和替换工具

**描述**: 这是编辑现有文件的**首选和主要工具**。使用明确的行号查找和替换文件中的特定内容。

**严格输出限制**:
- 总输出（包括所有字段和 replace_blocks）不得超过 5000 字符
- 单次输出中的 replace_blocks 数量不得超过 10 个
- 如果所需修改无法在这些限制内完成，只包含能容纳的 replace_blocks，其余修改可通过另一次工具调用完成

**参数**:
- `file_path` (必需): 要修改的文件路径
  - 类型: string
  - 示例: "src/components/TaskList.tsx"
- `search` (必需): 要搜索的内容（不包含行号）
  - 类型: string
  - 描述: 应匹配将被替换的现有代码
- `first_replaced_line` (必需): 第一行的行号（从1开始）
  - 类型: number
  - 示例: 15
- `last_replaced_line` (必需): 最后一行的行号（从1开始）
  - 类型: number
  - 示例: 28
- `replace` (必需): 用于替换搜索内容的新内容（不包含行号）
  - 类型: string

**省略号使用**:
当替换超过约 6 行的代码段时，应在搜索中使用省略号 (...) 来减少需要指定的行数：
- 包含要替换部分的前几行（通常 2-3 行）
- 在单独一行添加 "..." 表示省略的内容
- 包含要替换部分的后几行（通常 2-3 行）
- 关键是在开头和结尾提供足够的唯一上下文以确保准确匹配

**关键指南**:
1. **行号**: 指定确切的 first_replaced_line 和 last_replaced_line（从1开始）
2. **省略号使用**: 对于大段代码（>6行），使用省略号只包含前几行和后几行的关键标识行
3. **内容验证**: 搜索的前缀和后缀部分必须与文件中的实际内容完全匹配
4. **文件验证**: 文件必须存在且可读
5. **并行工具调用**: 需要多次编辑时，同时调用必要的工具
6. **原始行号**: 对同一文件进行多次编辑时，始终使用初始查看文件时的原始行号

### lov-view
**功能**: 读取文件内容

**描述**: 用于读取文件内容。文件路径应相对于项目根目录。可选择指定行范围。

**重要指南**:
- 如果文件内容已在 `<useful-context>` 中提供，请勿使用此工具
- 除非文件很大（>500行），否则不要指定行范围 - 依赖默认行为显示前500行
- 只有在需要查看默认视图中未显示的大文件特定部分时才使用行范围
- 如需读取多个文件，并行调用此工具多次（而非顺序调用）以提高效率

**参数**:
- `file_path` (必需): 文件路径
  - 类型: string
  - 示例: "src/App.tsx"
- `lines` (可选): 要读取的行范围
  - 类型: string
  - 示例: "1-800, 1001-1500"
  - 默认: 如未指定则读取前500行

### lov-rename
**功能**: 重命名文件

**描述**: 必须使用此工具重命名文件，而不是创建新文件并删除旧文件。

**参数**:
- `original_file_path` (必需): 原始文件路径
  - 类型: string
  - 示例: "src/main.ts"
- `new_file_path` (必需): 新文件路径
  - 类型: string
  - 示例: "src/main_new2.ts"

### lov-delete
**功能**: 删除文件

**参数**:
- `file_path` (必需): 要删除的文件路径
  - 类型: string
  - 示例: "src/App.tsx"

## 资源下载工具

### lov-download-to-repo
**功能**: 从URL下载文件并保存到仓库

**描述**: 用于下载图片、资源或其他文件。将图片下载到 src/assets 文件夹并作为 ES6 模块导入。

**用途**:
- 从URL下载图片、资源或其他文件
- 直接将外部资源保存到项目中
- 从外部源迁移文件到仓库

**参数**:
- `source_url` (必需): 要下载的文件URL
  - 类型: string
  - 示例: "https://example.com/image.png"
- `target_path` (必需): 文件在仓库中的保存路径
  - 类型: string
  - 示例: "public/images/logo.png"
  - 建议: 除非另有说明，否则使用 public 文件夹

### lov-fetch-website
**功能**: 获取网站内容

**描述**: 获取网站并临时将其内容（markdown、HTML、截图）保存到 `tmp://fetched-websites/` 中的文件。返回创建的文件路径和内容预览。

**参数**:
- `url` (必需): 网站URL
  - 类型: string
  - 示例: "https://example.com"
- `formats` (可选): 逗号分隔的格式列表
  - 类型: string
  - 支持格式: 'markdown', 'html', 'screenshot'
  - 默认值: 'markdown'
  - 示例: "markdown,screenshot"

## 调试工具

### lov-read-console-logs
**功能**: 读取最新的控制台日志

**描述**: 用于读取用户发送请求时的最新控制台日志内容。可选择提供搜索查询来过滤日志。

**重要说明**:
- 可能无法看到最近没有发生的日志
- 在构建和编写代码时日志不会更新
- 不要期望通过再次读取日志来验证是否修复了问题
- 不要对同一 command_id 使用超过一次，因为每次都会得到相同的日志

**参数**:
- `search` (必需): 搜索查询
  - 类型: string
  - 示例: "error"
  - 如果为空则获取所有最新日志

### lov-read-network-requests
**功能**: 读取最新的网络请求

**描述**: 用于读取最新的网络请求内容。可选择提供搜索查询来过滤请求。

**参数**:
- `search` (必需): 搜索查询
  - 类型: string
  - 示例: "error"
  - 如果为空则获取所有最新请求

## 图像生成工具

### generate_image
**功能**: 基于文本提示生成图像

**描述**: 根据文本提示生成图像并保存到指定文件路径。考虑页面上图像位置选择合适的宽高比。

**模型选择**:
- **小图像**（小于1000px）: 使用 `flux.schnell`，速度更快且质量很好，应作为默认模型
- **大图像**（如全屏图像）: 使用 `flux.dev`，最大分辨率 1920x1920

**提示技巧**:
- 在提示中提及宽高比有助于模型生成正确尺寸的图像
- 使用 "Ultra high resolution" 后缀最大化图像质量
- 如果生成英雄图像，在提示中提及

**参数**:
- `prompt` (必需): 图像的文本描述
  - 类型: string
- `target_path` (必需): 生成图像的保存路径
  - 类型: string
  - 建议: 放在 'src/assets' 文件夹中
- `width` (可选): 图像宽度
  - 类型: number
  - 范围: 512-1920像素，必须是32的倍数
- `height` (可选): 图像高度
  - 类型: number
  - 范围: 512-1920像素，必须是32的倍数
- `model` (可选): 生成模型
  - 类型: string
  - 选项: flux.schnell（默认）, flux.dev
  - 说明: flux.dev 生成更高质量图像但速度较慢

**使用示例**:
```javascript
import heroImage from "@/assets/hero-image.jpg";
```

### edit_image
**功能**: 编辑或合并现有图像

**描述**: 使用 Flux Kontext Pro 模型基于文本提示编辑或合并现有图像。

**工作模式**:
- **单图像**: 基于提示应用AI驱动的编辑
- **多图像**: 根据提示合并/组合图像

**强度参数**: 控制图像变化程度（0.0-1.0），较低值保留更多原始图像结构。

**单图像提示示例**:
- "make it rainy"
- "change to sunset lighting"
- "add snow"
- "make it more colorful"

**多图像提示示例**:
- "blend these two landscapes seamlessly"
- "combine the foreground of the first image with the background of the second"
- "merge these portraits into a group photo"
- "create a collage from these images"

**参数**:
- `image_paths` (必需): 现有图像文件路径数组
  - 类型: array of strings
  - 说明: 单图像编辑提供一个路径，多图像合并提供多个路径
- `prompt` (必需): 如何编辑/合并图像的文本描述
  - 类型: string
- `target_path` (必需): 编辑/合并后图像的保存路径
  - 类型: string
- `strength` (可选): 图像变化程度
  - 类型: number
  - 范围: 0.0-1.0

## 搜索工具

### web_search
**功能**: 执行网络搜索

**描述**: 执行网络搜索并返回相关结果和文本内容。用于查找当前信息、文档或任何基于网络的内容。

**使用场景**:
- 当你没有用户询问内容的任何信息时
- 需要查找当前信息、文档或任何基于网络的内容时
- 需要查找特定技术信息等时
- 需要查找特定人员、公司或组织信息时
- 需要查找特定事件、产品或服务信息时
- 需要查找特定人员、公司或组织的真实（非AI生成）图像时

**有效类别**（必须使用确切字符串）:
- "news"
- "linkedin profile"
- "pdf"
- "github"
- "personal site"
- "financial report"

**参数**:
- `query` (必需): 搜索查询
  - 类型: string
- `numResults` (可选): 返回的搜索结果数量
  - 类型: number
  - 默认值: 5
- `category` (可选): 搜索结果类别
  - 类型: string
- `links` (可选): 每个结果返回的链接数量
  - 类型: number
- `imageLinks` (可选): 每个结果返回的图像链接数量
  - 类型: number

## 分析工具

### read_project_analytics
**功能**: 读取项目分析数据

**描述**: 读取项目生产构建在两个日期之间的分析数据，具有给定的粒度。

**使用场景**:
- 用户询问其应用的使用情况时
- 用户想要改进其生产应用时

**参数**:
- `startdate` (必需): 开始日期
  - 类型: object
  - 格式: RFC3339 格式或仅日期格式（YYYY-MM-DD）
- `enddate` (必需): 结束日期
  - 类型: object
  - 格式: RFC3339 格式或仅日期格式（YYYY-MM-DD）
- `granularity` (必需): 数据粒度
  - 类型: string
  - 选项: 'hourly' 或 'daily'

## 总结

Lovable AI 代理工具集提供了完整的 Web 开发工作流支持，从文件操作、代码搜索到图像生成和项目分析。这些工具设计为高效、并行使用，以最大化开发效率和代码质量。