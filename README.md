# Outrigger HTTPS Proxy + WebSocket

Just a simple fork of 

https://github.com/phase2/docker-https-proxy 

to support websocket proxying for URLs starting with `/websocket`.

nginx.xconf.templ has been extended to have

````
location ~ ".*websocket.*" {
    # Same as above
    {{ if ne $rate "0" -}}
    limit_req zone=flood burst={{ getenv "RATE_LIMIT_BURST_QUEUE" }} nodelay;
    {{ end -}}

    proxy_set_header        Host              {{ getenv "UPSTREAM_DOMAIN" }};
    proxy_set_header        X-Real-IP         $remote_addr;
    proxy_set_header        X-Forwarded-For   $proxy_add_x_forwarded_for;
    proxy_set_header        X-Forwarded-Proto $scheme;
    proxy_set_header        X-Forwarded-Host  {{ getenv "PROXY_DOMAIN"}};
    proxy_set_header        X-Forwarded-Port  443;

    proxy_pass http://{{ getenv "UPSTREAM_DOMAIN" }}:{{ getenv "UPSTREAM_PORT" "80" }};

    # Add websocket upgrade/connection header
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "Upgrade";
}
````
