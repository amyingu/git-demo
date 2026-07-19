# Mihomo 代理快速参考

> 精简版使用指南，包含常用操作

---

## 🚀 快速开始

### 开启代理

```bash
proxy_on
```

### 关闭代理

```bash
proxy_off
```

### 检查代理状态

```bash
echo $http_proxy
# 输出: http://127.0.0.1:7890 表示已开启
# 输出空 表示已关闭
```

---

## 📊 查看当前节点

### 方法一：使用命令

```bash
clash-switch status
```

输出示例：
```
=== 当前代理组状态 ===
  Proxy: [S2] 🇯🇵日本东京3 | x0.1
  Auto: [S2] 🇺🇸美国凤凰城04 | 0.1x
  AI: [S1] 🇸🇬|新加坡-IEPL 02
```

### 方法二：使用 API

```bash
# 查看 Proxy 组当前节点
curl -s http://127.0.0.1:9091/proxies/Proxy | python3 -c "import sys,json; print(json.load(sys.stdin).get('now', 'N/A'))"

# 查看 Auto 组当前节点
curl -s http://127.0.0.1:9091/proxies/Auto | python3 -c "import sys,json; print(json.load(sys.stdin).get('now', 'N/A'))"
```

---

## 🔄 切换节点

### 列出所有节点

```bash
clash-switch list
```

输出示例：
```
=== 可用节点 ===
  1. Auto
  2. DIRECT
  3. [S1] 🇭🇰|香港-IEPL 01
  4. [S1] 🇭🇰|香港-IEPL 02
  ...
  50. [S1] 🇺🇸|美国-IEPL 01
  51. [S2] 🇺🇸美国凤凰城
  ...
```

**节点前缀说明：**
- `[S1]` - 来自 sub.yaml
- `[S2]` - 来自 sub2.yaml

### 切换节点

```bash
# 切换到指定节点
clash-switch '[S1] 🇭🇰|香港-IEPL 01'
clash-switch '[S2] 🇺🇸美国凤凰城'

# 切换到自动选择
clash-switch 'Auto'

# 切换到直连
clash-switch 'DIRECT'
```

### 测试节点延迟

```bash
clash-switch test '[S1] 🇭🇰|香港-IEPL 01'
```

---

## 🖥️ Web UI 使用

### 第一步：建立 SSH 反向代理

在**本地电脑**的终端执行：

```bash
ssh -L 9091:127.0.0.1:9091 gming@服务器IP
```

**保持终端打开**，不要关闭。

### 第二步：浏览器访问

打开浏览器，访问：

```
http://127.0.0.1:9091/ui/
```

**注意：** 必须带 `/ui/`（末尾斜杠）

### 第三步：使用 Web UI

| 功能 | 说明 |
|------|------|
| 🏠 代理 | 切换节点、查看状态 |
| 📊 概览 | 流量监控 |
| 📋 连接 | 当前连接列表 |
| 📝 日志 | 实时日志 |

### 关闭 SSH 隧道

直接关闭 SSH 终端窗口即可。

### 后台运行 SSH 隧道

```bash
ssh -fNL 9091:127.0.0.1:9091 gming@服务器IP
```

- `-f` - 后台运行
- `-N` - 不执行远程命令
- `-L` - 本地端口转发

### 常见问题

| 问题 | 解决方案 |
|------|----------|
| 显示 `{"hello":"mihomo"}` | 访问 `/ui/`（带斜杠） |
| 无法连接 | 检查 SSH 隧道是否建立 |
| 页面空白 | 强制刷新（Ctrl+F5） |

---

## 🛠️ 服务管理

### 启动/停止/重启

```bash
clash-ctl start    # 启动
clash-ctl stop     # 停止
clash-ctl restart  # 重启
clash-ctl status   # 状态
```

### 查看日志

```bash
clash-ctl log      # 实时日志
clash-ctl tail 50  # 最后 50 行
```

### 编辑配置

```bash
clash-ctl edit     # 编辑 config.yaml
```

---

## 📋 订阅管理

### 列出订阅

```bash
clash-switch-sub list
```

### 切换订阅

```bash
clash-switch-sub sub.yaml    # 切换到 sub.yaml
clash-switch-sub sub2.yaml   # 切换到 sub2.yaml
```

### 合并所有订阅

```bash
clash-switch-sub merge
```

### 更新订阅

```bash
clash-update '订阅链接'
```

---

## 📝 常用命令速查

| 命令 | 功能 |
|------|------|
| `proxy_on` | 开启代理 |
| `proxy_off` | 关闭代理 |
| `clash-ctl status` | 服务状态 |
| `clash-ctl restart` | 重启服务 |
| `clash-switch status` | 当前节点 |
| `clash-switch list` | 所有节点 |
| `clash-switch '节点名'` | 切换节点 |
| `clash-switch test '节点名'` | 测试延迟 |
| `clash-switch-sub list` | 订阅列表 |
| `clash-switch-sub merge` | 合并订阅 |
| `clash-update '链接'` | 更新订阅 |

---

## 💡 使用技巧

### 1. Git 操作

```bash
# 设置代理（推荐）
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy http://127.0.0.1:7890

# 取消代理
git config --global --unset http.proxy
git config --global --unset https.proxy
```

### 2. curl 测试

```bash
# 使用代理
curl -x http://127.0.0.1:7890 https://github.com

# 不使用代理
curl https://github.com
```

### 3. 检查代理是否工作

```bash
# 访问 GitHub
curl -s -o /dev/null -w "%{http_code}" https://github.com

# 访问百度
curl -s -o /dev/null -w "%{http_code}" https://www.baidu.com
```

### 4. 查看 mihomo 监听端口

```bash
ss -tlnp | grep -E "7890|9091"
```

---

## 🆘 故障排查

| 问题 | 解决方案 |
|------|----------|
| 代理不生效 | `clash-ctl status` 检查服务 |
| Web UI 无法访问 | 检查 SSH 隧道和 `/ui/` 路径 |
| 节点连接超时 | 切换其他节点或检查订阅 |
| GitHub 无法访问 | 确认规则中 GitHub 是 DIRECT |
| 配置文件错误 | `clash-ctl test` 验证配置 |

---

## 🛠️ 自定义命令说明

> 所有命令都是自己实现的，存放在 `~/.local/bin/` 或 `~/.bashrc` 中

### Shell 脚本命令

#### clash-ctl - 服务管理

**位置**：`~/.local/bin/clash-ctl`

**功能**：管理 mihomo 服务的启动、停止、重启等

**命令列表**：

| 子命令 | 功能 | 示例 |
|--------|------|------|
| `start` | 启动服务 | `clash-ctl start` |
| `stop` | 停止服务 | `clash-ctl stop` |
| `restart` | 重启服务 | `clash-ctl restart` |
| `status` | 查看状态 | `clash-ctl status` |
| `log` | 实时日志 | `clash-ctl log` |
| `tail [N]` | 查看最后 N 行 | `clash-ctl tail 50` |
| `test` | 测试配置 | `clash-ctl test` |
| `edit` | 编辑配置 | `clash-ctl edit` |

**实现原理**：
- 读取 PID 文件判断服务状态
- 使用 `nohup` 后台启动
- 日志输出到 `mihomo.log`

---

#### clash-switch - 节点切换

**位置**：`~/.local/bin/clash-switch`

**功能**：通过 mihomo API 切换代理节点

**命令列表**：

| 子命令 | 功能 | 示例 |
|--------|------|------|
| `status` | 查看当前节点 | `clash-switch status` |
| `list` | 列出所有节点 | `clash-switch list` |
| `<节点名>` | 切换到指定节点 | `clash-switch '🇭🇰\|香港-IEPL 01'` |
| `test <节点名>` | 测试节点延迟 | `clash-switch test '节点名'` |
| `help` | 显示帮助 | `clash-switch help` |

**实现原理**：
- 调用 mihomo RESTful API（端口 9091）
- `GET /proxies/<group>` 查看节点
- `PUT /proxies/<group>` 切换节点
- `GET /proxies/<node>/delay` 测试延迟

---

#### clash-switch-sub - 订阅管理

**位置**：`~/.local/bin/clash-switch-sub`

**功能**：管理多个订阅文件

**命令列表**：

| 子命令 | 功能 | 示例 |
|--------|------|------|
| `list` | 列出所有订阅 | `clash-switch-sub list` |
| `<订阅文件>` | 切换到指定订阅 | `clash-switch-sub sub.yaml` |
| `merge` | 合并所有订阅 | `clash-switch-sub merge` |
| `help` | 显示帮助 | `clash-switch-sub help` |

**实现原理**：
- 调用 `auto_parse.py` 解析订阅
- 调用 `merge_config.py` 合并配置
- 修复 GitHub 直连规则
- 重启服务

---

#### clash-update - 订阅更新

**位置**：`~/.local/bin/clash-update`

**功能**：一键下载并更新订阅

**使用方法**：
```bash
clash-update '订阅链接'
```

**实现原理**：
1. 下载订阅到 `sub.yaml`
2. 调用 `auto_parse.py` 解析
3. 调用 `merge_config.py` 合并
4. 修复 GitHub 直连规则
5. 重启 mihomo 服务

---

### Shell 函数

#### proxy_on - 开启代理

**位置**：`~/.bashrc`（函数定义）

**功能**：设置环境变量开启代理

**实现代码**：
```bash
proxy_on() {
    export http_proxy=http://127.0.0.1:7890
    export https_proxy=http://127.0.0.1:7890
    export HTTP_PROXY=http://127.0.0.1:7890
    export HTTPS_PROXY=http://127.0.0.1:7890
    export no_proxy=localhost,127.0.0.1,::1,.cn
    echo "代理已开启: http://127.0.0.1:7890"
}
```

**特性**：
- 设置 HTTP 和 HTTPS 代理
- 设置 no_proxy 排除本地和国内网站
- 新终端自动调用（在 ~/.bashrc 末尾）

---

#### proxy_off - 关闭代理

**位置**：`~/.bashrc`（函数定义）

**功能**：清除代理环境变量

**实现代码**：
```bash
proxy_off() {
    unset http_proxy https_proxy HTTP_PROXY HTTPS_PROXY no_proxy NO_PROXY
    echo "代理已关闭"
}
```

---

### Python 脚本

#### auto_parse.py - 智能解析订阅

**位置**：`~/.config/mihomo/auto_parse.py`

**功能**：解析订阅文件，支持多种格式和协议

**支持的格式**：
- Base64 编码链接
- 纯链接格式
- Clash YAML 格式

**支持的协议**：
- Trojan
- VMess
- Shadowsocks (SS)
- Hysteria2
- VLESS

**使用方法**：
```bash
# 使用默认订阅文件
python3 auto_parse.py

# 指定订阅文件
python3 auto_parse.py /path/to/sub.yaml
```

---

#### merge_subscriptions.py - 合并订阅

**位置**：`~/.config/mihomo/merge_subscriptions.py`

**功能**：合并多个订阅的节点，不去重，添加来源标识

**特性**：
- 保留所有节点（不去重）
- 节点名添加 `[S1]` / `[S2]` 前缀
- 统计协议分布

**使用方法**：
```bash
# 1. 解析各订阅
python3 auto_parse.py sub.yaml
cp proxies.yaml proxies_sub1.yaml
python3 auto_parse.py sub2.yaml
cp proxies.yaml proxies_sub2.yaml

# 2. 合并
python3 merge_subscriptions.py
```

---

#### merge_config.py - 合并配置

**位置**：`~/.config/mihomo/merge_config.py`

**功能**：将解析后的节点合并到主配置文件

**使用方法**：
```bash
python3 merge_config.py
```

---

#### rebuild_proxies.py - 重建 proxies

**位置**：`~/.config/mihomo/rebuild_proxies.py`

**功能**：从 `proxies.yaml` 重建 `config.yaml` 的 proxies 部分

**解决的问题**：
- 修复 YAML 缩进问题
- 确保 config.yaml 中的节点名与 proxies.yaml 一致

**使用方法**：
```bash
python3 rebuild_proxies.py
```

---

#### update_groups.py - 更新代理组

**位置**：`~/.config/mihomo/update_groups.py`

**功能**：自动更新代理组，添加所有节点

**生成的代理组**：
- **Proxy** - 包含所有节点（按 S1、S2 顺序）
- **Auto** - 自动测速组（30 个推荐节点）
- **AI** - AI 服务专用组

**使用方法**：
```bash
python3 update_groups.py
```

---

### 命令对比

| 命令 | 类型 | 作用范围 | 是否需要重启服务 |
|------|------|----------|------------------|
| `clash-ctl` | Shell 脚本 | 服务管理 | - |
| `clash-switch` | Shell 脚本 | 节点切换 | 否（热切换） |
| `clash-switch-sub` | Shell 脚本 | 订阅管理 | 是 |
| `clash-update` | Shell 脚本 | 订阅更新 | 是 |
| `proxy_on` | Shell 函数 | 终端代理 | 否 |
| `proxy_off` | Shell 函数 | 终端代理 | 否 |
| `auto_parse.py` | Python 脚本 | 解析订阅 | 否 |
| `merge_subscriptions.py` | Python 脚本 | 合并节点 | 否 |
| `merge_config.py` | Python 脚本 | 合并配置 | 否 |
| `rebuild_proxies.py` | Python 脚本 | 重建配置 | 否 |
| `update_groups.py` | Python 脚本 | 更新代理组 | 否 |

---

### 文件位置总结

| 工具 | 路径 |
|------|------|
| clash-ctl | `~/.local/bin/clash-ctl` |
| clash-switch | `~/.local/bin/clash-switch` |
| clash-switch-sub | `~/.local/bin/clash-switch-sub` |
| clash-update | `~/.local/bin/clash-update` |
| proxy_on/off | `~/.bashrc`（函数） |
| auto_parse.py | `~/.config/mihomo/auto_parse.py` |
| merge_subscriptions.py | `~/.config/mihomo/merge_subscriptions.py` |
| merge_config.py | `~/.config/mihomo/merge_config.py` |
| rebuild_proxies.py | `~/.config/mihomo/rebuild_proxies.py` |
| update_groups.py | `~/.config/mihomo/update_groups.py` |

---

## 📂 文件位置

| 文件 | 路径 |
|------|------|
| 主配置 | `~/.config/mihomo/config.yaml` |
| GEOIP 数据库 | `~/.config/mihomo/geoip.metadb` |
| GEOSITE 数据库 | `~/.config/mihomo/geosite.dat` |
| 订阅文件 | `~/.config/mihomo/sub*.yaml` |
| 日志文件 | `~/.config/mihomo/mihomo.log` |
| mihomo 二进制 | `~/.local/bin/mihomo` |

---

## 🔑 关键信息

| 项目 | 值 |
|------|-----|
| 代理端口 | `7890` |
| 控制端口 | `9091` |
| Web UI | `http://127.0.0.1:9091/ui/` |
| 配置文件 | `~/.config/mihomo/config.yaml` |
| mihomo 版本 | v1.19.27 |
| 节点总数 | 144（142 节点 + Auto + DIRECT） |
| 协议支持 | Trojan, VMess, SS, Hysteria2, VLESS |
