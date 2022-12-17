---
title: Adding a new external site
description: 
published: 1
date: 2022-12-17T11:39:54.057Z
tags: 
editor: markdown
dateCreated: 2022-12-17T10:17:48.978Z
---

# Adding a new external site
How to add a new external facing site with authelia protection
## Cloudflare configuration
1. Sign into [Cloudflare](https://dash.cloudflare.com/login)
2. Navigate to the [DNS records](https://dash.cloudflare.com/d2df6a48421f6ec20bbc7dcd74228df3/steve-fisher.me/dns/records) for the steve-fisher.me domain
3. Add a new new CNAME record for the site you wish to proxy![cloudflare-dns-cname.png](/images/cloudflare-dns-cname.png)
## Nginx configuration
1. Sign into [Nginx Proxy Manager](http://192.168.0.2:7818/)
2. Select 'Add Proxy Host'
![nginx-add-proxy-host.png](/images/nginx-add-proxy-host.png)
3. Add the details of the internal site you wish to expose
a. **Domain Names:** The required external domain name - this should use a steve-fisher.me domain
b. **Scheme:** [select either http or https depending on the configuration of the internal site]
c. **Forward Hostname / IP:** The ip or hostname of the internal site
d. **Forward Port:** The port the internal site is running on
e. Turn on *Cache Assets*, *Block Common Exploits* and *Websockets Support* but experiment if any problems are experienced
f. Leave Access List set to Publicly Accessible
![nginx-edit-proxy-host-details.png](/images/nginx-edit-proxy-host-details.png)
4. Set the SSL Certificate to be *Cloudflare* and turn on both *Force SSL* and *HTTP/2 Support*
![nginx-edit-proxy-host-ssl.png.png](/images/nginx-edit-proxy-host-ssl.png.png)
5. In the advanced section add the following custom Nginx Configuration
```
location /authelia {
internal;
set $upstream_authelia http://192.168.0.2:9091/api/verify;
proxy_pass_request_body off;
proxy_pass $upstream_authelia;    
proxy_set_header Content-Length "";

# Timeout if the real server is dead
proxy_next_upstream error timeout invalid_header http_500 http_502 http_503;
client_body_buffer_size 128k;
proxy_set_header Host $host;
proxy_set_header X-Original-URL $scheme://$http_host$request_uri;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $remote_addr; 
proxy_set_header X-Forwarded-Proto $scheme;
proxy_set_header X-Forwarded-Host $http_host;
proxy_set_header X-Forwarded-Uri $request_uri;
proxy_set_header X-Forwarded-Ssl on;
proxy_redirect  http://  $scheme://;
proxy_http_version 1.1;
proxy_set_header Connection "";
proxy_cache_bypass $cookie_session;
proxy_no_cache $cookie_session;
proxy_buffers 4 32k;

send_timeout 5m;
proxy_read_timeout 240;
proxy_send_timeout 240;
proxy_connect_timeout 240;
}

location / {
set $upstream_app $forward_scheme://$server:$port;
proxy_pass $upstream_app;

auth_request /authelia;
auth_request_set $target_url https://$http_host$request_uri;
auth_request_set $user $upstream_http_remote_user;
auth_request_set $email $upstream_http_remote_email;
auth_request_set $groups $upstream_http_remote_groups;
proxy_set_header Remote-User $user;
proxy_set_header Remote-Email $email;
proxy_set_header Remote-Groups $groups;

error_page 401 =302 https://auth.steve-fisher.me/?rd=$target_url;

client_body_buffer_size 128k;

proxy_next_upstream error timeout invalid_header http_500 http_502 http_503;

send_timeout 5m;
proxy_read_timeout 360;
proxy_send_timeout 360;
proxy_connect_timeout 360;

proxy_set_header Host $host;
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection upgrade;
proxy_set_header Accept-Encoding gzip;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto $scheme;
proxy_set_header X-Forwarded-Host $http_host;
proxy_set_header X-Forwarded-Uri $request_uri;
proxy_set_header X-Forwarded-Ssl on;
proxy_redirect  http://  $scheme://;
proxy_http_version 1.1;
proxy_set_header Connection "";
proxy_cache_bypass $cookie_session;
proxy_no_cache $cookie_session;
proxy_buffers 64 256k;

set_real_ip_from 172.18.0.0/16;
set_real_ip_from 172.19.0.0/16;
real_ip_header CF-Connecting-IP;
real_ip_recursive on;

}
```

## Authelia configuration
1. Open [VSCode](http://192.168.0.2:8443/) and open /user/appdata//Authelia/configuration.yml
2. Add the site to the access_contol section of the file. As standard sites should be added to the domain list in the *## two factor login - admin* section![vscode-authelia-config.png](/home-lab/vscode-authelia-config.png)