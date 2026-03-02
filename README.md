# 钉钉文档操作技能 (dingtalk-docs)

钉钉文档 MCP 技能，用于通过 OpenClaw 操作钉钉云文档。

## 功能特性

- ✅ 文档搜索（搜索有权限访问的文档）
- ✅ 文档创建（在指定节点下创建新文档）
- ✅ 节点创建（支持文档/表格/PPT/文件夹等多种类型）
- ✅ 内容写入（覆盖写入或续写模式）
- ✅ 内容读取（通过 URL 获取文档 Markdown 内容）
- ✅ 根目录获取（获取"我的文档"根节点 ID）

## 快速开始

### 1. 安装技能

```bash
clawhub install dingtalk-docs
```

### 2. 安装依赖

```bash
npm install -g mcporter
```

### 3. 配置凭证

访问 [钉钉 MCP 广场](https://mcp.dingtalk.com) 找到 **钉钉文档** 服务，获取 Streamable HTTP URL：

```bash
mcporter config add dingtalk-docs --url "<你的_URL>"
```

### 4. 使用示例

```bash
# 获取根目录 ID
ROOT_ID=$(mcporter call dingtalk-docs.get_my_docs_root_dentry_uuid | jq -r '.rootDentryUuid')

# 创建文档
mcporter call dingtalk-docs.create_doc_under_node "我的文档" "$ROOT_ID"

# 搜索文档
mcporter call dingtalk-docs.list_accessible_documents "项目"

# 写入内容到文档
mcporter call dingtalk-docs.write_content_to_document "# 标题\n\n内容" "0" "doc_xxx"

# 获取文档内容
mcporter call dingtalk-docs.get_document_content_by_url "https://alidocs.dingtalk.com/i/nodes/doc_xxx"
```

## API 参考

完整 API 列表请查看 [SKILL.md](SKILL.md)

**6 个可用方法：**

| 方法 | 说明 |
|------|------|
| `list_accessible_documents(keyword?)` | 搜索文档 |
| `get_my_docs_root_dentry_uuid()` | 获取根目录 ID |
| `create_doc_under_node(name, parentDentryUuid)` | 创建文档 |
| `create_dentry_under_node(name, accessType, parentDentryUuid)` | 创建节点（多类型） |
| `write_content_to_document(content, updateType, targetDentryUuid)` | 写入内容 |
| `get_document_content_by_url(docUrl)` | 获取文档内容 |

## 安全说明

- 凭证 URL 包含访问令牌，请妥善保管
- 建议在测试环境验证后再操作生产数据
- 仅能操作当前用户有权限访问的文档

## 开发

```bash
# 克隆仓库
git clone https://github.com/aliramw/dingtalk-docs.git

# 运行测试
npm test
```

## 许可证

MIT License

## 作者

Marila@Dingtalk
