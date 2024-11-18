---
title: 下載技術相關
date: "1999-12-31"
hideMeta: true
---

to be continued.

### Bittorrent 下載工具

#### Windows / macOS / Linux 桌面平臺

推薦使用 [qBittorrent](https://www.qbittorrent.org/download)，可以設置「工具 - 選項 - 連綫 - 代理伺服器」，配合 vpn 使用，下載和上傳更安全。

#### 網絡伺服器

在網絡伺服器、vps、nas 上部署遠端下載工具，也推薦 qBittorrent 的 Web UI 版，支持 [docker](https://hub.docker.com/r/linuxserver/qbittorrent) 部署。

- docker-compose.yml 其中 WEBUI_PORT 是管理界面的端口，TORRENTING_PORT 是在 bt 世界互傳文件用的端口，可以設置成其它端口。記得在 linux 防火墻（如 ufw）中開放端口。

```
---
services:
  qbittorrent:
    image: lscr.io/linuxserver/qbittorrent:latest
    container_name: qbittorrent
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - WEBUI_PORT=8080
      - TORRENTING_PORT=6881
    volumes:
      - /path/to/qbittorrent/appdata:/config
      - /path/to/downloads:/downloads #optional
    ports:
      - 8080:8080
      - 6881:6881
      - 6881:6881/udp
    restart: unless-stopped
```

- 在 nginx 設置 qBittorrent 管理界面的反向代理：

```
server {

    # 可以隱藏在任何域名的子路徑中
    location /any/subfolder/ {
        proxy_pass         http://127.0.0.1:8080/;
        proxy_http_version 1.1;

        # headers recognized by qBittorrent
        proxy_set_header Host $proxy_host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Host  $http_host;
        proxy_set_header X-Forwarded-Proto $scheme;

        # optionally, you can adjust the POST request size limit, to allow adding a lot of torrents at once
        # client_max_body_size 100M;

        proxy_cookie_path / "/; Secure";
    }
}
```

