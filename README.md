# VPS-V2ray-Warp

[Reference](https://github.com/Alvin9999/new-pac)

Guide to build a proxy server with VPS and Cloudfare warp.

A VPS is the core of a proxy server. For most cases (eg. Youtube), VPS is enough to cross over GFW. Websites like ChatGPT and Netflix will block famous ISP, in which case raw IP is needed. That's why warp is used here and Cloudfare is a free one and easy to deploy thanks to out-of-box shell scripts.

## VPS

1. Buy a VPS (LA and Debian Operating System is preferred)
   [Vultr](https://www.vultr.com/)

2. Deploy server (Bitvise is recommended)

## Cloudflare Warp

### Install

[Cloudflare WARP Installer](https://github.com/P3TERX/warp.sh)

```sh
# use warp without downloading
bash <(curl -fsSL git.io/warp.sh) [SUBCOMMAND]
# or download and use it locally
wget git.io/warp.sh
bash warp.sh [SUBCOMMAND]
```

### Usage

```sh
# IPv6 is preferred, because IPv4 is used by a lot of people. There is a high possibility that it is banned by some websites.
bash warp.sh wg6
```

## [V2ray](https://www.v2ray.com/)

### Install

```sh
source <(curl -sL https://multi.netlify.app/v2ray.sh) --zh
```

### Speed Booster

1. install

```sh
wget -N --no-check-certificate "https://raw.githubusercontent.com/chiakge/Linux-NetSpeed/master/tcp.sh"

chmod +x tcp.sh

./tcp.sh
```

2. open bbr

```sh
# BBR as an example
bash tcp.sh 4
```

### Usage

```
v2ray
3: change v2ray settings automatically (or change it from config file)
4: get vmess link
```

### Config

location: /etc/v2ray/config.json

```json
{
  "log": {
    "access": "/var/log/v2ray/access.log",
    "error": "/var/log/v2ray/error.log",
    "loglevel": "info"
  },
  // v2ray general settings, generated automatically by V2ray
  "inbounds": [
    {
      "port": 19157,
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "9749e4ec-91d6-486c-b81f-b3f6560c84a1",
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
            "type": "wechat-video"
          }
        },
        "wsSettings": {},
        "quicSettings": {}
      }
    }
  ],
  // outbonds rules
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    },
    {
      "protocol": "blackhole",
      "settings": {},
      "tag": "block"
    },
    // socks warp for IPv4, not used in this case, just for notes
    {
      "protocol": "socks",
      "tag": "warp",
      "settings": {
        "servers": [
          {
            "address": "127.0.0.1",
            "port": 40000,
            "users": []
          }
        ]
      }
    },
    //  Strategy for domain name resolution. Options are:
    // "AsIs": Default value. Resolve domain name by system.
    // "UseIP": Use internal DNS for domain name resolution.
    // "UseIPv4": Use IPv4 address only, after resolved by internal DNS.
    // "UseIPv6": Use IPv6 address only, after resolved by internal DNS.
    {
      "tag": "IP6_out",
      "protocol": "freedom",
      "settings": {
        "domainStrategy": "UseIPv6"
      }
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
        "outboundTag": "block"
      },
      // Domains are specified here since warp may slow your network speed and you do not want all connections get through warp
      {
        "type": "field",
        "domain": ["openai.com", "ai.com", "netflix.com"],
        "outboundTag": "IP6_out"
      }
    ]
  }
}
```

## Note

1. VPS needs to restart every month
2. warp server has to open manually after VPS restarts
