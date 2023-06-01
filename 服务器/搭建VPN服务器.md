# v2搭建教程

搭建这个就是非常简单的，首先需要一个服务器 推荐简云互联的 然后使用ssh工具连接上 安装被别人加速脚本，其次进行程序安装即可。教程开始随便**选择一台服务器** 推荐法国美国的 这边就不演示怎么购买了 购买完成直接打开ssh工具。

够买完 我们打开ssh工具，然后进行添加服务器，然后保存并且链接。

连接上我们输入bbr加速脚本，在回车即可接下来我们开始安装，我们输入脚本然后回车 选择我们要安装的 然后等他一步步安装到最后面安装完成即可：

```shell
1.安装BBR脚本命令：wget -N --no-check-certificate "https://raw.githubusercontent.com/chiakge/Linux-NetSpeed/master/tcp.sh" && chmod +x tcp.sh && ./tcp.sh
```

```shell
2.安装V2脚本命令：bash <(curl -Ls https://raw.githubusercontent.com/vaxilu/x-ui/master/install.sh)

如果不行，则输入以下备用指令：
source <(curl -sL https://multi.netlify.com/v2ray.sh) --zh
```

V2脚本安装完成后可以通过`ip:port` 访问管理界面，比如这里给出的地址：

![image-20220121185901852](https://s2.loli.net/2022/01/21/1ExUluYfIeBp5wR.png)



> 如果这里出现无法访问的话，就是属于服务器没有开放端口权限，就是用接下来的命令开放对于端口：

```shell
开端口：firewall-cmd --add-port=端口/tcp --permanent
载入端口：firewall-cmd --reload
查看端口:firewall-cmd --query-port=端口
```

配置好之后的V2ray 节点信息：

```json
[root@VM-0-12-centos system]# cat /etc/v2ray/config.json
{
  "log": {
    "access": "/var/log/v2ray/access.log",
    "error": "/var/log/v2ray/error.log",
    "loglevel": "info"
  },
  "inbounds": [
    {
      "port": 9534,
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "7895a34c-7b70-11ec-841e-525400dea82f",
            "alterId": 0
          }
        ]
      },
      "streamSettings": {
        "network": "kcp",
        "security": "none",
        "tlsSettings": {},
        "tcpSettings": {},
        "httpSettings": {},
        "kcpSettings": {
          "mtu": 1350,
          "tti": 50,
          "uplinkCapacity": 100,
          "downlinkCapacity": 100,
          "congestion": false,
          "readBufferSize": 2,
          "writeBufferSize": 2,
          "header": {
            "type": "srtp"
          }
        },
        "wsSettings": {},
        "quicSettings": {}
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    },
    {
      "protocol": "blackhole",
      "settings": {},
      "tag": "blocked"
    }
  ],
  "routing": {
    "rules": [
      {
        "type": "field",
        "ip": [
          "0.0.0.0/8",
          "10.0.0.0/8",
          "100.64.0.0/10",
          "169.254.0.0/16",
          "172.16.0.0/12",
          "192.0.0.0/24",
          "192.0.2.0/24",
          "192.168.0.0/16",
          "198.18.0.0/15",
          "198.51.100.0/24",
          "203.0.113.0/24",
          "::1/128",
          "fc00::/7",
          "fe80::/10"
        ],
        "outboundTag": "blocked"
      }
    ]
  }
}
```

```shell
Group: A
IP: 42.193.55.146
Port: 9534
TLS: 关闭
UUID: 7895a34c-7b70-11ec-841e-525400dea82f
Alter ID: 0
Network: kcp srtp

vmess://eyJ2IjogIjIiLCAicHMiOiAiIiwgImFkZCI6ICI0Mi4xOTMuNTUuMTQ2IiwgInBvcnQiOiA5NTM0LCAiYWlkIjogMCwgInR5cGUiOiAic3J0cCIsICJuZXQiOiAia2NwIiwgInBhdGgiOiAiIiwgImhvc3QiOiAiIiwgImlkIjogIjc4OTVhMzRjLTdiNzAtMTFlYy04NDFlLTUyNTQwMGRlYTgyZiIsICJ0bHMiOiAibm9uZSJ9

Tip: 同一Group的节点传输方式,端口配置,TLS等设置相同

```

