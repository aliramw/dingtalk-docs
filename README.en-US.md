

# DingTalk Docs Skill (dingtalk-docs) v1.0

Manage documents, folders, and content in DingTalk Cloud Docs. Supports document search, creation, content read/write, folder traversal, and fine-grained block element editing.

## ⚠️ Version Compatibility Notice

**This Skill v1.0 requires the new DingTalk Docs MCP URL.**

If you previously configured the old URL (with tool names like `list_accessible_documents`, `write_content_to_document`, etc.), you need to reconfigure it:

1. Visit [DingTalk Docs MCP Marketplace](https://mcp.dingtalk.com/#/detail?mcpId=9629) to get the new StreamableHttp URL
2. Reconfigure: `mcporter config add dingtalk-docs --url "<新版URL>"`

## Features

- ✅ Document Search — Search for accessible documents (`search_documents`)
- ✅ Document Creation — Supports writing initial content simultaneously, defaults to root directory (`create_document`)
- ✅ Content Reading — Retrieve document Markdown content (`get_document_content`)
- ✅ Content Update — Overwrite or append mode (`update_document`)
- ✅ Document Metadata — Retrieve document type, creation time, etc. (`get_document_info`)
- ✅ Folder Management — Create folders (`create_folder`)
- ✅ Folder Traversal — List child nodes, supports pagination and recursion (`list_nodes`)
- ✅ Block Fine-grained Editing — Insert/Update/Delete document block elements (`insert/update/delete_document_block`)
- ✅ File Creation — Create online docs, spreadsheets, presentations, whiteboards, mind maps, multi-dimensional tables, folders (`create_file`)

## Quick Start

### 1. Install the Skill

```bash
clawhub install dingtalk-docs
```

### 2. Install Dependencies

```bash
npm install -g mcporter
```

### 3. Configure Credentials

Visit [DingTalk Docs MCP Marketplace](https://mcp.dingtalk.com/#/detail?mcpId=9629) to get the new StreamableHttp URL:

```bash
mcporter config add dingtalk-docs --url "<你的新版URL>"
```

You can also use an environment variable:

```bash
export DINGTALK_MCP_DOCS_URL="<你的新版URL>"
```

> This URL contains an access token and is a sensitive credential. It is recommended to use `mcporter config` to save it first to avoid leaking it to shell history.

### 4. Usage Examples

```bash
# 创建文档（带初始内容，一步完成）
mcporter call dingtalk-docs create_document --args '{"name": "项目计划", "markdown": "# 项目计划\n\n## 目标"}'

# 搜索文档
mcporter call dingtalk-docs search_documents --args '{"keyword": "项目"}'

# 获取文档内容（支持 URL 或 nodeId）
mcporter call dingtalk-docs get_document_content --args '{"nodeId": "https://alidocs.dingtalk.com/i/nodes/xxx"}'

# 追加内容到文档
mcporter call dingtalk-docs update_document --args '{"nodeId": "doc_nodeId", "markdown": "\n\n## 新章节", "mode": "append"}'

# 列出文件夹内容
mcporter call dingtalk-docs list_nodes --args '{"folderId": "folder_nodeId"}'

# 创建文件夹
mcporter call dingtalk-docs create_folder --args '{"name": "2026 项目"}'
```

## Tool List

### Core Tools

| Tool | Description | Required Parameters |
|------|-------------|---------------------|
| `search_documents` | Search documents | None (keyword is optional) |
| `create_document` | Create online document (can include initial Markdown content) | name |
| `create_file` | Create file (online doc/spreadsheet/presentation/whiteboard/mind map/multi-dimensional table/folder) | name, type |
| `get_document_content` | Get document Markdown content | nodeId |
| `update_document` | Update document content (overwrite or append) | nodeId, markdown |
| `get_document_info` | Get document metadata | nodeId |
| `create_folder` | Create folder | name |
| `list_nodes` | Traverse folder/knowledge base child nodes | None (folderId is optional) |

### Block Fine-Grained Editing Tools

| Tool | Description | Required Parameters |
|------|-------------|---------------------|
| `list_document_blocks` | Query document block list (get blockId) | nodeId |
| `insert_document_block` | Insert block element at specified position | nodeId, element |
| `update_document_block` | Update specified block element (only supports paragraph) | nodeId, blockId, element |
| `delete_document_block` | Delete specified block element (non-recoverable) | nodeId, blockId |

For complete parameter descriptions, see [references/api-reference.md](references/api-reference.md)

For Block element data structures, see:
- Chinese: [dingtalk_document_struct.md](dingtalk_document_struct.md)
- English: [dingtalk_document_struct_en.md](dingtalk_document_struct_en.md)

## Notes

- **nodeId supports automatic URL or ID recognition**, no manual URL construction required
- **`update_document(mode="overwrite")` will clear all content**, please confirm before proceeding
- **`delete_document_block` is non-recoverable**, use `list_document_blocks` to confirm blockId before deletion
- Only supports reading/writing content for documents with contentType=ALIDOC; spreadsheets/PPTs/PDFs do not support Markdown read/write
- **`get_document_content` requires "Download" permission** for the target document; content cannot be retrieved with view-only permission
- **`get_document_content` does not support cross-organization documents**; cross-organization documents will return a `forbidden.accessDenied` error
- The credential URL contains an access token; please keep it secure

## Directory Structure

```
dingtalk-docs/
├── SKILL.md                 # AI 技能入口（≤150 行）
├── package.json             # 元数据
├── README.md                # 人类可读说明
├── CHANGELOG.md             # 变更日志
├── references/
│   ├── api-reference.md     # 12 个工具完整参数 Schema
│   ├── block-api.md         # Block 工具完整 Schema + 块元素数据结构
│   └── error-codes.md       # 错误码说明 + 调试流程
├── scripts/
│   ├── mcporter_utils.py    # mcporter 公共工具函数
│   ├── create_doc.py        # 创建在线文档脚本
│   ├── create_file.py       # 创建文件脚本（支持7种类型）
│   ├── block_ops.py         # Block 精细编辑脚本
│   ├── import_docs.py       # 导入文档脚本
│   └── export_docs.py       # 导出文档脚本
└── tests/
    ├── test_security.py     # 安全功能测试
    ├── testcases.json       # 评测用例
    └── TEST_REPORT.md       # 测试报告
```

## Development

```bash
# 克隆仓库
git clone https://github.com/aliramw/dingtalk-docs.git

# 运行测试
python3 tests/test_security.py -v
```

## License

MIT License

## Author

Marila@Dingtalk
