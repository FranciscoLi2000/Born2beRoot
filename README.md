# Born2beRoot

这个 **Born2beRoot 项目** 旨在通过配置和管理一个安全的 Linux 服务器，帮助你掌握以下 **系统管理核心技能** 和 **虚拟化技术实践**：

---

### **核心学习目标**
1. **虚拟化与操作系统部署**
- 使用 **VirtualBox** 或 **UTM** 创建虚拟机，安装 **Debian** 或 **Rocky Linux**，理解虚拟化技术的基本原理。
- 配置磁盘分区（使用 **LVM** 和加密），掌握逻辑卷管理（LVM）和磁盘加密（如 `cryptsetup`）的核心操作。

2. **系统安全与网络配置**
- 配置防火墙（**UFW** 或 **firewalld**），仅开放指定端口（如 SSH 的 `4242`），限制非必要流量。
- 强化 SSH 安全：禁用 root 登录、修改默认端口、使用密钥认证，理解安全通信的基础。
- 实施 **SELinux**（Rocky）或 **AppArmor**（Debian）强制访问控制，保护系统资源。

3. **用户与权限管理**
- 创建用户并分配权限（`sudo` 组），设置强密码策略（密码复杂度、过期时间、历史记录限制）。
- 审计 `sudo` 使用：记录命令日志到 `/var/log/sudo/`，限制可执行路径，增强权限管理透明度。

4. **自动化与监控脚本**
- 编写 **Bash 脚本**（`monitoring.sh`），定时收集系统信息（CPU、内存、磁盘、网络等），通过 `cron` 和 `wall` 实现周期性报告。
- 掌握系统状态监控工具（如 `ss`、`df`、`free`），理解 `/proc` 文件系统的关键作用。

5. **服务配置与维护**
- 配置最小化服务安装，避免图形界面（如禁止安装 X.org），优化服务器性能与安全。
- 理解系统启动流程（如 `systemd`），管理服务依赖与启动顺序。

---

### **关键技术点解析**
#### 1. LVM 与加密分区配置
```bash
	# 创建加密分区
	cryptsetup luksFormat /dev/sdX
	cryptsetup open /dev/sdX encrypted_volume

	# 设置 LVM
	pvcreate /dev/mapper/encrypted_volume
	vgcreate vg0 /dev/mapper/encrypted_volume
	lvcreate -n lv_root -L 10G vg0
	mkfs.ext4 /dev/vg0/lv_root
```
**关键点**：通过 LVM 实现灵活的磁盘空间管理，结合加密保护敏感数据。

#### 2. 强密码策略配置（`/etc/login.defs` 和 `pam_pwquality`）
```bash
	# 密码复杂度要求
	password requisite pam_pwquality.so retry=3 minlen=10 ucredit=-1 lcredit=-1 dcredit=-1 maxrepeat=3
	# 密码有效期
	PASS_MAX_DAYS 30
	PASS_MIN_DAYS 2
	PASS_WARN_AGE 7
```
**关键点**：通过 PAM 模块和全局策略文件强制实施安全标准。

#### 3. 监控脚本实现（`monitoring.sh`）
```bash
	#!/bin/bash
	# 获取 CPU 核心数
	physical_cores=$(grep "physical id" /proc/cpuinfo | sort | uniq | wc -l)
	virtual_cores=$(grep -c "processor" /proc/cpuinfo)

	# 获取内存使用率
	mem_total=$(free -m | awk '/Mem:/ {print $2}')
	mem_used=$(free -m | awk '/Mem:/ {print $3}')
	mem_percent=$((mem_used * 100 / mem_total))

	# 输出到所有终端
	wall "Architecture: $(uname -a)
	CPU Physical: $physical_cores
	vCPU: $virtual_cores
	Memory Usage: ${mem_used}MB/${mem_total}MB ($mem_percent%)"
```
**关键点**：利用 `/proc` 和系统工具提取信息，通过 `cron` 定时任务实现自动化。

---

### **工程实践与思维提升**
1. **防御式配置**
- 禁用 root SSH 登录（`PermitRootLogin no`），限制攻击面。
- 配置防火墙仅允许必要端口（`ufw allow 4242/tcp`），防止未授权访问。

2. **日志与审计**
- 记录所有 `sudo` 操作到 `/var/log/sudo/`，便于事后审计。
- 监控系统日志（`journalctl`），快速定位异常事件。

3. **版本控制与备份**
- 定期生成虚拟机快照，防止配置错误导致系统不可用。
- 使用 `git` 管理配置文件和脚本，确保变更可追溯。

---

### **项目隐喻与深层价值**
- **“最小化安装是安全的基石”**：通过精简服务降低攻击面，理解安全与功能的平衡。
- **“自动化是运维的核心”**：通过脚本和定时任务减少重复劳动，提升效率。
- **“虚拟化是云时代的入门技能”**：掌握虚拟机管理，为后续学习容器化（Docker/Kubernetes）打下基础。

---

### **通关要点**
1. **分步实施**
- 先完成必做部分（系统安装、分区、SSH、防火墙），再处理附加任务（如 WordPress 配置）。
- 使用 `virt-manager` 或 `VBoxManage` 管理虚拟机，熟悉命令行工具。

2. **严格测试**
- 验证防火墙规则（`ufw status`）、SSH 连接（`ssh -p 4242 user@host`）、密码策略（`chage -l user`）。
- 测试监控脚本的定时执行和输出准确性。

3. **文档与备份**
- 记录所有配置步骤（如分区方案、防火墙规则），便于复查和答辩。
- 备份虚拟机签名文件（`signature.txt`），避免评估失败。

---

通过此项目，你将从“普通用户”进阶为“系统管理员”，掌握企业级 Linux 服务器的配置与管理能力，为运维、DevOps 或网络安全领域的职业发展奠定坚实基础。
