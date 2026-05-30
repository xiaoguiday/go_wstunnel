# wstunnel-go：高性能 Golang 网络隧道与传输解决方案

`wstunnel-go` 是一款基于 Go (Golang) 语言开发的高性能、轻量级网络隧道工具。项目针对 network 连接保障与传输效率进行了重构与深度优化，旨在为复杂、受限的网络环境提供稳定、安全、低延迟的 TCP/UDP 流量转发能力。

本项目原生支持多平台部署，适用于混合云互联、边缘节点通信以及安全通道构建等场景。

---

## ✨ 技术特性

*   **原生 Go 语言实现：** 零外部运行时依赖，支持单文件静态编译，具备极佳的跨平台部署能力（支持 x86、ARM 及 **s390x 大型机架构**）。
*   **低资源占用与高吞吐：** 采用非阻塞 I/O 设计，启动速度达毫秒级，内存与 CPU 占用极低，适合长期稳定的守护进程（Daemon）运行。
*   **多模式柔性传输：**
    *   **Direct / Direct TLS：** 支持标准的直连与 TLS 加密传输，保障数据在传输过程中的机密性与完整性。
    *   **HTTP Payload：** 模拟标准 HTTP 流量，用于穿透严格的七层协议防火墙。
    *   **SNI Fronting (TLS + HTTP Payload)：** 结合 SNI 前置技术，支持在复杂代理与中转场景下的精细化流量伪装。
*   **集成化运维面板：** 内置轻量级 Web 管理面板，支持流量监控、状态可视化与实时连接管理。
*   **网络协议全覆盖：** 搭配 `badvpn-udpgw` 组件，完整支持 TCP 与 UDP 双栈流量的隧道化传输。

---

## 🔧 系统架构与支持模式

| 传输模式 | 加密类型 | 适用场景 |
| :--- | :--- | :--- |
| **Direct** | 无加密 | 授信内部网络，追求极致传输吞吐 |
| **Direct TLS** | TLS 1.3 | 跨公网传输，需要防止中间人攻击与数据窃听 |
| **HTTP Payload** | 可选 | 绕过仅允许 HTTP 流量的特定网关限制 |
| **SNI Fronted** | TLS + 域名伪装 | 复杂网络边界穿透，降低流量特征识别率 |

## 📦 快速部署指南

本指南适用于 Debian / Ubuntu 等 Linux 发行版，采用标准化路径部署以确保系统服务的长期稳定运行。

### 方案一：自动化一键部署（推荐）

该脚本将程序、配置及 Web 模板统一分发至系统标准可执行路径 `/usr/local/bin`，并自动配置 Systemd 守护进程。

```bash
#!/bin/bash
set -e

echo "============================================="
echo "  开始部署 wstunnel-go & badvpn-udpgw 服务"
echo "============================================="

# 1. 基础依赖环境安装与临时目录创建
apt-get update && apt-get install -y unzip curl
mkdir -p ~/wstunnel_install_temp
cd ~/wstunnel_install_temp

# 2. 解压部署包（请确保 111.zip 已上传至当前用户主目录）
if [ -f "~/111.zip" ]; then
    unzip -o ~/111.zip -d .
else
    # 兼容当前目录下的压缩包
    unzip -o ../111.zip -d . || { echo "未找到 111.zip 部署包，请检查路径。"; exit 1; }
fi

# 3. 分发程序及资源文件至系统标准路径
echo "正在分发执行文件与配置模板..."
chmod +x wstunnel-go badvpn-udpgw
cp wstunnel-go badvpn-udpgw config.json admin.html login.html traffic.json /usr/local/bin/

# 4. 配置 Systemd 系统服务
echo "正在配置 Systemd 服务..."
if [ -f "wstunnel.service" ] && [ -f "udpgw.service" ]; then
    cp *.service /etc/systemd/system/
else
    echo "错误：部署包内未检测到 .service 配置文件"
    exit 1
fi

# 5. 启动服务并设置开机自启
systemctl daemon-reload
systemctl enable --now wstunnel.service udpgw.service

echo "============================================="
echo "             服务部署完成！"
echo "============================================="
echo "管理面板地址: http://\$(curl -s ifconfig.me):9090/login.html"
echo "默认管理账号: admin"
echo "默认初始化密码: @@123123@@"
echo "提示：建议在首次登录后立即修改默认配置文件中的账密信息。"
echo "============================================="

### 方案二：手动分发部署

如果您需要自定义配置，可按以下步骤手动部署：

1. **环境准备与解压：**

    apt update && apt install -y unzip
    unzip 111.zip -d ~/wstunnel_dist
    cd ~/wstunnel_dist

2. **二进制文件授权与路径归档：**
   将程序、配置文件及前端模板放置在同一目录下（推荐 `/usr/local/bin`），以确保 Web 管理面板的相对路径读取正常：

    chmod +x wstunnel-go badvpn-udpgw
    cp wstunnel-go badvpn-udpgw config.json admin.html login.html traffic.json /usr/local/bin/

3. **注册系统服务：**

    cp *.service /etc/systemd/system/
    systemctl daemon-reload
    systemctl enable --now wstunnel.service udpgw.service

---

## 🔒 安全与合规建议

1. **凭据修改：** 默认管理账号密码（`admin` / `@@123123@@`）仅用于初始化部署。生产环境部署后，请务必修改 `/usr/local/bin/config.json` 中的凭据信息并重启服务。
2. **数据清理：** 官方发布的二进制压缩包中不包含任何特定服务器的连接历史或私有密钥数据。请勿将包含历史运行数据的 `traffic.json` 或 `config.json` 直接分发给第三方。
