# Katabump Server Auto-Renewal Tool

基于 [eooce/katabump-renew](https://github.com/eooce/katabump-renew) 移植，使用 **SeleniumBase UC 模式** 绕过 Cloudflare Turnstile 验证码，支持 sing-box 全协议代理、多账号、Telegram 通知。

> 旧版 Playwright + CDP 方案因 `isTrusted=false` 问题无法绕过 Turnstile，已废弃。新版改用 SeleniumBase 内置的 `uc_gui_click_captcha()`，通过 PyAutoGUI 发送真实 X11 鼠标事件，`isTrusted=true`。

## 🚀 GitHub Actions 云端运行 (推荐)

### 1. Fork 本仓库

### 2. 配置 Secrets

进入 **Settings** → **Secrets and variables** → **Actions** → **New repository secret**

| Secret 名称 | 是否必填 | 说明 |
|-------------|----------|------|
| `USERS_JSON` | ✅ 必填 (多账号) | JSON 数组：`[{"username":"email@example.com","password":"pwd"}]` |
| `KATABUMP_EMAIL` | ✅ 必填 (单账号) | 单账号登录邮箱（与 `USERS_JSON` 二选一） |
| `KATABUMP_PASSWORD` | ✅ 必填 (单账号) | 单账号登录密码 |
| `PROXY_URL` | ❌ 可选 | 代理链接（vless/vmess/trojan/tuic/anytls/hysteria2/socks5） |
| `TG_BOT_TOKEN` | ❌ 可选 | Telegram Bot Token |
| `TG_CHAT_ID` | ❌ 可选 | Telegram Chat ID |

> **账号配置优先级**：如果同时设置了 `USERS_JSON` 和 `KATABUMP_EMAIL`，优先使用 `USERS_JSON`。

### 3. 运行

- **定时运行**：每天 UTC 0:00（北京时间 8:00）自动触发
- **手动运行**：进入 **Actions** → 选 `Katabump 自动续期` → **Run workflow**

### 4. 截图留存

每次运行（无论成功与否）通过 `Upload Screenshots` 步骤自动上传截图到 Artifacts。

## 🔧 代理说明

⚠️ Cloudflare Turnstile 对 IP 信誉有要求，**机房 IP 可能过不了**，建议用住宅代理。

支持的协议（与 v2rayN 兼容的节点链接）：
- `vless://uuid@server:port?security=reality&sni=...&type=ws&...`
- `vmess://base64encoded...`
- `trojan://password@server:port?sni=...&type=ws&...`
- `tuic://uuid:password@server:port...`
- `anytls://uuid@server:port...`
- `hysteria2://base64@server:port...`
- `socks5://user:pass@server:port` 或 `socks://user:pass@server:port`

## 🛠️ 项目结构

- `app.py`: 主程序（Python + SeleniumBase）
- `.github/workflows/renew.yml`: GitHub Actions 配置
- `proxy_handler.py`: (遗留) 旧版 sing-box 配置生成器，新版用 `setup_proxy.sh`

## 💻 本地运行 (可选)

```bash
# 安装依赖
sudo apt-get install -y xvfb x11-utils xdotool scrot fonts-noto-cjk
pip install seleniumbase requests
seleniumbase install chromedriver

# 设置环境变量
export USERS_JSON='[{"username":"your@email.com","password":"pwd"}]'
# 可选代理:
# export NODE_LINK='vless://...'
# bash <(wget -qO- https://main.ssss.nyc.mn/setup_proxy.sh)

# 运行
xvfb-run --auto-servernum --server-args="-screen 0 1920x1080x24" python3 app.py
```

## 📝 致谢

- [eooce/katabump-renew](https://github.com/eooce/katabump-renew) - 原始 SeleniumBase 方案
- [XCQ0607/katabump](https://github.com/XCQ0607/katabump) - 早期 Playwright 方案
