# 钉钉云文档参数参考

> 完整的参数 Schema、返回值格式和调用示例。SKILL.md 的按需加载参考文件。

## 1. list_accessible_documents

搜索当前用户有权限访问的文档列表。当用户想找某个文档、查看文档列表时使用。与 get_document_content_by_url 不同：本方法搜索文档元信息，后者读取文档正文内容。

**参数:**

| 参数 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| keyword | string | 否 | 搜索关键词，为空时返回所有有权限的文档 |

**返回值:**

```json
{
  "docs": [
    {
      "dentryUuid": "DnRL6jAJMNX9kAgycoLy2vOo8yMoPYe1",
      "title": "项目计划",
      "type": "document",
      "updateTime": "2026-03-01T10:00:00Z"
    }
  ]
}
```

**关键字段:**
- `dentryUuid` — 文档唯一 ID，用于拼接 `https://alidocs.dingtalk.com/i/nodes/{dentryUuid}` 读取内容
- `title` — 文档标题，用于展示给用户确认

**调用示例:**

```bash
# 搜索包含"项目"的文档
mcporter call dingtalk-docs.list_accessible_documents --args '{"keyword": "项目"}'

# 列出所有有权限的文档
mcporter call dingtalk-docs.list_accessible_documents
```

---

## 2. get_my_docs_root_dentry_uuid

获取当前用户"我的文档"空间的根目录节点 ID。当用户想创建文档或文件夹时，必须先调用此方法获取根目录 ID 作为父节点。与 list_accessible_documents 不同：本方法获取根节点 ID，后者搜索文档。

**参数:** 无

**返回值:**

```json
{
  "rootDentryUuid": "DnRL6jAJMNX9kAgycoLy2vOo8yMoPYe1"
}
```

**关键字段:**
- `rootDentryUuid` — 根目录 ID，用作 create_doc_under_node 或 create_dentry_under_node 的 parentDentryUuid

**调用示例:**

```bash
mcporter call dingtalk-docs.get_my_docs_root_dentry_uuid
```

---

## 3. create_doc_under_node

在指定父节点下创建一篇新的在线文档。当用户只需要创建文档时使用。与 create_dentry_under_node 不同：本方法只能创建文档，后者支持文件夹、表格、PPT 等多种类型。

**参数:**

| 参数 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| name | string | 是 | 文档名称 |
| parentDentryUuid | string | 是 | 父节点 ID（来源：get_my_docs_root_dentry_uuid 的 rootDentryUuid 或文件夹的 dentryUuid） |

**返回值:**

```json
{
  "dentryUuid": "abc123def456",
  "title": "项目计划",
  "createTime": "2026-03-01T10:00:00Z",
  "url": "https://alidocs.dingtalk.com/i/nodes/abc123def456"
}
```

**关键字段:**
- `dentryUuid` — 新文档 ID，用于 write_content_to_document 的 targetDentryUuid，或拼接 URL 读取内容
- `url` — 文档访问链接

**调用示例:**

```bash
mcporter call dingtalk-docs.create_doc_under_node --args '{"name": "我的新文档", "parentDentryUuid": "ROOT_ID"}'
```

---

## 4. create_dentry_under_node

在指定节点下创建新节点，支持多种类型。当用户需要创建文件夹、表格、PPT、脑图等非文档类型时使用。与 create_doc_under_node 不同：本方法支持所有节点类型，后者只能创建文档。

**参数:**

| 参数 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| name | string | 是 | 节点名称 |
| accessType | string | 是 | 节点类型（**必须是字符串**，见下方枚举表） |
| parentDentryUuid | string | 是 | 父节点 ID（来源：get_my_docs_root_dentry_uuid 的 rootDentryUuid 或文件夹的 dentryUuid） |

**accessType 节点类型枚举:**

| 值 | 类型 | 说明 |
|----|------|------|
| "0" | 文档 | 在线文档（等同于 create_doc_under_node） |
| "1" | 表格 | 在线表格 |
| "2" | PPT | 在线演示文稿 |
| "3" | 白板 | 在线白板 |
| "6" | 脑图 | 思维导图 |
| "7" | 多维表 | AI 多维表 |
| "9" | 视频 | 视频文件 |
| "10" | 图片 | 图片文件 |
| "13" | 文件夹 | 文件夹（用于整理文档） |
| "14" | PDF | PDF 文件 |
| "99" | 其他文件 | 其他类型文件 |

**⚠️ accessType 必须是字符串类型，传数字会静默失败。**

**返回值:**

```json
{
  "dentryUuid": "folder789xyz",
  "title": "项目资料",
  "createTime": "2026-03-01T10:00:00Z"
}
```

**关键字段:**
- `dentryUuid` — 新节点 ID，文件夹可作为后续创建操作的 parentDentryUuid

**调用示例:**

```bash
# 创建文件夹
mcporter call dingtalk-docs.create_dentry_under_node --args '{"name": "项目资料", "accessType": "13", "parentDentryUuid": "ROOT_ID"}'

# 创建表格
mcporter call dingtalk-docs.create_dentry_under_node --args '{"name": "数据报表", "accessType": "1", "parentDentryUuid": "ROOT_ID"}'

# 创建脑图
mcporter call dingtalk-docs.create_dentry_under_node --args '{"name": "需求分析", "accessType": "6", "parentDentryUuid": "ROOT_ID"}'
```

---

## 5. write_content_to_document

将文本内容写入目标文档，支持覆盖或续写模式。当用户想写入、更新或追加文档内容时使用。与 get_document_content_by_url 不同：本方法写入内容，后者读取内容。

**参数:**

| 参数 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| content | string | 是 | 要写入的内容（支持 Markdown 格式） |
| updateType | number | 是 | 0=覆盖写入（**清空原内容**），1=续写（追加到末尾）。**必须是数字**，传字符串会静默失败 |
| targetDentryUuid | string | 是 | 目标文档 ID（来源：create_doc_under_node 的 dentryUuid 或搜索结果的 dentryUuid） |

**⚠️ 覆盖写入（updateType=0）会清空文档原有内容，操作前应确认用户意图。不确定时先问用户。**

**返回值:**

```json
{
  "success": true
}
```

**调用示例:**

```bash
# 覆盖写入
mcporter call dingtalk-docs.write_content_to_document --args '{"content": "# 项目计划\n\n## 目标\n完成 Q1 目标", "updateType": 0, "targetDentryUuid": "doc_xxx"}'

# 续写（追加内容）
mcporter call dingtalk-docs.write_content_to_document --args '{"content": "\n\n## 更新日志\n- 2026-03-02: 初始版本", "updateType": 1, "targetDentryUuid": "doc_xxx"}'
```

---

## 6. get_document_content_by_url

根据文档 URL 获取文档内容，返回 Markdown 格式。当用户想查看、读取文档内容时使用。与 list_accessible_documents 不同：本方法读取文档正文，后者搜索文档元信息。

**灰度说明：** 该方法目前仍处于官方灰度发布阶段。部分通过钉钉 MCP 广场接入的实例，方法列表里可能看不到它，只会显示另外 5 个稳定工具。遇到这种情况，优先按**服务端未放量**处理，不要先判断为本地配置错误或权限问题。

**参数:**

| 参数 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| docUrl | string | 是 | 文档完整 URL，格式：`https://alidocs.dingtalk.com/i/nodes/{dentryUuid}` |

**⚠️ 必须传完整 URL，不能只传 dentryUuid。需要先通过其他方法获取 dentryUuid，再拼接成完整 URL。**

**返回值:**

```json
{
  "content": "# 文档标题\n\n正文内容...",
  "format": "markdown"
}
```

**关键字段:**
- `content` — 文档的 Markdown 格式内容

**调用示例:**

```bash
mcporter call dingtalk-docs.get_document_content_by_url --args '{"docUrl": "https://alidocs.dingtalk.com/i/nodes/DnRL6jAJMNX9kAgycoLy2vOo8yMoPYe1"}'
```

---

## 完整工作流示例

### 创建文档并写入内容

```bash
# 1. 获取根目录 ID
ROOT_ID=$(mcporter call dingtalk-docs.get_my_docs_root_dentry_uuid --output json | jq -r '.rootDentryUuid')

# 2. 创建新文档
DOC_ID=$(mcporter call dingtalk-docs.create_doc_under_node --args "{\"name\": \"项目计划\", \"parentDentryUuid\": \"$ROOT_ID\"}" --output json | jq -r '.dentryUuid')

# 3. 写入内容
mcporter call dingtalk-docs.write_content_to_document --args "{\"content\": \"# 项目计划\\n\\n## 目标\\n完成 Q1 目标\", \"updateType\": 0, \"targetDentryUuid\": \"$DOC_ID\"}"

# 4. 验证内容
mcporter call dingtalk-docs.get_document_content_by_url --args "{\"docUrl\": \"https://alidocs.dingtalk.com/i/nodes/$DOC_ID\"}"
```

### 搜索并读取文档（仅当 `get_document_content_by_url` 已放量可用时）

```bash
# 1. 搜索文档
mcporter call dingtalk-docs.list_accessible_documents --args '{"keyword": "项目"}'

# 2. 获取文档内容（假设搜索到 dentryUuid=abc123）
mcporter call dingtalk-docs.get_document_content_by_url --args '{"docUrl": "https://alidocs.dingtalk.com/i/nodes/abc123"}'
```

如果当前实例的方法列表里没有 `get_document_content_by_url`，说明该能力暂未对你的实例放量；此时只能先停在搜索结果，不要误判成本地配置问题。

### 创建文件夹并整理文档

```bash
# 1. 获取根目录
ROOT_ID=$(mcporter call dingtalk-docs.get_my_docs_root_dentry_uuid --output json | jq -r '.rootDentryUuid')

# 2. 创建文件夹
FOLDER_ID=$(mcporter call dingtalk-docs.create_dentry_under_node --args "{\"name\": \"2026 项目\", \"accessType\": \"13\", \"parentDentryUuid\": \"$ROOT_ID\"}" --output json | jq -r '.dentryUuid')

# 3. 在文件夹中创建文档
mcporter call dingtalk-docs.create_doc_under_node --args "{\"name\": \"Q1 计划\", \"parentDentryUuid\": \"$FOLDER_ID\"}"
```
