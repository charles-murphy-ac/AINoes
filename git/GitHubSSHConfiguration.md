# GitHub SSH 连接问题解决指南

本文总结了在使用 SSH 地址克隆 GitHub
仓库时，常见的连接问题及对应解决方案，包括 SSH 密钥配置与强制走 VPN 443
端口的高级配置。

------------------------------------------------------------------------

## 一、配置 SSH 密钥（若使用 SSH 地址）

### 🔍 问题原因

当使用 SSH 地址（如 `git@github.com:用户名/仓库名.git`）克隆 GitHub 仓库时，若本地 SSH 密钥未添加至 GitHub 账号，会被拒绝连接，导致克隆失败。

### 🧩 解决方法

#### 1. 检查本地是否已有 SSH 密钥

打开终端（Windows 用 PowerShell，Mac/Linux 用 Terminal），输入：

``` bash
ls -la ~/.ssh
```

-   若存在 `id_rsa` 和 `id_rsa.pub` 文件，说明已有密钥。
-   否则需要生成新密钥。

#### 2. 生成新的 SSH 密钥

``` bash
ssh-keygen -t rsa -C "your_github_email@example.com"
```

执行后按提示一路回车即可。

#### 3. 复制公钥内容

根据操作系统选择对应命令：

**Windows:**

``` bash
cat ~/.ssh/id_rsa.pub | clip
```

**Mac:**

``` bash
pbcopy < ~/.ssh/id_rsa.pub
```

**Linux:**

``` bash
xclip -sel clip < ~/.ssh/id_rsa.pub
```

#### 4. 将公钥添加到 GitHub

1.  登录 GitHub\
2.  点击头像 → **Settings** → **SSH and GPG keys**\
3.  点击 **New SSH key**\
4.  粘贴公钥内容并保存

#### 5. 测试 SSH 连接

``` bash
ssh -T git@github.com
```

出现以下提示即表示配置成功：

    Hi username! You've successfully authenticated...

------------------------------------------------------------------------

## 二、强制 SSH 走 VPN 并使用 443 端口（核心方案）

### 🎯 适用场景

即使已开启 VPN，若 SSH 仍被防火墙或网络拦截，可通过配置让 SSH 使用
GitHub 支持的 **443 端口** 并强制走 VPN 隧道。

### 🛠️ 配置步骤

#### 1. 编辑 SSH 配置文件

-   **Windows:**\
    `C:\Users\<你的用户名>\.ssh\config`
-   **Mac/Linux:**\
    `~/.ssh/config`

若文件不存在可手动创建。

#### 2. 添加以下配置

``` config
Host github.com
  Hostname ssh.github.com  # GitHub 443 端口 SSH 入口
  Port 443                 # 强制使用 443 端口（HTTPS 通常不被拦截）
  ProxyCommand nc -X 5 -x 127.0.0.1:1080 %h %p  # 强制通过 VPN 代理
```

> **说明：** - `127.0.0.1:1080` 是 VPN 的本地代理端口\
> - 可在 VPN 软件的「本地代理设置」中查看\
> - 常见端口号包括：1080、1086、7890、7891 等\
> - 协议应选择 **SOCKS5**

#### 3. 测试 SSH 连接

``` bash
ssh -T git@github.com
```

若成功出现：

    Hi username! You've successfully authenticated...

则表示 SSH 已正确走 VPN 隧道并可正常连接。

------------------------------------------------------------------------

## ✅ 总结

-   若无法通过 SSH 克隆仓库，请先确认 **SSH 密钥** 是否配置正确。\
-   若密钥无误但连接仍失败，可通过 **SSH 443 端口 + VPN 隧道**
    的方式绕过网络限制。\
-   上述方法可稳定解决大多数 GitHub SSH 连接问题。
