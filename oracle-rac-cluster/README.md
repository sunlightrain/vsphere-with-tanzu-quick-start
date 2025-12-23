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
