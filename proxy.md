# Proxy

Inspiration and documentation are highly based on our friends at [SimpleAnalytics](https://docs.simpleanalytics.com/proxy). If you want to run all requests through a proxy on your side, you can do this by using a reverse proxy.

## Caddy

We love [Caddy](https://caddyserver.com/).

```
proxy.yourdomain.com {
    encode zstd gzip
    reverse_proxy https://www.captcha.eu {
        header_up Host www.captcha.eu
        header_up X-Caddy-Proxy "true"
        header_up -X-Forwarded-For
    }
}
```

## NGINX

But we also love NGINX.

```
server {
    listen 443 ssl;
    server_name proxy.yourdomain.com;

    ssl_certificate /path/to/ssl/certificate.crt;
    ssl_certificate_key /path/to/ssl/private.key;

    location / {
        proxy_pass https://www.captcha.eu;
        proxy_set_header Host "www.captcha.eu";
        proxy_set_header X-Real-IP "";
        proxy_set_header X-Forwarded-For "";
        # Additional proxy configuration options if needed
    }
}
```

If you want to use the AT only endpoint, use `at.captcha.at` as a reverse_proxy. This config eliminates the end-users' IP, which will limit bot protection to some extent, but it will still provide good protection.

## Use the Proxy

### Frontend

Integrate like normal - see: [Integration](install.md).

Make sure you set `KROT.KROT_HOST` to `proxy.yourdomain.com`.

```js
KROT.setup("xx-MY-PUBLIC-KEY");
KROT.KROT_HOST = "https://proxy.yourdomain.com";
```

### Backend

Integrate like normal - see: [Integration](install.md).

Use `https://proxy.yourdomain.com/validate` as an endpoint.

