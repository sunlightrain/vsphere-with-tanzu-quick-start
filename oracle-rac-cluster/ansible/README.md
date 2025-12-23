# Ansible

此目录将存放 Ansible playbooks 与 roles，用于在目标 VM 上安装并配置 Oracle Grid Infrastructure 与 Oracle Database（RAC）。

示例流程（待填充）
1. 准备主机（用户、SSH、所需包）
2. 配置共享存储挂载点（ASM）
3. 安装 Grid Infrastructure
4. 配置 ASM 磁盘组
5. 安装 Oracle Database 软件
6. 创建 RAC 实例并验证

## 一键安装 RAC Playbook

### 使用方法
1. 安装 Ansible：`pip install ansible`
2. 配置 inventory 文件（ansible/inventory/hosts），包含 2 个节点。
3. 复制响应文件到 /tmp 或 playbook 指定的位置。
4. 运行 playbook：`ansible-playbook playbooks/install_rac.yml -i inventory/hosts`

### 注意事项
- 确保所有前置条件满足（见项目 README）。
- 密码在响应文件中硬编码，生产环境请使用 Ansible Vault。
- 脚本为示例，可能需要根据实际环境调整。
