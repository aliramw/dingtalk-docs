# 钉钉云文档错误码参考

> SKILL.md 的按需加载参考文件。遇到错误时查阅此文档。

## 错误码速查表

| 错误信息 | 原因 | 修复动作 |
|----------|------|----------|
| Invalid credentials | 凭证配置错误或令牌过期 | 访问 https://mcp.dingtalk.com 重新获取 URL，执行 `mcporter config add dingtalk-docs --url "<新URL>"` |
| Permission denied | 当前用户无权限操作该文档 | 确认文档分享权限，检查是否被锁定或只读，联系文档所有者授权 |
| Document not found | dentryUuid 无效或文档已删除 | 确认 ID 来自返回值（禁止编造），用 list_accessible_documents 重新搜索 |
| 52600007 | 企业账号限制或父节点 ID 无效 | 确认企业钉钉账号，确认 parentDentryUuid 来自 get_my_docs_root_dentry_uuid |
| Timeout | 网络超时 | 检查网络连接，稍后重试 |
| Invalid parameter | 参数类型或格式错误 | 见下方参数类型速查 |
| 方法列表里没有 `get_document_content_by_url` / 只显示 5 个工具 | 官方灰度未放量 | 按服务端未放量处理，不要优先判断为本地配置错误或权限问题 |

## 参数类型速查

| 参数 | 正确 | 错误 | 后果 |
|------|------|------|------|
| accessType | `"13"`（字符串） | `13`（数字） | 静默失败 |
| updateType | `0`（数字） | `"0"`（字符串） | 静默失败 |
| docUrl | `https://alidocs.dingtalk.com/i/nodes/{id}`（完整 URL） | `DnRL6jAJ...`（只传 ID） | 报错 |

## 调试流程

```
1. 检查错误信息 → 对照上方速查表
2. 确认参数格式 → accessType 是字符串，updateType 是数字，docUrl 是完整 URL
3. 确认 ID 来源 → 所有 dentryUuid 必须从返回值中提取
4. 检查权限 → 确认用户对文档有操作权限
5. 检查配置 → 确认凭证 URL 配置正确
6. 如果方法列表里缺少 `get_document_content_by_url` → 按官方灰度未放量处理
7. 重试 → 排除服务端临时故障
```

## 日志位置

```bash
~/.mcporter/logs/      # mcporter 日志
~/.openclaw/logs/      # 技能执行日志
```
