# Sing-box 多协议一键部署脚本

一个强大的 Sing-box 自动化部署工具，支持SS/HY2/TUIC/VLESS Reality/AnyTLS Reality 协议自选部署和线路机 VLESS Reality 中转的完整解决方案。

---

## ✨ 主要特性

### 🎯 部署机功能

- ✅ **一键安装** - 自动部署 Sing-box 最新服务端
- ✅ **自动生成** - 自动生成 密钥和配置文件，Reality 自选或默认SNI
- ✅ **多系统支持** - 支持 Alpine, Debian, Ubuntu, CentOS, RHEL, Fedora 等操作系统
- ✅ **开机自启** - 自动配置 Systemd / OpenRC 开机自启，崩溃自动拉起服务端
- ✅ **连接 IP** - 自动获取公网 IP 或手动输入 连接IP/DDNS域名 并生成客户端链接
- ✅ **管理工具** - 输入 sb 指令进入管理界面查看节点链接、重置端口、服务端控制查看等功能

### 🔗 线路机功能

- ✅ **一键生成** - 从落地机直接生成线路机安装脚本
- ✅ **Reality 入站** - 自动部署 VLESS Reality 入站
- ✅ **灵活端口** - 支持自动寻找空闲端口或手动指定
- ✅ **流量转发** - 自动转发流量到落地机SS节点
- ✅ **完整链接** - 生成可用的 VLESS Reality 客户端链接

## 🙏 特别鸣谢以下商家对本项目的赞助支持

<div align="center">

<table>
  <tr>
    <td align="center" width="220">
      <a href="https://app.kaze.network/" target="_blank">
        <img src="https://app.kaze.network/templates/lagom2/assets/img/logo/logo_big.634794647.svg" width="100" alt="Kaze" />
        <br><sub><b>Kaze</b></sub>
      </a>
    </td>
    <td align="center" width="220">
      <a href="https://console.alice.sh/" target="_blank">
        <img src="https://console.alice.sh/assets/images/logo-yellow.svg" width="100" alt="AliceNetworks" />
        <br><sub><b>AliceNetworks</b></sub>
      </a>
    </td>
    <td align="center" width="220">
      <a href="https://lxc.lazycat.wiki" target="_blank">
        <img src="https://lxc.lazycat.wiki/upload/logo2.png" width="100" alt="懒猫云" />
        <br><sub><b>懒猫云</b></sub>
      </a>
    </td>
    <td align="center" width="220">
      <a href="https://www.lxc.wiki/" target="_blank">
        <img src="https://www.lxc.wiki/themes/web/starvm-phj/img/logo.png" width="100" alt="拼好鸡" />
        <br><sub><b>拼好鸡</b></sub>
      </a>
    </td>

  </tr>
</table>

</div>

## ✅ 一键部署命令

安装全功能 sing-box：

```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/caigouzi121380/singbox-deploy/main/install-singbox-yyds.sh)"
```

## 📦 脚本说明

- `install-singbox-yyds.sh`：主入口，支持多协议部署、`sb` 管理面板和线路机脚本生成。
- `install-singbox.sh`：基础版，仅用于简单 Shadowsocks 部署场景。

## 🔧 安装后管理

安装完成后会创建 `sb` 快捷管理命令，可用于：

- 查看协议链接
- 查看或编辑配置文件
- 重置各协议端口
- 启动、停止、重启和查看服务状态
- 更新 sing-box
- 生成线路机安装脚本
- 卸载 sing-box

常用服务命令：

```bash
sb
systemctl status sing-box
journalctl -u sing-box -f
```

Alpine/OpenRC 系统使用：

```bash
sb
rc-service sing-box status
tail -f /var/log/sing-box.log
```

## 🧪 Docker Alpine 验证

语法检查：

```bash
docker run --rm -v "$PWD:/work:ro" -w /work alpine:latest sh -c 'apk add --no-cache bash >/dev/null && bash -n install-singbox-yyds.sh && bash -n install-singbox.sh'
```

在 Alpine 容器中实际跑一次最小 SS 部署：

```bash
docker run --rm -i -v "$PWD:/work:ro" -w /work alpine:latest sh -c 'mkdir -p /run/openrc && touch /run/openrc/softlevel && apk add --no-cache bash >/dev/null && printf "\n1\n\n\n\n" | bash install-singbox-yyds.sh'
```

验证安装产物和 `sb` 面板：

```bash
docker run --rm -i -v "$PWD:/work:ro" -w /work alpine:latest sh -c 'mkdir -p /run/openrc && touch /run/openrc/softlevel && apk add --no-cache bash >/dev/null && printf "\n1\n\n\n\n" | bash install-singbox-yyds.sh >/tmp/install.log && sing-box check -c /etc/sing-box/config.json && test -x /usr/local/bin/sb && printf "1\n0\n" | sb'
```

说明：Docker 容器不是完整 init 环境，OpenRC 可能输出 `hwdrivers`、`machine-id` 或 cgroup 相关警告；只要脚本最终提示部署完成、`sing-box check` 通过、`sb` 可生成链接，即表示容器验证通过。

## ⚠️ 注意事项

- 脚本需要 root 权限运行。
- 脚本会写入 `/etc/sing-box`、系统服务文件和 `/usr/local/bin/sb`。
- 请确保服务器防火墙或安全组已放行所选协议端口。
- 线路机脚本依赖落地机已部署 SS 协议作为出口。
