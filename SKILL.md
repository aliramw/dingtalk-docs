---
name: dingtalk-docs
description: 钉钉文档操作技能。使用 mcporter CLI 连接钉钉 MCP server 执行文档创建、读取、更新、删除等操作。需要配置 DINGTALK_MCP_DOCS_URL 凭证。使用场景：创建云文档、编辑文档内容、管理文档权限、文档格式转换等。
version: 0.1.0
metadata:
  openclaw:
    requires:
      env:
        - DINGTALK_MCP_DOCS_URL
      bins:
        - mcporter
    primaryEnv: DINGTALK_MCP_DOCS_URL
    homepage: https://github.com/aliramw/dingtalk-docs
---

# 钉钉文档操作

通过 MCP 协议连接钉钉文档 API，执行文档创建、读取、更新、删除等操作。

## ⚠️ 安全须知

**安装前请阅读：**

1. **本技能需要外部 CLI 工具** - 需安装 `mcporter` (npm/bun 全局安装)
2. **需要配置认证凭证** - Streamable HTTP URL 包含访问令牌，请妥善保管
3. **脚本审查建议** - `scripts/` 目录包含 Python 辅助脚本，建议先审查再运行
4. **测试环境优先** - 首次使用建议在测试文档中验证，确认无误后再操作生产数据

### 🔒 安全加固措施

脚本已实施以下安全保护：

| 保护措施 | 说明 |
|----------|------|
| **路径沙箱** | `resolve_safe_path()` 防止目录遍历攻击，限制文件访问在 `OPENCLAW_WORKSPACE` 内 |
| **UUID 验证** | 严格验证 docUuid 格式，防止无效输入 |
| **文件扩展名白名单** | 仅允许 `.md`, `.txt`, `.json` 等安全格式 |
| **文件大小限制** | 导入文件最大 10MB，防止 DoS |
| **命令超时** | mcporter 命令超时限制（60-120 秒） |
| **输入清理** | 自动去除空白、验证空值 |

**配置建议：**
```bash
# 设置工作目录限制（推荐）
export OPENCLAW_WORKSPACE=/Users/marila/.openclaw/workspace
```

## 前置要求

### 安装 mcporter CLI

本技能依赖 `mcporter` 工具。安装前请确认来源可信：

```bash
# 使用 npm 安装
npm install -g mcporter

# 或使用 bun 安装
bun install -g mcporter
```

验证安装：
```bash
mcporter --version
```

### 配置 MCP Server

**获取 Streamable HTTP URL：**

1. 访问钉钉 MCP 广场 **钉钉文档** MCP 服务：https://mcp.dingtalk.com/#/detail?mcpId=1047&detailType=marketMcpDetail
2. 点击"获取 MCP Server 配置"按钮，复制 `Streamable HTTP URL`

**方式一：使用 mcporter config（推荐）**

```bash
# 添加钉钉文档服务器配置（持久化存储）
mcporter config add dingtalk-docs --url "<Streamable_HTTP_URL>"
```

**方式二：使用环境变量**

```bash
# 临时设置（当前终端会话有效）
export DINGTALK_MCP_DOCS_URL="<Streamable_HTTP_URL>"
```

将 `<Streamable_HTTP_URL>` 替换为实际获取的完整 URL。

> **⚠️ 凭证安全**: Streamable HTTP URL 包含访问令牌，等同于密码：
> - 不要提交到版本控制系统
> - 不要分享给他人
> - 推荐使用 `mcporter config` 持久化存储

## API 方法

### 文档基础操作

| 方法 | 说明 | 输入参数 | 返回 |
|------|------|----------|------|
| `create_doc` | 创建新文档 | title, folder_id, template_id | { doc_id, url } |
| `get_doc` | 获取文档详情 | doc_id | { title, content, meta } |
| `update_doc` | 更新文档内容 | doc_id, content | { success, updated_at } |
| `delete_doc` | 删除文档 | doc_id | { success } |
| `list_docs` | 列出文档 | folder_id, limit, offset | { docs: [], total } |
| `move_doc` | 移动文档 | doc_id, target_folder_id | { success } |
| `copy_doc` | 复制文档 | doc_id, target_folder_id, new_title | { doc_id, url } |

### 文档内容操作

| 方法 | 说明 | 输入参数 | 返回 |
|------|------|----------|------|
| `get_content` | 获取文档内容 | doc_id, format | { content, format } |
| `insert_block` | 插入内容块 | doc_id, block_type, content, position | { block_id } |
| `update_block` | 更新内容块 | doc_id, block_id, content | { success } |
| `delete_block` | 删除内容块 | doc_id, block_id | { success } |
| `list_blocks` | 列出内容块 | doc_id, parent_block_id | { blocks: [] } |

### 文档权限管理

| 方法 | 说明 | 输入参数 | 返回 |
|------|------|----------|------|
| `get_permission` | 获取权限设置 | doc_id | { permissions: [] } |
| `set_permission` | 设置权限 | doc_id, user_id, role | { success } |
| `remove_permission` | 移除权限 | doc_id, user_id | { success } |
| `share_doc` | 分享文档 | doc_id, share_type, password | { share_url } |

### 文件夹操作

| 方法 | 说明 | 输入参数 | 返回 |
|------|------|----------|------|
| `create_folder` | 创建文件夹 | name, parent_id | { folder_id } |
| `list_folders` | 列出文件夹 | parent_id | { folders: [] } |
| `delete_folder` | 删除文件夹 | folder_id | { success } |

## 使用示例

### 创建文档

```bash
mcporter call dingtalk-docs create_doc \
  --title "项目计划" \
  --folder_id "fld_xxx"
```

### 获取文档内容

```bash
mcporter call dingtalk-docs get_content \
  --doc_id "doc_xxx" \
  --format "markdown"
```

### 更新文档

```bash
mcporter call dingtalk-docs update_doc \
  --doc_id "doc_xxx" \
  --content "# 更新后的内容\n\n正文..."
```

### 列出文档

```bash
mcporter call dingtalk-docs list_docs \
  --folder_id "fld_xxx" \
  --limit 20
```

### 分享文档

```bash
mcporter call dingtalk-docs share_doc \
  --doc_id "doc_xxx" \
  --share_type "anyone_with_link"
```

## 脚本工具

`scripts/` 目录提供 Python 辅助脚本：

| 脚本 | 说明 |
|------|------|
| `create_doc.py` | 批量创建文档 |
| `import_docs.py` | 从本地文件导入文档 |
| `export_docs.py` | 导出文档到本地 |
| `sync_folder.py` | 同步文件夹内容 |

使用示例：
```bash
python3 scripts/create_doc.py --title "新文档" --folder "fld_xxx"
```

## 故障排查

### 常见问题

**1. 认证失败**
```
Error: Invalid credentials
```
- 检查 DINGTALK_MCP_DOCS_URL 是否正确
- 确认 URL 中包含完整的访问令牌
- 尝试重新获取 MCP Server 配置

**2. 权限不足**
```
Error: Permission denied
```
- 确认当前用户对文档有操作权限
- 检查文档是否被锁定或只读

**3. 文档不存在**
```
Error: Document not found
```
- 确认 doc_id 正确
- 检查文档是否已被删除

### 日志位置

```bash
# mcporter 日志
~/.mcporter/logs/

# 技能执行日志
~/.openclaw/logs/
```

## 变更日志

参见 [CHANGELOG.md](CHANGELOG.md)

## 许可证

MIT License
