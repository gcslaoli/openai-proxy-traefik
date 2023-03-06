# openai-proxy-traefik

使用traefik作为openai的反向代理,解决openai的API在国内无法访问的问题

## 配置

克隆本项目

```bash
git clone https://github.com/gcslaoli/openai-proxy-traefik.git
```

修改 `config/traefik/dynamic.yml` ,将 `yourdomain.com` 替换为你的域名,注意有两处需要修改

```yaml
http:
  services:
    openai:
      loadBalancer:
        passHostHeader: false
        servers:
          - url: https://api.openai.com
  routers:
    openai:
      entryPoints:
        - websecure
      rule: "Host(`yourdomain.com`)"
      service: openai
      tls:
        certResolver: letsencrypt
        domains:
          - main: "yourdomain.com"
```

修改 `config/traefik/traefik.yml` ,将 `yourname@company.com` 替换为你的邮箱

```yaml
providers:
  file:
    filename: /etc/traefik/dynamic.yml
    watch: true

api:
  insecure: true
  dashboard: true

entryPoints:
  web:
    address: ":80"
  websecure:
    address: ":443"

certificatesResolvers:
  letsencrypt:
    acme:
      email: "yourname@company.com"
      storage: "/etc/traefik/acme.json"
      httpChallenge:
        entryPoint: web

log:
  level: WARN

accessLog: {}

```

## 运行

```bash
docker-compose up -d
```

## 使用

在openai的API中使用 `yourdomain.com` 代替 `api.openai.com` 即可