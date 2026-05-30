# wstunnel-go

<p align="center">
  <img src="https://img.shields.io/badge/Go-1.18+-00ADD8?style=for-the-badge&logo=go" />
  <img src="https://img.shields.io/badge/License-Apache%202.0-blue?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Platform-Linux%20%7C%20Windows%20%7C%20macOS-green?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Architecture-x86%20%7C%20ARM%20%7C%20s390x-orange?style=for-the-badge" />
</p>

<p align="center">
<b>高性能、多模式、跨平台网络隧道解决方案</b>
</p>

---

## 📖 项目简介

**wstunnel-go** 是一款基于 **Go (Golang)** 重构开发的高性能网络隧道工具，同时保留轻量级 Python 备用版本。

项目专注于：

* 网络连接稳定性
* 高并发传输能力
* TCP / UDP 全协议支持
* 复杂网络环境穿透
* 多架构兼容部署

适用于：

* 海外云服务器
* IDC 服务器
* ARM 设备
* NAS
* 边缘计算节点
* IBM Z / LinuxONE（s390x）大型机平台

---

# ✨ 核心特性

## 🚀 高性能 Go 实现

采用原生 Go 编写：

* 单文件部署
* 无运行时依赖
* 静态编译
* 极低资源占用
* 毫秒级启动速度

适合长期高并发运行。

---

## ⚡ 高吞吐低延迟

基于 Go 网络模型优化：

* 非阻塞 I/O
* 高并发连接支持
* 多核心 CPU 利用
* 低内存占用

能够在复杂网络环境中保持稳定传输。

---

## 🔒 多模式传输支持

支持多种连接模式，可根据网络环境灵活选择。

| 模式           | 加密         | 说明         |
| ------------ | ---------- | ---------- |
| Direct       | ❌          | 纯直连模式，延迟最低 |
| Direct TLS   | TLS 1.3    | 加密传输       |
| HTTP Payload | 可选         | HTTP 流量伪装  |
| SNI Fronting | TLS + HTTP | 域名伪装与前置    |

---

## 🌐 TCP / UDP 全协议支持

配合：

**badvpn-udpgw**

实现：

* TCP 转发
* UDP 转发
* DNS 转发
* 游戏 UDP 流量支持

满足大部分网络应用需求。

---

## 📊 Web 管理面板

内置轻量级 Web 后台。

功能包括：

* 实时流量监控
* 在线配置管理
* 用户状态查看
* 节点运行状态监控
* 日志查看

无需额外安装数据库。

---

## 🖥️ 多架构支持

支持主流 CPU 架构：

| 架构               | 状态 |
| ---------------- | -- |
| x86_64           | ✅  |
| ARM64            | ✅  |
| ARMv7            | ✅  |
| s390x (LinuxONE) | ✅  |

特别针对：

* IBM LinuxONE
* IBM Z Mainframe

进行了兼容性优化。

---

# 🔧 传输模式说明

## Direct

特点：

* 无加密
* 性能最佳
* 延迟最低

适用于：

* 内网环境
* 专线环境
* 可信网络

---

## Direct TLS

特点：

* TLS 1.3 加密
* 防中间人攻击
* 数据安全性高

适用于：

* 公网服务器
* 云服务器

---

## HTTP Payload

特点：

* 模拟标准 HTTP 请求
* 支持特殊 Header
* 提高穿透能力

适用于：

* HTTP 白名单网络
* 企业代理环境

---

## SNI Fronting

特点：

* TLS + HTTP Payload
* 域名伪装
* 流量特征弱化

适用于：

* 复杂网络边界
* 多层代理环境

---

# 📦 安装部署

## 方案一：快速安装（推荐）

直接下载预编译版本。

```bash
curl -sSO https://raw.githubusercontent.com/xiaoguiday/xiyang110/main/go_wstunnel_mini.sh && bash go_wstunnel_mini.sh
```

### 自动完成

* 下载主程序
* 下载 badvpn-udpgw
* 安装管理面板
* 生成配置文件
* 注册 Systemd 服务
* 设置开机自启

---

## 方案二：源码编译安装

适用于：

* LinuxONE
* IBM Z
* ARM
* 自定义 CPU 优化

### 环境要求

* Go 1.18+
* Git

执行：

```bash
curl -sSO https://raw.githubusercontent.com/xiaoguiday/xiyang110/main/wsttunnel-go-install.sh && bash wsttunnel-go-install.sh
```

### 自动完成

* 克隆最新源码
* 本地编译
* CPU 架构优化
* 安装服务
* 启动服务

---

# 🖥️ Web 管理后台

安装完成后访问：

```text
http://服务器IP:9090/login.html
```

默认账号：

```text
admin
```

默认密码：

```text
@@123123@@
```

---

# 📂 默认安装目录

```text
/usr/local/bin/
├── wstunnel-go
├── badvpn-udpgw
├── config.json
├── traffic.json
└── web/
```

---

# 🔒 安全建议

## 修改默认密码

部署完成后请立即修改：

```text
/usr/local/bin/config.json
```

中的后台账号密码。

修改后重启服务：

```bash
systemctl restart wstunnel.service
```

---

## 保护配置文件

请勿公开以下文件：

```text
config.json
traffic.json
```

这些文件可能包含：

* 用户信息
* 节点配置
* 历史流量记录

---

## 防火墙建议

建议仅开放：

```text
隧道端口
9090 (管理面板)
```

并配置：

* Fail2Ban
* Cloudflare
* Nginx Reverse Proxy

进一步提升安全性。

---

# 📜 开源协议

本项目基于 Apache License 2.0 开源。

您可以：

✅ 使用
✅ 修改
✅ 二次开发
✅ 商业使用

但需保留原作者版权声明。

---

# ⭐ Star History

如果本项目对您有所帮助，欢迎点亮 Star 支持项目发展。

Thank You ❤️
