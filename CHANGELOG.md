# Changelog

## [0.3.4] - 2026-03-07

### 修复

- ✅ SKILL.md frontmatter 补充 `mcporter` 二进制依赖声明
- ✅ SKILL.md frontmatter 补充 `DINGTALK_MCP_DOCS_URL` 凭证声明和 `primaryEnv`
- ✅ README.md 明确补充环境变量配置方式与凭证敏感性说明
- ✅ README.md / SKILL.md 新增本地文件脚本行为说明，明确 import / export 会处理工作区内文件
- ✅ 统一 registry metadata、package.json、README、SKILL 的依赖与凭证口径

## [0.3.3] - 2026-03-07

### 文档一致性修复

- ✅ README.md 的读取示例补充灰度提示，避免用户误以为所有实例都能直接调用读取接口
- ✅ references/api-reference.md 补充 `get_document_content_by_url` 灰度说明
- ✅ references/api-reference.md 的“搜索并读取”工作流改为条件可用说明
- ✅ references/error-codes.md 新增“方法列表只显示 5 个工具”时的处理规则

## [0.3.2] - 2026-03-07

### 文档更新

- ✅ README.md 新增已知限制说明，明确 `get_document_content_by_url` 当前仍处于灰度发布
- ✅ README.md 补充 5 个稳定工具 + 1 个灰度工具的可用性说明
- ✅ SKILL.md 新增灰度发布说明，要求缺少读取接口时按官方未放量处理
- ✅ SKILL.md 调整读文档意图与错误处理，避免把缺少接口误判为本地配置或权限问题

## [0.3.1] - 2026-03-05

### 修复 (open-spec 合规审计)

**SKILL.md 三项审计修复：**
- ✅ "重要约束"改为"严格禁止"，使用 Authority 原则措辞（"禁止"/"必须"）
- ✅ 核心工作流写入步骤添加 HARD-GATE 标注 + "提取什么"标注
- ✅ 意图判断新增"关键区分"说明，明确区分在线表格/多维表/文档

**package.json description 同步：**
- ✅ description 与 SKILL.md frontmatter 保持一致，补充口语同义词和排除场景

---

## [0.3.0] - 2026-03-05

### 重构

**SKILL.md 按 dingtalk-neulink 技能包规范全面重构：**
- ✅ 满足 7 区块规范（frontmatter / 严格禁止 / 方法总览 / 意图决策树 / 核心工作流 / 上下文传递 / 错误处理）
- ✅ 主文件控制在 117 行（规范要求 ≤ 150 行）
- ✅ 严格禁止 7 条，含产品专属禁令（类型混淆、URL 格式、覆盖写入确认等）
- ✅ 意图决策树覆盖 6 种用户意图，使用中文自然语言 + 口语化表达
- ✅ 上下文传递链完整无断裂
- ✅ 参数格式区块含正确/错误 JSON 对比示例

**package.json 规范化：**
- ✅ description 符合 D-01~D-08 规则（动词短语开头、含触发场景、含排除场景、含口语同义词、无内部术语）
- ✅ 版本号升级至 0.3.0
- ✅ keywords 移除 "mcp"，新增 "钉钉文档"、"在线文档"

**references/api-reference.md 增强：**
- ✅ 每个方法描述包含四要素（一句话用途 + 使用场景 + 区分说明 + 调用示例）
- ✅ 参数来源标注（如 parentDentryUuid 来自 get_my_docs_root_dentry_uuid）
- ✅ 类型陷阱高亮（accessType 字符串、updateType 数字、docUrl 完整 URL）

**references/error-codes.md 精简：**
- ✅ 错误码速查表精简为一行一错误，含具体修复动作
- ✅ 新增参数类型速查表（正确/错误/后果）
- ✅ 调试流程精简为 6 步

**README.md 同步更新：**
- ✅ 使用示例改为 `--args` JSON 格式
- ✅ 新增目录结构说明
- ✅ 新增注意事项（参数类型陷阱）

---

## [0.2.1] - 2026-03-04

### 修复

- 🐛 测试套件同步适配 v0.2.0 JSON 传参（旧测试用位置参数调用 run_mcporter，与代码不一致）
- ✅ 新增 parse_response / 函数签名一致性 / 内容常量等测试用例（10→18 个）
- 🐛 修复 macOS symlink 导致路径比较失败（/var → /private/var）
- 📝 更新 TEST_REPORT.md


## [0.2.0] - 2026-03-04

### 改动

**传参方式统一为 JSON：**
- ✅ SKILL.md 所有示例改用 `--args '{"key": "value"}'` 格式
- ✅ `create_doc.py` — `run_mcporter()` 改为 `--args` JSON 传参
- ✅ `import_docs.py` — 同上
- ✅ `export_docs.py` — 同上
- ✅ 三个脚本新增 `parse_response()` 统一处理嵌套 `result` 返回结构

**Bug 修复：**
- 🐛 修复脚本无法正确提取 `dentryUuid` 和 `pcUrl`（API 返回嵌套在 `result` 字段内）
- 🐛 `export_docs.py` UUID 正则从固定 32 位改为 `[a-zA-Z0-9]+`，兼容不同长度 ID

---

## [0.1.1] - 2026-03-02

### 新增功能

**脚本工具：**
- ✅ `create_doc.py` - 创建文档并写入内容
- ✅ `import_docs.py` - 从本地文件导入文档（支持 .md/.txt/.markdown）
- ✅ `export_docs.py` - 导出文档到本地

**测试套件：**
- ✅ `test_security.py` - 10 个安全功能单元测试
- ✅ `TEST_REPORT.md` - 完整测试报告

### API 方法（6 个）

| 方法 | 说明 |
|------|------|
| `list_accessible_documents(keyword?)` | 搜索文档 |
| `get_my_docs_root_dentry_uuid()` | 获取根目录 ID |
| `create_doc_under_node(name, parentDentryUuid)` | 创建文档 |
| `create_dentry_under_node(name, accessType, parentDentryUuid)` | 创建节点（11 种类型） |
| `write_content_to_document(content, updateType, targetDentryUuid)` | 写入内容 |
| `get_document_content_by_url(docUrl)` | 获取文档内容 |

### 安全特性

- ✅ 路径沙箱保护
- ✅ 文件扩展名白名单
- ✅ 文件大小限制（10MB）
- ✅ 内容长度限制（50K 字符）
- ✅ URL 格式验证
- ✅ 命令超时保护

---

## [0.1.0] - 2026-03-02

### 初始发布

- ✅ 6 个真实可用的 API 方法
- ✅ 完整 SKILL.md 文档
- ✅ README.md 使用指南

---

## 已知限制

- ⚠️ 创建文档可能返回错误码 `52600007`（企业账号限制）
- ⚠️ 仅支持当前用户有权限访问的文档
