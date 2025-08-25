# Prompts Space - Hugo博客系统

这是一个基于Hugo的博客系统，用于记录和收集各种AI Prompts。

## 项目结构

```
.
├── .github/
│   └── workflows/
│       └── hugo-deploy.yml    # GitHub Actions部署配置
├── content/
│   └── posts/                 # 博客文章目录
├── themes/
│   └── stack/                 # Hugo Stack主题
├── hugo.toml                  # Hugo配置文件
└── README.md                  # 项目说明
```

## 本地开发

### 前提条件

- 已安装Hugo (Extended版本)
- Git

### 启动本地服务器

```bash
# 克隆项目（包含子模块）
git clone --recursive <your-repo-url>
cd <your-repo-name>

# 启动开发服务器
hugo server --buildDrafts
```

访问 http://localhost:1313 查看网站。

### 创建新文章

```bash
# 创建新的博客文章
hugo new content/posts/your-post-name.md
```

## GitHub Pages部署

### 设置步骤

1. **更新配置**：
   - 修改 `hugo.toml` 中的 `baseURL` 为你的GitHub Pages URL
   - 格式：`https://username.github.io/repository-name/`

2. **启用GitHub Pages**：
   - 进入GitHub仓库设置
   - 找到 "Pages" 选项
   - Source选择 "GitHub Actions"

3. **推送代码**：
   ```bash
   git add .
   git commit -m "Initial commit"
   git push origin main
   ```

### 自动部署

每次推送到 `main` 分支时，GitHub Actions会自动：

1. 安装Hugo和依赖
2. 构建静态网站
3. 部署到GitHub Pages

你可以在仓库的 "Actions" 选项卡中查看部署状态。

## 主题配置

当前使用的是 [Hugo Theme Stack](https://github.com/CaiJimmy/hugo-theme-stack)，一个现代化的Hugo主题。

主要特性：
- 响应式设计
- 深色模式支持
- 搜索功能
- 评论系统支持
- 多语言支持

## 自定义配置

编辑 `hugo.toml` 文件来自定义：

- 网站标题和描述
- 侧边栏信息
- 菜单项
- 评论系统
- 其他主题特定设置

## 贡献

欢迎提交Issues和Pull Requests来改进这个博客系统！

## 许可证

本项目采用MIT许可证。