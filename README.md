# 钉钉文档操作技能 (dingtalk-docs)

管理钉钉云文档中的文档、文件夹和内容。支持文档搜索、创建、内容读写和文件夹整理。

## 功能特性

- ✅ 文档搜索 — 搜索有权限访问的文档
- ✅ 文档创建 — 在指定节点下创建新文档
- ✅ 多类型节点创建 — 支持文档/表格/PPT/文件夹等 11 种类型
- ✅ 内容写入 — 覆盖写入或续写模式（支持 Markdown）
- ✅ 内容读取 — 通过 URL 获取文档 Markdown 内容（当前为灰度能力）
- ✅ 根目录获取 — 获取"我的文档"根节点 ID

## 当前已知限制

### `get_document_content_by_url` 仍在灰度发布

根据 GitHub issue #1 下维护者的回复：`get_document_content_by_url` **目前还在灰度中，全量发布还需要一点时间**。

这意味着：

- 通过 **钉钉 MCP 广场** 获取的 Streamable HTTP URL，接入后**可能只会看到 5 个工具**
- 缺失 `get_document_content_by_url` **不代表你配置错了，也不一定是权限问题**
- 当前更可能是**官方服务端尚未对你的实例放量**

目前常见的 5 个可见工具是：

- `list_accessible_documents`
- `get_my_docs_root_dentry_uuid`
- `create_doc_under_node`
- `create_dentry_under_node`
- `write_content_to_document`

如果你在 `mcporter list` 或其他 MCP 客户端里看不到 `get_document_content_by_url`，先按**服务端灰度未放开**处理，不要先怀疑本地配置。

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

也可以使用环境变量：

```bash
export DINGTALK_MCP_DOCS_URL="<你的_URL>"
```

> 这个 URL 含访问令牌，属于敏感凭证。推荐优先用 `mcporter config` 保存，避免泄露到 shell 历史。

### 4. 使用示例

```bash
# 获取根目录 ID
mcporter call dingtalk-docs.get_my_docs_root_dentry_uuid

# 创建文档
mcporter call dingtalk-docs.create_doc_under_node --args '{"name": "我的文档", "parentDentryUuid": "ROOT_ID"}'

# 搜索文档
mcporter call dingtalk-docs.list_accessible_documents --args '{"keyword": "项目"}'

# 写入内容到文档（覆盖模式）
mcporter call dingtalk-docs.write_content_to_document --args '{"content": "# 标题\n\n内容", "updateType": 0, "targetDentryUuid": "doc_xxx"}'

# 获取文档内容（仅当你的实例已放量该灰度接口时可用）
mcporter call dingtalk-docs.get_document_content_by_url --args '{"docUrl": "https://alidocs.dingtalk.com/i/nodes/doc_xxx"}'
```

## 方法列表

| 方法 | 说明 | 必填参数 | 状态 |
|------|------|---------|------|
| `get_my_docs_root_dentry_uuid` | 获取根目录 ID | 无 | 稳定可用 |
| `list_accessible_documents` | 搜索文档 | 无（keyword 选填） | 稳定可用 |
| `create_doc_under_node` | 创建文档 | name, parentDentryUuid | 稳定可用 |
| `create_dentry_under_node` | 创建节点（多类型） | name, accessType, parentDentryUuid | 稳定可用 |
| `write_content_to_document` | 写入内容 | content, updateType, targetDentryUuid | 稳定可用 |
| `get_document_content_by_url` | 获取文档内容 | docUrl | **灰度中，部分实例不可见** |

完整参数说明请查看 [references/api-reference.md](references/api-reference.md)

## 注意事项

- **accessType 必须是字符串**（如 `"13"`），传数字会静默失败
- **updateType 必须是数字**（如 `0`），传字符串会静默失败
- **docUrl 必须是完整 URL**（`https://alidocs.dingtalk.com/i/nodes/{dentryUuid}`），不能只传 ID
- 凭证 URL 包含访问令牌，请妥善保管
- 仅能操作当前用户有权限访问的文档

## 本地文件脚本说明

`scripts/` 目录中的辅助脚本会读写本地文件：

- `import_docs.py`：读取工作区内的本地 Markdown / TXT 文件并导入到钉钉文档
- `export_docs.py`：将钉钉文档内容导出到工作区内的本地 Markdown 文件
- `create_doc.py`：读取输入内容并创建文档

这些脚本已做安全限制：

- 仅允许访问工作区内路径
- 使用 `resolve_safe_path()` 防止目录遍历
- 限制文件大小和扩展名
- 仅通过 `mcporter` 调用 MCP 服务，不直接发起网络请求

## 目录结构

```
dingtalk-docs/
├── SKILL.md                 # AI 技能入口（≤150 行）
├── package.json             # 元数据
├── README.md                # 人类可读说明
├── CHANGELOG.md             # 变更日志
├── references/
│   ├── api-reference.md     # 完整参数 Schema + 返回值
│   └── error-codes.md       # 错误码说明 + 调试流程
├── scripts/
│   ├── create_doc.py        # 创建文档脚本
│   ├── import_docs.py       # 导入文档脚本
│   └── export_docs.py       # 导出文档脚本
└── tests/
    ├── test_security.py     # 安全功能测试
    └── TEST_REPORT.md       # 测试报告
```

## 开发

```bash
# 克隆仓库
git clone https://github.com/aliramw/dingtalk-docs.git

# 运行测试
python3 tests/test_security.py -v
```

## 许可证

MIT License

## 作者

Marila@Dingtalk
