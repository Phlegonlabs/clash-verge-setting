# clash-verge-setting

**Language / 语言:** [English](README.md) | [繁體中文](README.zh-TW.md) | 简体中文

适用于 [Clash Verge Rev](https://github.com/clash-verge-rev/clash-verge-rev) 的 [mihomo (Clash.Meta)](https://github.com/MetaCubeX/mihomo) 配置模板，通过 [IPRoyal](https://iproyal.com) 住宅代理对 Twitter/X 及境外 AI 服务进行分流，其余流量一律直连。

---

## 功能说明

本配置实现**分流（Split-Tunnel）**策略：

| 流量目标 | 路由方式 |
|---|---|
| Twitter / X | IPRoyal 代理 |
| ChatGPT / OpenAI | IPRoyal 代理 |
| Claude / Anthropic | IPRoyal 代理 |
| Google Gemini | IPRoyal 代理 |
| Perplexity AI | IPRoyal 代理 |
| Microsoft Copilot | IPRoyal 代理 |
| 其余所有流量 | 直连（不经过代理） |

只有需要代理的服务才会走 IPRoyal，本地及一般浏览流量完全不受影响。

---

## 前置要求

- 已安装 **[Clash Verge Rev](https://github.com/clash-verge-rev/clash-verge-rev)**
- 拥有 **[IPRoyal](https://iproyal.com)** 账号并订阅住宅代理套餐

---

## 教程步骤

### 第一步：下载配置文件

**方法 A — 克隆整个仓库：**
```bash
git clone https://github.com/<your-username>/clash-verge-setting.git
```

**方法 B — 直接下载：**

前往此 GitHub 仓库，点击 `mihomo-template.yaml`，再按 **Raw** 按钮后另存文件。

---

### 第二步：获取 IPRoyal 凭据

1. 登录 [IPRoyal 控制台](https://iproyal.com)
2. 前往 **Residential Proxies（住宅代理）→ Proxy List（代理列表）**
3. 选择所需的国家／地区
4. 记录以下五项信息：
   - 代理服务器主机名（例如 `geo.iproyal.com`）
   - HTTP 端口（例如 `12323`）
   - SOCKS5 端口（例如 `12324`）
   - 用户名
   - 密码

---

### 第三步：编辑模板

用任意文本编辑器打开 `mihomo-template.yaml`，替换五个占位符：

| 占位符 | 替换为 |
|---|---|
| `<IPROYAL_SERVER>` | 代理主机名，例如 `geo.iproyal.com` |
| `<HTTP_PORT>` | HTTP 端口号，例如 `12323` |
| `<SOCKS5_PORT>` | SOCKS5 端口号，例如 `12324` |
| `<USERNAME>` | 你的 IPRoyal 用户名 |
| `<PASSWORD>` | 你的 IPRoyal 密码 |

编辑后示例：
```yaml
proxies:
  - name: IPRoyal-HTTP
    type: http
    server: geo.iproyal.com
    port: 12323
    username: "myuser"
    password: "mypassword"

  - name: IPRoyal-SOCKS5
    type: socks5
    server: geo.iproyal.com
    port: 12324
    username: "myuser"
    password: "mypassword"
    udp: true
```

保存时可重命名，例如 `mihomo-config.yaml`。

---

### 第四步：导入 Clash Verge Rev

1. 打开 **Clash Verge Rev**
2. 点击左侧侧栏的 **Profiles（配置文件）**
3. 点击 **New（新建）→ Local（本地）**
4. 选择编辑好的 YAML 文件

也可以直接将 YAML 文件**拖放**到 Profiles 页面。

---

### 第五步：激活配置文件

1. 点击导入的配置文件卡片使其**激活**（高亮显示）
2. 前往 **Settings（设置）**，开启以下其中一项：
   - **System Proxy（系统代理）**：代理浏览器及大部分应用流量
   - **TUN Mode（TUN 模式）**：透明捕获所有系统流量（推荐）

---

### 第六步：验证是否正常工作

1. 访问 [twitter.com](https://twitter.com) 或 [chatgpt.com](https://chatgpt.com)，应能正常加载
2. 在 Clash Verge Rev 中打开 **Connections（连接）** 或 **Logs（日志）** 标签页
3. 确认对应域名的请求通过 **Proxy** 组路由

---

## 配置说明

| 区段 | 说明 |
|---|---|
| **TUN 模式** | `stack: mixed` — 通过虚拟网络接口透明捕获所有系统流量 |
| **DNS** | Fake-IP 模式；以阿里云 DNS（`dns.alidns.com`）为主要服务器防止 DNS 污染；Google/Cloudflare 为备用 |
| **代理组** | `Proxy` — `fallback` 类型，优先尝试 SOCKS5，不可用时自动切换至 HTTP |
| **规则** | 对 Twitter/X 及 AI 服务使用 `DOMAIN-SUFFIX` 匹配 → Proxy；`MATCH,DIRECT` 兜底其余流量 |

---

## 自定义配置

### 添加更多代理域名

编辑 YAML 中的 `rules:` 区段，在最后一行 `MATCH,DIRECT` **之前**添加：

```yaml
rules:
  # ... 现有规则 ...
  - DOMAIN-SUFFIX,example.com,Proxy   # 在此添加你的域名
  - MATCH,DIRECT
```

### 更换代理服务商

将 `proxies:` 下的条目替换为你的代理服务商信息。如果增减了代理名称，也需同步调整 `proxy-groups:` 区段。

---

## 免责声明

本配置文件仅供**个人使用及学习目的**。使用者需自行确保使用行为符合当地适用法律及各被代理平台的服务条款。
