# Changelog

## [0.1.0] - 2026-03-02

### 初始发布

**可用 API 方法（6 个）：**

| 方法 | 说明 |
|------|------|
| `list_accessible_documents(keyword?)` | 搜索当前用户有权限访问的文档列表 |
| `get_my_docs_root_dentry_uuid()` | 获取"我的文档"根目录节点 ID |
| `create_doc_under_node(name, parentDentryUuid)` | 在指定父节点下创建新文档 |
| `create_dentry_under_node(name, accessType, parentDentryUuid)` | 创建节点（支持文档/表格/PPT/文件夹等 11 种类型） |
| `write_content_to_document(content, updateType, targetDentryUuid)` | 写入内容到文档（支持覆盖/续写模式） |
| `get_document_content_by_url(docUrl)` | 通过 URL 获取文档内容（Markdown 格式） |

**安全特性：**
- ✅ 凭证隔离（推荐 mcporter config 存储）
- ✅ 权限控制（仅访问有权限的文档）
- ✅ 命令超时保护
- ✅ 输入验证（dentryUuid 格式）

**文档：**
- ✅ SKILL.md 完整 API 说明与示例
- ✅ README.md 快速开始指南
- ✅ 故障排查指南

---

## 已知限制

- ⚠️ 创建文档可能返回错误码 `52600007`（企业账号限制或服务临时故障）
- ⚠️ 仅支持当前用户有权限访问的文档
- ⚠️ 需要企业钉钉账号才能正常使用全部功能

---

## 待开发功能

- [ ] 文档权限管理（如 MCP 服务后续支持）
- [ ] 文档移动/复制
- [ ] 文档版本历史
- [ ] 批量操作优化

