# DNSHE 域名自动续期工具

> ⚠️ **安全提示**：本工具纯Vibe Coding生成，仅供个人使用。请勿将真实的 API 密钥和 Webhook URL 提交到代码仓库。所有敏感信息应通过 GitHub Secrets 配置。

基于 GitHub Actions 的 DNSHE 域名自动续期工具，支持每月自动续期并将结果发送到企业微信 webhook。

## 功能特性

- ✅ 每月自动续期所有域名
- ✅ 支持手动触发续期
- ✅ 企业微信 webhook 通知
- ✅ 多 API 密钥管理
- ✅ 详细的续期日志
- ✅ 并发续期，提高效率

## 快速开始

### 1. 配置 GitHub Secrets

在你的 GitHub 仓库中，进入 `Settings` -> `Secrets and variables` -> `Actions`，添加以下 Secrets：

#### API_KEYS (必需)

API 密钥配置，格式为 JSON 数组：

```json
[
  {
    "name": "密钥1名称",
    "api_key": "your_api_key_1",
    "api_secret": "your_api_secret_1"
  },
  {
    "name": "密钥2名称",
    "api_key": "your_api_key_2",
    "api_secret": "your_api_secret_2"
  }
]
```

#### WEBHOOK_URL (可选)

企业微信机器人 webhook URL，用于接收续期通知。

### 2. 启用 GitHub Actions

将代码推送到 GitHub 后，GitHub Actions 会自动启用。

### 3. 手动触发续期

进入 GitHub 仓库的 `Actions` 标签页，选择 `DNSHE 域名自动续期` 工作流，点击 `Run workflow` 按钮。

可以选择是否强制续期所有域名（默认只续期可以续期的域名）。

## 工作流说明

### 自动执行

工作流默认在每月 1 日 0:00（UTC 时间）自动执行。

```yaml
schedule:
  - cron: '0 0 1 * *'
```

### 手动执行

支持通过 GitHub Actions 界面手动触发，可以选择：
- **false**: 正常续期（默认）
- **true**: 强制续期所有域名

## 本地使用

### 安装依赖

```bash
pip install requests
```

### 配置 API 密钥

创建 `api_keys.json` 文件：

```json
[
  {
    "name": "密钥1",
    "api_key": "your_api_key",
    "api_secret": "your_api_secret"
  }
]
```

### 运行续期脚本

```bash
# 基本使用
python auto_renew.py

# 使用环境变量配置
export API_KEYS='[{"name":"key1","api_key":"xxx","api_secret":"yyy"}]'
export WEBHOOK_URL="https://qyapi.weixin.qq.com/cgi-bin/webhook/send?key=xxx"
python auto_renew.py
```

## 企业微信通知

### 配置 Webhook

1. 在企业微信群中添加机器人
2. 获取 webhook URL
3. 将 URL 添加到 GitHub Secrets `WEBHOOK_URL`

### 通知内容

通知包含以下信息：
- 执行时间
- 统计信息（总计、成功、失败、跳过）
- 失败详情（如果有）
- 成功续期列表（最多显示 5 个）

### 特殊通知

如果有续期失败的域名，会 `@all` 提醒所有人。

## 日志文件

每次执行后，会在 `renewal_log.txt` 中生成详细的续期日志，包括：

- 执行时间
- 统计信息
- 每个域名的续期结果
- 剩余天数和到期时间
- 错误信息（如果有）

日志文件会作为 GitHub Actions artifact 保存 30 天。

## API 密钥获取

1. 登录 DNSHE 客户区
2. 进入 "免费域名" 页面
3. 在底部找到 "API 管理" 卡片
4. 点击 "创建 API 密钥"
5. 保存 API Key 和 API Secret

## 注意事项

1. **API 密钥安全**: 不要将 API 密钥提交到代码仓库，始终使用 GitHub Secrets
2. **速率限制**: 默认限制 60 请求/分钟，脚本已优化并发数
3. **续期规则**: 域名续期有冷却时间，未到时间会自动跳过
4. **时区注意**: GitHub Actions 使用 UTC 时间，每月 1 日 0:00 UTC 对应北京时间 8:00

## 故障排查

### 续期失败

检查以下内容：
1. API 密钥是否正确
2. API 密钥是否有效
3. 网络连接是否正常
4. 域名是否已过期

### 企业微信通知未收到

检查以下内容：
1. WEBHOOK_URL 是否正确配置
2. 企业微信机器人是否被禁用
3. 消息是否被群主拦截

### GitHub Actions 未执行

检查以下内容：
1. 工作流文件是否在 `.github/workflows/` 目录
2. 文件名是否为 `auto-renew.yml`
3. GitHub Actions 是否已启用

## 许可证

MIT License
