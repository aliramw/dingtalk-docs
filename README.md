# 钉钉文档操作技能 (dingtalk-docs)

钉钉文档 MCP 技能，用于通过 OpenClaw 操作钉钉云文档。

## 功能特性

- ✅ 文档 CRUD 操作（创建/读取/更新/删除）
- ✅ 文档内容块管理（插入/更新/删除）
- ✅ 文档权限管理
- ✅ 文件夹操作
- ✅ 文档分享
- ✅ 批量导入导出

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

访问 [钉钉 MCP 广场 钉钉文档 MCP 服务](https://mcp.dingtalk.com/#/detail?mcpId=1047&detailType=marketMcpDetail) 获取文档服务的 Streamable HTTP URL。

```bash
mcporter config add dingtalk-docs --url "<你的_URL>"
```

### 4. 使用示例

```bash
# 创建文档
mcporter call dingtalk-docs create_doc --title "我的文档"

# 获取内容
mcporter call dingtalk-docs get_content --doc_id "doc_xxx"

# 更新文档
mcporter call dingtalk-docs update_doc --doc_id "doc_xxx" --content "# 新内容"
```

## API 参考

完整 API 列表请查看 [SKILL.md](SKILL.md)

## 安全说明

- 凭证 URL 包含访问令牌，请妥善保管
- 建议在测试环境验证后再操作生产数据
- 脚本已实施路径沙箱、输入验证等安全措施

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
