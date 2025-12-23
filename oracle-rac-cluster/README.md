# Oracle RAC Cluster Quick Start

目的
--
本项目骨架旨在为在私有云（如 vSphere）或公有云上搭建 Oracle RAC（Real Application Clusters）提供可重复的部署流程与文档。

范围与建议工具
--
- 基础设施即代码：推荐使用 `Terraform`（或 vSphere SDK）进行网络和 VM 的可重复创建。 
- 配置管理与安装：建议使用 `Ansible` 编排 Oracle Grid Infrastructure、Oracle Database 和 RAC 的安装步骤。 
- 目标环境：vSphere（推荐） 或 支持的云平台（需按云平台调整资源/镜像）。

目录结构（初始）
--
- `ansible/` — playbook 与角色示例
- `terraform/` — 基础 infra 模板（占位）
- `scripts/` — 环境准备脚本示例
- `docs/architecture.md` — 架构与拓扑说明

快速开始（注意：此仓库为骨架，实际执行前请完善变量与凭据）
--
1. 阅读 `docs/architecture.md` 并准备目标环境访问权限。 
2. 使用 `terraform/` 创建基础网络与 VM（占位）。
3. 使用 `ansible/` 部署并配置 Grid Infrastructure 和 Oracle RAC。

后续我可以：
- 填充 `terraform/` 示例模块（vSphere provider）
- 添加 `ansible/` playbook 完整流程（自动化安装 Grid、DB 和集群配置）
- 提供测试与验证脚本

## 一键安装 Oracle RAC 脚本

### 前置条件（假设用户已满足）
- **操作系统**：RHEL 8.10，已注册并启用必要的 repo（BaseOS, AppStream, HighAvailability, ResilientStorage）。
- **虚拟机配置**：2 台 VM，每台 8 CPU, 32G RAM, 100G 系统盘。
- **网络**：
  - 公共网络：用于客户端访问（IP: node1: 192.168.1.101, node2: 192.168.1.102）。
  - 私有网络：用于集群内部通信（IP: node1: 10.0.0.101, node2: 10.0.0.102）。
  - VIP: node1-vip: 192.168.1.103, node2-vip: 192.168.1.104。
  - SCAN: rac-scan: 192.168.1.105-107 (DNS 配置）。
- **共享存储**：
  - ASM 磁盘：3 个共享磁盘（/dev/sdb, /dev/sdc, /dev/sdd），每个 50G，用于 DATA, FRA, OCR/VOTE。
  - 使用 iSCSI 或 vSphere RDM 配置共享磁盘。
- **安装文件**：所有 Oracle 安装文件放在 /tmp 下，包括：
  - LINUX.X64_193000_grid_home.zip
  - LINUX.X64_193000_db_home.zip
  - p6880880_190000_Linux-x86-64.zip (OPatch)
- **用户与权限**：root 用户 SSH 无密码访问所有节点，oracle/grid 用户已创建。
- **DNS/NTP**：DNS 配置 SCAN，NTP 同步。
- **防火墙**：禁用或开放必要端口（1521, 5500 等）。

### 脚本使用
运行 `ansible/playbooks/install_rac.yml`（需要 Ansible 安装）。

脚本将：
1. 配置主机名、hosts 文件。
2. 安装必要包（oracle-database-preinstall-19c）。
3. 配置 ASM 磁盘。
4. 安装 Grid Infrastructure。
5. 安装 Oracle Database 并创建 RAC 实例。


