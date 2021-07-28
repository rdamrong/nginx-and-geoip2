# How to compile Geoip2 Dynamic Module for NGINX Open Source that installed in Ubuntu 20.04 LTS
0. Update, Upgrade and verify Ubuntu version
```
$ sudo apt update
$ sudo apt upgrade -y
$ cat /etc/os-release
NAME="Ubuntu"
VERSION="20.04.2 LTS (Focal Fossa)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 20.04.2 LTS"
VERSION_ID="20.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=focal
UBUNTU_CODENAME=focal
```
1. Install NGINX and check NGINX version by nginx -v
```
$ sudo apt install -y nginx
$ nginx -v
nginx version: nginx/1.18.0 (Ubuntu)
```
2. Install Development tool and required library
```
$ sudo apt install -y libmaxminddb0 libmaxminddb-dev mmdb-bin build-essential libpcre3-dev zlib1g-dev libssl-dev libxml2-dev libxslt-dev libgd-dev
```
3. Download the same NGINX source code that installed.
 ``` 
$ wget https://nginx.org/download/nginx-1.18.0.tar.gz
$ tar xvfz nginx-1.18.0.tar.gz
$ cd nginx-1.18.0
```
4. Download latest version ngx_http_geoip2_module source code from https://github.com/leev/ngx_http_geoip2_module
```
$ wget https://github.com/leev/ngx_http_geoip2_module/archive/refs/tags/3.3.tar.gz
$ tar xvfz 3.3.tar.gz
```
5. Find the original complie options by nginx -V and
```
$ nginx -V
nginx version: nginx/1.18.0 (Ubuntu)
built with OpenSSL 1.1.1f  31 Mar 2020
TLS SNI support enabled
configure arguments: --with-cc-opt='-g -O2 -fdebug-prefix-map=/build/nginx-KTLRnK/nginx-1.18.0=. -fstack-protector-strong -Wformat -Werror=format-security -fPIC -Wdate-time -D_FORTIFY_SOURCE=2' --with-ld-opt='-Wl,-Bsymbolic-functions -Wl,-z,relro -Wl,-z,now -fPIC' --prefix=/usr/share/nginx --conf-path=/etc/nginx/nginx.conf --http-log-path=/var/log/nginx/access.log --error-log-path=/var/log/nginx/error.log --lock-path=/var/lock/nginx.lock --pid-path=/run/nginx.pid --modules-path=/usr/lib/nginx/modules --http-client-body-temp-path=/var/lib/nginx/body --http-fastcgi-temp-path=/var/lib/nginx/fastcgi --http-proxy-temp-path=/var/lib/nginx/proxy --http-scgi-temp-path=/var/lib/nginx/scgi --http-uwsgi-temp-path=/var/lib/nginx/uwsgi --with-debug --with-compat --with-pcre-jit --with-http_ssl_module --with-http_stub_status_module --with-http_realip_module --with-http_auth_request_module --with-http_v2_module --with-http_dav_module --with-http_slice_module --with-threads --with-http_addition_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_image_filter_module=dynamic --with-http_sub_module --with-http_xslt_module=dynamic --with-stream=dynamic --with-stream_ssl_module --with-mail=dynamic --with-mail_ssl_module
```
6. Compile NGINX with the same option as it build with added Geoip2 module option  --add-dynamic-module=./ngx_http_geoip2_module-3.3
```
$ ./configure --add-dynamic-module=./ngx_http_geoip2_module-3.3 --with-cc-opt='-g -O2 -fdebug-prefix-map=/build/nginx-KTLRnK/nginx-1.18.0=. -fstack-protector-strong -Wformat -Werror=format-security -fPIC -Wdate-time -D_FORTIFY_SOURCE=2' --with-ld-opt='-Wl,-Bsymbolic-functions -Wl,-z,relro -Wl,-z,now -fPIC' --prefix=/usr/share/nginx --conf-path=/etc/nginx/nginx.conf --http-log-path=/var/log/nginx/access.log --error-log-path=/var/log/nginx/error.log --lock-path=/var/lock/nginx.lock --pid-path=/run/nginx.pid --modules-path=/usr/lib/nginx/modules --http-client-body-temp-path=/var/lib/nginx/body --http-fastcgi-temp-path=/var/lib/nginx/fastcgi --http-proxy-temp-path=/var/lib/nginx/proxy --http-scgi-temp-path=/var/lib/nginx/scgi --http-uwsgi-temp-path=/var/lib/nginx/uwsgi --with-debug --with-compat --with-pcre-jit --with-http_ssl_module --with-http_stub_status_module --with-http_realip_module --with-http_auth_request_module --with-http_v2_module --with-http_dav_module --with-http_slice_module --with-threads --with-http_addition_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_image_filter_module=dynamic --with-http_sub_module --with-http_xslt_module=dynamic --with-stream=dynamic --with-stream_ssl_module --with-mail=dynamic --with-mail_ssl_module
$ make modules
```
7. Copy ngx_http_geoip2_module.so and ngx_stream_geoip2_module.so to Shared NGINX module library
```
$ sudo cp -vi objs/ngx_http_geoip2_module.so /usr/share/nginx/modules/
$ sudo cp -vi objs/ngx_stream_geoip2_module.so /usr/share/nginx/modules/
```
8. Added load_module into /etc/nginx/nginx.conf and restart NGINX
```
$ sudo vi /etc/nginx/nginx.conf

user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;
#--- Add this 2 lines ----
load_module modules/ngx_http_geoip2_module.so;
load_module modules/ngx_stream_geoip2_module.so;
[...]

$ sudo systemctl restart nginx
```
9. Verify NGINX configuration and NGINX status
```
$ sudo nginx -T | grep geoip2
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
load_module modules/ngx_http_geoip2_module.so;
load_module modules/ngx_stream_geoip2_module.so;

$ sudo systemctl status nginx
● nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
     Active: active (running) since Wed 2021-07-28 11:10:53 +07; 1min 38s ago
       Docs: man:nginx(8)
    Process: 3109605 ExecStartPre=/usr/sbin/nginx -t -q -g daemon on; master_process on; (code=exited, status=0/SUCCESS)
    Process: 3109611 ExecStart=/usr/sbin/nginx -g daemon on; master_process on; (code=exited, status=0/SUCCESS)
   Main PID: 3109613 (nginx)
      Tasks: 3 (limit: 9510)
     Memory: 3.6M
     CGroup: /system.slice/nginx.service
             ├─3109613 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
             ├─3109614 nginx: worker process
             └─3109615 nginx: worker process

Jul 28 11:10:53 server200 systemd[1]: Starting A high performance web server and a reverse proxy server...
Jul 28 11:10:53 server200 systemd[1]: Started A high performance web server and a reverse proxy server.
```

#มูลค่าความสุข

