# clash-verge-setting

**Language / 語言:** [English](README.md) | 繁體中文 | [简体中文](README.zh-CN.md)

適用於 [Clash Verge Rev](https://github.com/clash-verge-rev/clash-verge-rev) 的 [mihomo (Clash.Meta)](https://github.com/MetaCubeX/mihomo) 設定範本，透過 [IPRoyal](https://iproyal.com/?r=jackyyyso) 住宅代理對 Twitter/X 及境外 AI 服務進行分流，其餘流量一律直連。

---

## 功能說明

本設定實作**分流（Split-Tunnel）**策略：

| 流量目標 | 路由方式 |
|---|---|
| Twitter / X | IPRoyal 代理 |
| ChatGPT / OpenAI | IPRoyal 代理 |
| Claude / Anthropic | IPRoyal 代理 |
| Google Gemini | IPRoyal 代理 |
| Perplexity AI | IPRoyal 代理 |
| Microsoft Copilot | IPRoyal 代理 |
| 其餘所有流量 | 直連（不經過代理） |

只有需要代理的服務才會走 IPRoyal，本地及一般瀏覽流量完全不受影響。

---

## 前置需求

- 已安裝 **[Clash Verge Rev](https://github.com/clash-verge-rev/clash-verge-rev)**
- 擁有 **[IPRoyal](https://iproyal.com/?r=jackyyyso)** 帳號並訂閱住宅代理方案

---

## 教學步驟

### 步驟一：下載設定檔

**方法 A — 複製整個儲存庫：**
```bash
git clone https://github.com/<your-username>/clash-verge-setting.git
```

**方法 B — 直接下載：**

前往此 GitHub 儲存庫，點擊 `mihomo-template.yaml`，再按 **Raw** 按鈕後另存檔案。

---

### 步驟二：取得 IPRoyal 憑證

1. 登入 [IPRoyal 控制台](https://iproyal.com/?r=jackyyyso)
2. 前往 **Residential Proxies（住宅代理）→ Proxy List（代理清單）**
3. 選擇想要的國家／地區
4. 記錄以下五個資訊：
   - 代理伺服器主機名稱（例如 `geo.iproyal.com`）
   - HTTP 連接埠（例如 `12323`）
   - SOCKS5 連接埠（例如 `12324`）
   - 使用者名稱
   - 密碼

---

### 步驟三：編輯範本

以任意文字編輯器開啟 `mihomo-template.yaml`，替換五個佔位符：

| 佔位符 | 替換為 |
|---|---|
| `<IPROYAL_SERVER>` | 代理主機名稱，例如 `geo.iproyal.com` |
| `<HTTP_PORT>` | HTTP 連接埠號碼，例如 `12323` |
| `<SOCKS5_PORT>` | SOCKS5 連接埠號碼，例如 `12324` |
| `<USERNAME>` | 你的 IPRoyal 使用者名稱 |
| `<PASSWORD>` | 你的 IPRoyal 密碼 |

編輯後範例：
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

儲存檔案時可重新命名，例如 `mihomo-config.yaml`。

---

### 步驟四：匯入 Clash Verge Rev

1. 開啟 **Clash Verge Rev**
2. 點擊左側側欄的 **Profiles（設定檔）**
3. 點擊 **New（新增）→ Local（本機）**
4. 選取你編輯好的 YAML 檔案

也可以直接將 YAML 檔案**拖放**至 Profiles 頁面。

---

### 步驟五：啟用設定檔

1. 點擊匯入的設定檔卡片使其**啟用**（會以高亮顯示）
2. 前往 **Settings（設定）**，開啟以下其中一項：
   - **System Proxy（系統代理）**：代理瀏覽器及大部分應用程式流量
   - **TUN Mode（TUN 模式）**：透明劫持所有系統流量（推薦）

---

### 步驟六：驗證是否正常運作

1. 前往 [twitter.com](https://twitter.com) 或 [chatgpt.com](https://chatgpt.com)，應能正常載入
2. 在 Clash Verge Rev 中開啟 **Connections（連線）** 或 **Logs（記錄）** 頁籤
3. 確認對應網域的請求是透過 **Proxy** 群組路由

---

## 設定說明

| 區段 | 說明 |
|---|---|
| **TUN 模式** | `stack: mixed` — 透過虛擬網路介面透明劫持所有系統流量 |
| **DNS** | Fake-IP 模式；以阿里雲 DNS（`dns.alidns.com`）為主要伺服器防止 DNS 污染；Google/Cloudflare 為備援 |
| **代理群組** | `Proxy` — `fallback` 類型，優先嘗試 SOCKS5，無法連線時自動切換至 HTTP |
| **規則** | 對 Twitter/X 及 AI 服務使用 `DOMAIN-SUFFIX` 匹配 → Proxy；`MATCH,DIRECT` 兜底其餘流量 |

---

## 自訂設定

### 新增更多代理網域

編輯 YAML 中的 `rules:` 區段，在最後一行 `MATCH,DIRECT` **之前**新增：

```yaml
rules:
  # ... 現有規則 ...
  - DOMAIN-SUFFIX,example.com,Proxy   # 在此新增你的網域
  - MATCH,DIRECT
```

### 更換代理供應商

將 `proxies:` 下的項目替換為你的代理供應商資訊。如果新增或移除了代理名稱，也需同步調整 `proxy-groups:` 區段。

---

## 免責聲明

本設定檔僅供**個人使用及學習目的**。使用者需自行確保使用行為符合當地適用法律及各被代理平台的服務條款。
