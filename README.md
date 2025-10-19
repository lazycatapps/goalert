# GoAlert for Lazycat Platform

## 项目简介

本项目将 [GoAlert](https://github.com/target/goalert) 打包移植到 Lazycat 平台，帮助团队快速搭建可观察性告警与通知中心。通过随仓库提供的 Docker Compose 与脚本，您可以在 Lazycat 环境内一键启动 GoAlert，并配合官方文档完成后续配置。

GoAlert 是 Target 开源的值班调度与告警自动化平台，支持多渠道通知、排班管理、Webhook 接入等功能，能够帮助 SRE 与运维团队在故障发生时快速响应。

## 主要功能

- 📟 多渠道通知：支持邮件、短信、语音电话等通知方式
- 🕒 值班排班：以日历视图管理值班计划与升级策略
- 🔔 通知升级：根据服务级别自动升级告警，确保及时响应
- 🔗 Webhook 集成：通过 REST API 与第三方监控系统对接
- 🛠️ 操作便捷：提供 CLI、GraphQL API 与 Web 控制台
- 🔒 安全审计：内置审计日志与权限管理

## 使用说明

1. 首次登录默认账号为 `admin` / `admin123`。请立即修改密码，并参考仓库内的 `HELP.md` 了解 Gmail 邮件通知与 Webhook 配置示例。

## 致谢

- **GoAlert 团队**：感谢 Target 及 GoAlert 项目维护者长期迭代高质量的开源告警平台
- **开源社区**：感谢所有为 GoAlert 提交代码、文档与反馈的贡献者
- **Lazycat 平台**：提供一站式的应用部署与交付能力，帮助我们快速整合社区优秀项目

## 版权说明

- 本仓库的打包与部署配置遵循 [Apache License 2.0](LICENSE)，版权所有 © 2025 Lazycat Apps
- GoAlert 原始项目同样使用 [Apache License 2.0](https://github.com/target/goalert/blob/master/LICENSE.md)

## 相关链接

- GoAlert 官网：https://goalert.me/
- GoAlert 源代码仓库：https://github.com/target/goalert
- GoAlert 官方文档：https://goalert.me/docs
- Lazycat 平台：https://lazycat.cloud/
