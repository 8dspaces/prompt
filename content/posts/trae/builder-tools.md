---
title: "Trae AI Builder 工具配置"
date: 2025-01-15T10:30:00+08:00
draft: false
categories: ["AI工具", "IDE", "工具配置"]
tags: ["Trae", "AI编程助手", "工具配置", "API接口"]
summary: "Trae AI Builder的完整工具配置文档，包含所有可用工具的详细参数说明和使用方法，涵盖代码搜索、文件操作、命令执行等功能。"
author: "AI工具收集"
---

# Trae AI Builder 工具配置

本文档详细介绍了Trae AI Builder中所有可用工具的配置和参数说明。

## 任务管理工具

### todo_write - 任务列表管理

**描述：** 用于创建和管理当前编程会话的结构化任务列表。这有助于跟踪进度、组织复杂任务并向用户展示全面性。它还帮助用户了解任务进度和请求的整体进展。

**参数：**
```json
{
  "type": "object",
  "properties": {
    "todos": {
      "description": "更新的任务列表",
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "content": {"type": "string"},
          "status": {"type": "string", "enum": ["pending", "in_progress", "completed"]},
          "id": {"type": "string"},
          "priority": {"type": "string", "enum": ["high", "medium", "low"]}
        },
        "required": ["content", "status", "id", "priority"],
        "minItems": 3,
        "maxItems": 10
      }
    }
  },
  "required": ["todos"]
}
```

## 代码搜索工具

### search_codebase - 代码库搜索

**描述：** 这是Trae的上下文引擎。它具有以下功能：
1. 接受对要查找代码的自然语言描述
2. 使用专有的检索/嵌入模型套件，从整个代码库中产生最高质量的相关代码片段召回
3. 维护代码库的实时索引，因此结果始终是最新的，反映代码库的当前状态
4. 可以跨不同编程语言检索
5. 仅反映磁盘上代码库的当前状态，没有版本控制或代码历史信息

**参数：**
```json
{
  "type": "object",
  "properties": {
    "information_request": {"type": "string"},
    "target_directories": {"type": "array", "items": {"type": "string"}}
  },
  "required": ["information_request"]
}
```

### search_by_regex - 正则表达式搜索

**描述：** 快速的基于文本的搜索，在文件或目录中查找精确的模式匹配，利用ripgrep命令进行高效搜索。

**参数：**
```json
{
  "type": "object",
  "properties": {
    "query": {"type": "string"},
    "search_directory": {"type": "string"}
  },
  "required": ["query"]
}
```

## 文件操作工具

### view_files - 文件查看

**描述：** 以批处理模式同时查看最多3个文件，用于更快的信息收集。

**参数：**
```json
{
  "type": "object",
  "properties": {
    "files": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "file_path": {"type": "string"},
          "start_line_one_indexed": {"type": "integer"},
          "end_line_one_indexed_inclusive": {"type": "integer"},
          "read_entire_file": {"type": "boolean"}
        },
        "required": ["file_path", "start_line_one_indexed", "end_line_one_indexed_inclusive"]
      }
    }
  },
  "required": ["files"]
}
```

### list_dir - 目录列表

**描述：** 用于查看指定目录的文件。

**参数：**
```json
{
  "type": "object",
  "properties": {
    "dir_path": {"type": "string"},
    "max_depth": {"type": "integer", "default": 3}
  },
  "required": ["dir_path"]
}
```

### write_to_file - 文件写入

**描述：** 用于向文件写入内容，可以精确控制创建/重写行为。

**参数：**
```json
{
  "type": "object",
  "properties": {
    "rewrite": {"type": "boolean"},
    "file_path": {"type": "string"},
    "content": {"type": "string"}
  },
  "required": ["rewrite", "file_path", "content"]
}
```

## 文件编辑工具

### update_file - 文件更新

**描述：** 用于编辑文件，如果你认为使用此工具比其他可用的编辑工具更具成本效益，你应该选择此工具，否则应该选择其他可用的编辑工具。

**参数：**
```json
{
  "type": "object",
  "properties": {
    "file_path": {"type": "string"},
    "replace_blocks": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "old_str": {"type": "string"},
          "new_str": {"type": "string"}
        },
        "required": ["old_str", "new_str"]
      }
    }
  },
  "required": ["file_path", "replace_blocks"]
}
```

### edit_file_fast_apply - 快速文件编辑

**描述：** 用于编辑少于1000行代码的现有文件，需要遵循特定规则。

**参数：**
```json
{
  "type": "object",
  "properties": {
    "file_path": {"type": "string"},
    "content": {"type": "string"},
    "instruction": {"type": "string", "default": ""},
    "code_language": {"type": "string"}
  },
  "required": ["file_path", "content"]
}
```

### rename_file - 文件重命名

**描述：** 用于移动或重命名现有文件。

**参数：**
```json
{
  "type": "object",
  "properties": {
    "file_path": {"type": "string"},
    "rename_file_path": {"type": "string"}
  },
  "required": ["file_path", "rename_file_path"]
}
```

### delete_file - 文件删除

**描述：** 用于删除文件，可以在一次调用中删除多个文件，必须确保文件在删除前存在。

**参数：**
```json
{
  "type": "object",
  "properties": {
    "file_paths": {"type": "array", "items": {"type": "string"}}
  },
  "required": ["file_paths"]
}
```

## 命令执行工具

### run_command - 命令执行

**描述：** 用于代表用户提议运行命令。

**参数：**
```json
{
  "type": "object",
  "properties": {
    "command": {"type": "string"},
    "target_terminal": {"type": "string"},
    "command_type": {"type": "string"},
    "cwd": {"type": "string"},
    "blocking": {"type": "boolean"},
    "wait_ms_before_async": {"type": "integer", "minimum": 0},
    "requires_approval": {"type": "boolean"}
  },
  "required": ["command", "blocking", "requires_approval"]
}
```

### check_command_status - 命令状态检查

**描述：** 用于通过命令ID获取先前执行的命令状态（非阻塞命令）。

**参数：**
```json
{
  "type": "object",
  "properties": {
    "command_id": {"type": "string"},
    "wait_ms_before_check": {"type": "integer"},
    "output_character_count": {"type": "integer", "minimum": 0, "default": 1000},
    "skip_character_count": {"type": "integer", "minimum": 0, "default": 0},
    "output_priority": {"type": "string", "default": "bottom"}
  }
}
```

### stop_command - 命令停止

**描述：** 此工具允许你终止当前正在运行的命令（命令必须是先前执行的命令）。

**参数：**
```json
{
  "type": "object",
  "properties": {
    "command_id": {"type": "string"}
  },
  "required": ["command_id"]
}
```

## 预览和搜索工具

### open_preview - 打开预览

**描述：** 如果你在之前的工具调用中成功启动了本地服务器，可以使用此工具向用户显示可用的预览URL，用户可以在浏览器中打开它。

**参数：**
```json
{
  "type": "object",
  "properties": {
    "preview_url": {"type": "string"},
    "command_id": {"type": "string"}
  },
  "required": ["preview_url", "command_id"]
}
```

### web_search - 网络搜索

**描述：** 此工具可用于搜索互联网，应谨慎使用，因为频繁搜索会导致糟糕的用户体验和过高的成本。

**参数：**
```json
{
  "type": "object",
  "properties": {
    "query": {"type": "string"},
    "num": {"type": "int32", "default": 5},
    "lr": {"type": "string"}
  },
  "required": ["query"]
}
```

## 会话控制工具

### finish - 完成会话

**描述：** 此会话的最终工具，当你认为已经达到用户需求的目标时，应该使用此工具标记为完成。

**参数：**
```json
{
  "type": "object",
  "properties": {
    "summary": {"type": "string"}
  },
  "required": ["summary"]
}
```

---

## 使用说明

这些工具为Trae AI Builder提供了强大的功能集合，包括：

- **代码理解**：通过智能搜索和上下文分析理解代码库
- **文件操作**：完整的文件读写、编辑和管理功能
- **命令执行**：安全的命令行操作和状态监控
- **开发辅助**：预览、搜索和任务管理功能

每个工具都有详细的参数规范，确保AI助手能够准确执行用户的编程任务。

---

*本文档翻译自Trae AI Builder的官方工具配置文件，用于了解该AI编程助手的完整工具集和使用方法。*