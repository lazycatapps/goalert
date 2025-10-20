# GoAlert 快速入门指南

本文介绍如何在 GoAlert 中配置 Gmail 邮件通知、创建服务与升级策略，以及使用通用 Webhook 自动触发并关闭告警，帮助你在第一次部署后快速投入使用。

## 1. 配置 Gmail SMTP

1. **准备 Gmail 账号**
   - 在该 Google 账号上启用双重验证（2-Step Verification）。
   - 前往 Google 账号安全页面生成 “App Password”（应用专用密码），应用类型选择「Mail」，记录生成的 16 位密码。
   - 了解 Gmail 的发信限额（个人 Gmail 每日约 500 封，Google Workspace 视套餐而定）。发件地址必须与这个账号一致。

2. **在 GoAlert 中填写 SMTP 设置**
   - 使用管理员账户登录 GoAlert 控制台，打开 `Admin → Config`。
   - 在 **SMTP** 区块中：
     - 勾选 **Enable**（启用）。
     - **From** 填写你的 Gmail 地址（例如 `alerts@example.com`）。
     - **Address** 填 `smtp.gmail.com:587`。
     - **Username** 填 Gmail 地址，**Password** 填刚才生成的 App Password。
     - 勾选 **Disable TLS**，让 GoAlert 先建立明文连接再升级到 STARTTLS（符合 Gmail 587 端口要求）。
     - 保持 **Skip Verify** 未勾选（除非你使用自签名证书）。
   - 点击 **Save** 保存。若信息填写正确，GoAlert 将开始使用 Gmail 发送通知邮件。

3. **验证邮件联系方法**
   - 前往 `Users`，编辑某位用户，添加或更新 **Email** 联系方式。
   - GoAlert 会发送验证邮件，按邮件中的提示完成确认，确保邮件链路正常。

## 2. 创建服务与升级策略

1. **配置升级策略（Escalation Policy）**
   - 打开 `Escalation Policies`，点击 **Create Escalation Policy**。
   - 新增至少一个步骤（Step），将用户或 Schedule 加入，并设置延迟时间（例如 0 分钟表示立即通知）。

2. **创建服务（Service）**
   - 打开 `Services`，点击 **Create Service**。
   - 填写服务名称，并关联刚建立的升级策略。
   - 保存后，该服务的所有告警都会按照策略逐级通知相关负责人。

## 3. 使用通用 Webhook 触发告警

1. **生成 Generic Webhook 集成 Key**
   - 进入服务详情页的 **Integrations** 标签页，点击 **Add Integration Key**。
   - 选择 **Generic Webhook**，填写名称后保存。
   - 系统会生成一个 URL，形如 `https://<你的域名>/api/v2/generic/incoming?token=<token>`。复制包含 `token` 的完整链接（注意保持 URL 编码状态）。

2. **发送 HTTP POST 请求创建告警**
   - 支持 `application/json` 和 `application/x-www-form-urlencoded`。以下为 JSON 示例：
     ```bash
     curl -X POST \
       'https://goalert.example.com/api/v2/generic/incoming?token=<token>' \
       -H 'Content-Type: application/json' \
       -d '{
             "summary": "CPU 使用率过高",
             "details": "server=prod-01, usage=97%",
             "dedup": "cpu-prod-01",
             "meta": {
               "severity": "critical",
               "source": "Prometheus"
             }
           }'
     ```
     - `summary`（必填）：告警标题。
     - `details`（可选）：告警详情。
     - `dedup`（可选）：去重键；相同值被视作同一告警，可避免重复通知。
     - `meta`（可选）：自定义元数据键值对，会显示在告警元信息中。
   - 请求成功返回 HTTP 204（或在请求头加 `Accept: application/json` 时返回包含 `AlertID` 的 JSON），告警随即出现在 GoAlert 界面，并按照升级策略通知。

3. **自动关闭告警（可选）**
   - 当外部系统检测到问题恢复时，可通过相同 Webhook 发送 `"action": "close"` 的请求关闭告警：
     ```bash
     curl -X POST \
       'https://goalert.example.com/api/v2/generic/incoming?token=<token>' \
       -H 'Content-Type: application/json' \
       -d '{
             "summary": "CPU 使用率过高",
             "dedup": "cpu-prod-01",
             "action": "close"
           }'
     ```
   - GoAlert 会把对应的告警状态标记为已关闭，此后不再继续升级通知。

## 4. 端到端验证

1. 确认有至少一个用户的 Email 联系方式已验证。
2. 调用 Webhook 触发测试告警，检查：
   - 告警是否出现在 `Alerts` 列表中。
   - Gmail 是否成功发出通知邮件并送达。
3. 在 UI 中手动确认（Acknowledge）或关闭告警，或者使用 `action: close` 请求模拟自动恢复。

完成以上步骤后，GoAlert 即可作为告警中心：通过 Gmail 发送邮件通知，并允许任意能发 HTTP 请求的系统自动创建或关闭告警。你可以继续添加更多服务、集成 Key 或通知渠道，以覆盖所有业务场景。
