# JustRunMy.app 自动续期

> 基于 SeleniumBase + sing-box 代理的 JustRunMy.app 自动续期脚本，支持 Cloudflare Turnstile 人机验证自动处理。

---

## 🌟 核心特色

- 🤖 **自动续期**：一键自动登录并点击 Reset Timer，无需人工干预。
- 🛡️ **人机验证**：内置 Cloudflare Turnstile 物理级点击绕过方案。
- 🌐 **全协议代理**：内置 sing-box 核心，支持 `vless` / `vmess` / `tuic` / `hy2` / `socks5` / `http` / `https` 等代理协议。
- 📱 **Telegram 推送**：续期完成后自动推送结果到 Telegram。
- 🖥️ **动态应用名**：自动抓取并显示网页上的应用名称。

---

## ⚡ 快速开始

### 1. Fork 本项目

点击右上角 **Fork** 按钮，将本项目复制到你的个人仓库。

### 2. 配置 Secrets

前往你 Fork 后的仓库 → `Settings` → `Secrets and variables` → `Actions` → `New repository secret`，添加以下变量：

| 变量名 | 是否必填 | 示例值 | 说明 |
|---|---|---|---|
| `JUSTRUNMY_EMAIL` | ✅ | `user@example.com` | JustRunMy 登录邮箱 |
| `JUSTRUNMY_PASSWORD` | ✅ | `your_password` | JustRunMy 登录密码 |
| `PROXY_URL` | ❌ | `vless://uuid@host:port?...` | 代理链接（支持全协议） |
| `TG_BOT_TOKEN` | ❌ | `123456:ABC...` | Telegram Bot Token |
| `TG_CHAT_ID` | ❌ | `987654321` | Telegram 用户/群组 Chat ID |

### 3. 启用 Actions

进入仓库的 **Actions** 页面，点击左侧工作流名称，然后点击 **Enable workflow** 启用。

### 4. 手动测试运行

在 Actions 页面点击 **Run workflow** → **Run workflow**，即可手动触发一次续期任务。

---

## 🛠️ 代理配置（可选但强烈建议）

JustRunMy 对 IP 风控较严，**强烈建议配置代理**。

### 支持的代理协议

| 协议 | 示例格式 |
|---|---|
| **VLESS** | `vless://uuid@host:port?security=tls&type=ws&path=/ws&sni=host` |
| **VMess** | `vmess://eyJhZGQiOiJob3N0IiwicG9ydCI6IjQ0MyIsImlkIjoidXVpZCJ9...` |
| **Hysteria2** | `hy2://password@host:port?sni=host&insecure=1` |
| **TUIC** | `tuic://uuid:password@host:port?sni=host&alpn=h3` |
| **SOCKS5** | `socks5://user:pass@host:port` |
| **HTTP** | `http://user:pass@host:port` |
| **HTTPS** | `https://user:pass@host:port` |

### 代理工作原理

1. GitHub Actions 运行环境安装 **sing-box**（v1.11.0）。
2. `proxy_handler.py` 解析 `PROXY_URL`，自动生成 `config.json`。
3. sing-box 在本地 `127.0.0.1:8080` 启动 HTTP 入站代理。
4. SeleniumBase 浏览器通过 `http://127.0.0.1:8080` 走代理访问 JustRunMy。

> 💡 如果不配置 `PROXY_URL`，脚本将尝试直连访问。

---

## 📅 定时执行

工作流默认配置为 **每天北京时间 06:00（UTC 22:00）** 自动执行：

```yaml
schedule:
  - cron: '0 22 * * *'
```

如需修改时间，编辑 `.github/workflows/renew.yml` 中的 cron 表达式。

---

## 📱 Telegram 通知格式

续期完成后，Telegram 会收到如下消息：

```
justrunmy.app 续期报告
🖥 你的应用名称
✅ 续期完成
⏱️ 剩余: 2 days 23 hours 59 minutes
时间: 2026-05-05 14:30:00
```

---

## ⚠️ 常见问题

### Q1: Actions 运行失败，提示 "未找到 JUSTRUNMY_EMAIL 或 JUSTRUNMY_PASSWORD"

请检查 Secrets 中是否正确配置了 `JUSTRUNMY_EMAIL` 和 `JUSTRUNMY_PASSWORD`，注意区分大小写。

### Q2: Turnstile 验证失败

- 通常是代理质量不佳或 Cloudflare 策略更新导致。
- 建议更换更稳定的 `PROXY_URL`。
- 可在 Actions 运行日志的 **Artifacts** 中下载 `debug-screenshots` 查看截图。

### Q3: 如何获取 Telegram Chat ID？

1. 在 Telegram 中搜索 `@userinfobot`
2. 发送 `/start`，机器人会返回你的 Chat ID
3. 如果是群组，将 Bot 加入群组后发送一条消息，访问 `https://api.telegram.org/bot<你的Token>/getUpdates` 查看 `chat.id`

### Q4: 支持多账号吗？

当前版本为单账号设计。如需多账号支持，建议参考 [zv201413/JustRunMy.App_Multi_Renew](https://github.com/zv201413/JustRunMy.App_Multi_Renew)。

---

## 📁 项目结构

```
.
├── .github/workflows/renew.yml   # GitHub Actions 工作流
├── justrunmy_renew.py            # 主续期脚本
├── proxy_handler.py              # 代理解析与 sing-box 配置生成
└── README.md                     # 本文件
```

---

## 🙏 特别鸣谢

- [zv201413/JustRunMy.App_Multi_Renew](https://github.com/zv201413/JustRunMy.App_Multi_Renew) — 提供 sing-box 代理方案与 Turnstile 物理点击算法
- [SagerNet/sing-box](https://github.com/SagerNet/sing-box) — 强大的代理工具
- [seleniumbase](https://github.com/seleniumbase/SeleniumBase) — 优秀的浏览器自动化框架

---

## 📜 License

MIT License
