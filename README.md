# clash-verge-setting

**Language / 語言:** English | [繁體中文](README.zh-TW.md) | [简体中文](README.zh-CN.md)

A [mihomo (Clash.Meta)](https://github.com/MetaCubeX/mihomo) configuration template for [Clash Verge Rev](https://github.com/clash-verge-rev/clash-verge-rev) that split-tunnels Twitter/X and overseas AI services through an [IPRoyal](https://iproyal.com) residential proxy while sending all other traffic direct.

---

## What This Does

This config implements a **split-tunnel** policy:

| Traffic | Route |
|---|---|
| Twitter / X | IPRoyal Proxy |
| ChatGPT / OpenAI | IPRoyal Proxy |
| Claude / Anthropic | IPRoyal Proxy |
| Google Gemini | IPRoyal Proxy |
| Perplexity AI | IPRoyal Proxy |
| Microsoft Copilot | IPRoyal Proxy |
| Everything else | DIRECT (no proxy) |

Only the services you need proxied go through IPRoyal — your local and general browsing traffic is unaffected.

---

## Prerequisites

- **[Clash Verge Rev](https://github.com/clash-verge-rev/clash-verge-rev)** installed on your system
- An **[IPRoyal](https://iproyal.com)** account with an active Residential Proxies subscription

---

## Tutorial

### Step 1: Download the Config

**Option A — Clone the repo:**
```bash
git clone https://github.com/<your-username>/clash-verge-setting.git
```

**Option B — Download directly:**

Go to this repository on GitHub, click `mihomo-template.yaml`, then click the **Raw** button and save the file.

---

### Step 2: Get Your IPRoyal Credentials

1. Log in to your [IPRoyal dashboard](https://iproyal.com)
2. Navigate to **Residential Proxies → Proxy List**
3. Select your desired country/location
4. Note down the following five values:
   - Proxy server hostname (e.g. `geo.iproyal.com`)
   - HTTP port (e.g. `12323`)
   - SOCKS5 port (e.g. `12324`)
   - Username
   - Password

---

### Step 3: Edit the Template

Open `mihomo-template.yaml` in any text editor and replace the five placeholders:

| Placeholder | Replace with |
|---|---|
| `<IPROYAL_SERVER>` | Your proxy hostname, e.g. `geo.iproyal.com` |
| `<HTTP_PORT>` | HTTP port number, e.g. `12323` |
| `<SOCKS5_PORT>` | SOCKS5 port number, e.g. `12324` |
| `<USERNAME>` | Your IPRoyal username |
| `<PASSWORD>` | Your IPRoyal password |

Example after editing:
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

Save the file with a new name, e.g. `mihomo-config.yaml`.

---

### Step 4: Import into Clash Verge Rev

1. Open **Clash Verge Rev**
2. Click **Profiles** in the left sidebar
3. Click **New** → **Local**
4. Select your edited YAML file

Alternatively, you can **drag and drop** the YAML file directly onto the Profiles page.

---

### Step 5: Activate the Profile

1. Click on the imported profile card to set it as **active** (it will be highlighted)
2. Go to **Settings** and enable either:
   - **System Proxy** — proxies browser and most app traffic
   - **TUN Mode** — captures all system traffic transparently (recommended)

---

### Step 6: Verify It's Working

1. Visit [twitter.com](https://twitter.com) or [chatgpt.com](https://chatgpt.com) — they should load successfully
2. In Clash Verge Rev, go to the **Connections** or **Logs** tab
3. Confirm that requests to those domains are routing through the **Proxy** group

---

## Configuration Overview

| Section | Details |
|---|---|
| **TUN Mode** | `stack: mixed` — captures all system traffic transparently via a virtual network interface |
| **DNS** | Fake-IP mode; Alibaba DNS (`dns.alidns.com`) as primary to avoid DNS pollution; Google/Cloudflare as fallback |
| **Proxy Groups** | `Proxy` — a `fallback` group that tries SOCKS5 first, falls back to HTTP if SOCKS5 is unreachable |
| **Rules** | `DOMAIN-SUFFIX` matching for Twitter/X and AI services → Proxy; `MATCH,DIRECT` catches everything else |

---

## Customization

### Add More Domains to Proxy

Edit the `rules:` section in your YAML and add lines before the final `MATCH,DIRECT` line:

```yaml
rules:
  # ... existing rules ...
  - DOMAIN-SUFFIX,example.com,Proxy   # add your domain here
  - MATCH,DIRECT
```

### Change Proxy Provider

Replace the entries under `proxies:` with your provider's server details. Adjust the `proxy-groups:` section if you add or remove proxy names.

---

## Disclaimer

This configuration is provided for **personal use and educational purposes only**. You are responsible for ensuring your usage complies with applicable local laws and the terms of service of any proxied platforms.
