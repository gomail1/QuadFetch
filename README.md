<div align="center">

# QuadFetch

**本地优先的多协议下载器 · Flutter 界面 + Go 引擎**

![platform](https://img.shields.io/badge/platform-Windows%2010%2F11%20x64-0078D4?logo=windows)

![go](https://img.shields.io/badge/Go-1.27-00ADD8?logo=go\&logoColor=white)

![flutter](https://img.shields.io/badge/Flutter-3.47-02569B?logo=flutter\&logoColor=white)

![version](https://img.shields.io/badge/version-1.0.21-orange)

![license](https://img.shields.io/badge/license-Proprietary-red)

HTTP/HTTPS · FTP · BitTorrent · 磁力链 —— 无账号、无遥测、全本地运行

</div>

---

## 📖 简介

QuadFetch 是一款**本地优先**的桌面下载器：Go 语言编写的下载引擎 + Flutter 构建的现代界面，  
两者通过 `127.0.0.1` 本地 API 通信。

- **引擎与界面分离**：界面可以随时关闭重开，下载任务在引擎里不受影响
- **全本地运行**：不注册、不登录、无遥测、无云端依赖
- **一套代码多端形态**：Windows 现已可用，Android 与 macOS 版在计划开发中

## ✨ 功能特性

### 下载内核

- **HTTP / HTTPS**：多线程分段下载（默认 8 线程，可按任务调整）、断点续传、自动重定向跟随、Cookie 支持
- **FTP**：主动/被动模式、控制与数据连接均支持代理
- **BitTorrent / 磁力链**：DHT 发现节点、多文件选择下载、做种开关、上传限速
- **任务控制**：暂停 / 继续 / 删除、并发任务数控制、全局与单任务限速、启动时自动恢复任务
- **完成动作**：无操作 / 全部完成后关机 / 休眠，可自动打开下载目录

### 网络出口

- **三种模式**：直连网络 / 代理网络（HTTP 或 SOCKS5，支持账号密码）/ 跟随系统（环境变量）
- **真实生效**：配置即时作用于新建连接，无需重启引擎
- **一键测试**：内置出口 IP 检测，直连与走代理的结果一目了然
- 说明：BT 的 tracker / webseed 走代理，P2P 对等连接始终直连（组件能力边界）

### 网盘与浏览器集成

- **内置 OpenList 网盘服务**：以 sidecar 方式随引擎启动，多网盘文件浏览后一键建任务
- **浏览器接管扩展**（Chrome / Edge MV3）：网页下载自动推送到 QuadFetch、右键菜单下载、  
  可配置跳过规则，支持磁力链（详见下方「🔌 浏览器扩展」）

### 桌面体验

- **系统托盘**：左键显示主界面；右键菜单（显示主窗口 / 新建下载 / 设置 / 检查更新 / 退出）
- **关闭行为可选**：最小化到托盘或退出；单实例运行
- **主题**：浅色 / 深色 / 跟随系统，6 套主题色（海洋蓝 / 薄荷绿 / 琥珀橙 / 珊瑚红 / 暮光紫 / 暗黑），  
  标题栏、任务栏与托盘图标跟随主题变色
- **增量热更新**：版本清单 ed25519 签名校验 + 按文件 sha256 比对 + 内容寻址增量下载，  
  新版本推送后无需重装（详见下文「增量热更新」）

## 🏗️ 架构

```
┌───────────────────────────┐     127.0.0.1:17888      ┌───────────────────────────┐
│  Flutter 界面 (Windows)    │ ◄── REST + WebSocket ──► │  Go 下载引擎 quadfetchd    │
│  任务列表 / 设置 / 网盘     │                          │                           │
└───────────────────────────┘                          │  ├─ HTTP(S) 多线程下载    │
        │                                              │  ├─ FTP                   │
        │ sidecar                                      │  ├─ BitTorrent / 磁力链   │
        ▼                                              │  └─ 自更新模块            │
┌───────────────────────────┐                          └───────────────────────────┘
│  OpenList (网盘服务)       │                                        │
└───────────────────────────┘                          settings.json / 任务记录
```

- 引擎只监听 `127.0.0.1`，不对外网开放
- 界面启动时探测引擎健康状态，未运行则自动拉起引擎进程

## 📥 下载安装

前往 [**Releases**](https://github.com/gomail1/QuadFetch/releases) 页面，或直接下载：

| 文件 | 说明 |
|---|---|
| [QuadFetch-Setup-1.0.21.exe](https://github.com/gomail1/QuadFetch/releases/download/quadfetch/QuadFetch-Setup-1.0.21.exe) | 安装版，安装向导 + 开始菜单快捷方式 |
| [QuadFetch-1.0.21-portable.zip](https://github.com/gomail1/QuadFetch/releases/download/quadfetch/QuadFetch-1.0.21-portable.zip) | 便携版，解压即用，不写注册表 |

**系统要求**：Windows 10 / 11（x64）

## 🔌 浏览器扩展

Chrome / Edge（Chromium 内核）MV3 扩展，把浏览器里的下载自动接管到 QuadFetch。

**功能**

- 浏览器下载 HTTP/HTTPS/FTP 链接时自动拦截，推送到 QuadFetch
- 默认行为可选：立即下载 / 仅添加
- 推送后自动取消浏览器原生下载（可关闭），浏览器不保留下载记录
- 右键菜单「用 QuadFetch 下载此链接」，支持磁力链
- 跳过规则（支持 `*` `?` 通配符）、推送成功后系统通知

**安装**

1. 下载 [QuadFetch.zip 浏览器扩展包](https://github.com/gomail1/QuadFetch/releases/download/quadfetch/QuadFetch.zip) 并解压
2. 打开 `chrome://extensions/`（Edge 为 `edge://extensions/`）
3. 打开右上角「开发者模式」→ 点「加载已解压的扩展程序」→ 选择解压出的文件夹
4. 确保 QuadFetch 正在运行（引擎监听 `127.0.0.1:17888`）

**使用**

- 点扩展图标：快速开关接管、切换「立即下载 / 仅添加」
- 点「设置」进入完整配置页（跳过规则等）

> 扩展仅与 `127.0.0.1:17888` 的本地引擎通信，不访问任何第三方服务。

## ⚙️ 配置与数据

所有数据都在引擎的数据目录（默认 `data/`）：

| 文件 / 目录          | 内容                     |
| ---------------- | ---------------------- |
| `settings.json`  | 下载目录、并发数、限速、做种、网络出口等设置 |
| `tasks*.json`    | 任务记录与状态（重启后自动恢复）       |
| `bt-store/`      | BT 任务数据                |

## 🔄 增量热更新

QuadFetch 内置自更新能力：

1. 客户端拉取版本清单（含每个文件的 sha256 与 ed25519 签名）
2. **签名校验失败直接拒绝**，杜绝被篡改的清单
3. 与本地文件逐个比对 sha256，只下载有差异的文件（内容寻址，跨版本去重）
4. 引擎组件停机热替换、界面组件在重启时由助手脚本完成替换，全程任务记录不受影响

## 🔒 安全与隐私

- 引擎 API 仅监听 `127.0.0.1`，不监听外网
- 无遥测、无统计上报、无内置推广
- 下载连接内置 SSRF 防护（阻断内网 / 回环 / 保留地址，防 DNS rebinding）
- 热更新清单经 ed25519 签名校验，文件逐一 sha256 校验

## 🗺️ 路线图

- [ ] Android 版（引擎移植 + 前台 Service，Flutter 界面复用）
- [ ] macOS 版
- [ ] 下载队列优先级与计划任务

## 💬 问题反馈

使用中遇到 Bug 或有功能建议，欢迎提交 [Issue](../../issues)。  
描述时请附上：操作系统版本、QuadFetch 版本号（设置 → 关于 可查）、复现步骤。

## 📄 许可证

**© 2026 gomail1 · 保留所有权利。**

本软件为专有软件，未授权的复制、反编译、二次分发均不被允许；  
安装包内附的第三方组件依其原始许可发布（安装目录下的 `THIRD_PARTY_LICENSES.txt`）。

## ⚠️ 免责声明

本工具仅提供下载能力。使用 BitTorrent 等功能下载内容时，请遵守所在地区的法律法规，  
勿用于下载、分发任何侵权或违法内容，由此产生的一切责任由使用者自行承担。
