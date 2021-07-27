# nginx-and-geoip2

check NGINX version by nginx -v 


wget https://nginx.org/download/nginx-1.18.0.tar.gz
tar xvfz nginx-1.18.0.tar.gz
cd nginx-1.18.0

wget http://nginx.org/download/nginx-1.21.1.tar.gz
tar xvfz nginx-1.21.1.tar.gz
cd nginx-1.21.1/

sudo apt-get update
sudo apt install libmaxminddb0 libmaxminddb-dev mmdb-bin
sudo apt-get install build-essential
sudo apt-get install libpcre3-dev zlib1g-dev libssl-dev libxml2-dev libxslt-dev libgd-dev



check latest version https://github.com/leev/ngx_http_geoip2_module and download it
 wget https://github.com/leev/ngx_http_geoip2_module/archive/refs/tags/3.3.tar.gz
 tar xvfz 3.3.tar.gz

find the original complie options by nginx -V and add --add-dynamic-module=./ngx_http_geoip2_module-3.3


 ./configure --add-dynamic-module=./ngx_http_geoip2_module-3.3 --with-cc-opt='-g -O2 -fdebug-prefix-map=/build/nginx-KTLRnK/nginx-1.18.0=. -fstack-protector-strong -Wformat -Werror=format-security -fPIC -Wdate-time -D_FORTIFY_SOURCE=2' --with-ld-opt='-Wl,-Bsymbolic-functions -Wl,-z,relro -Wl,-z,now -fPIC' --prefix=/usr/share/nginx --conf-path=/etc/nginx/nginx.conf --http-log-path=/var/log/nginx/access.log --error-log-path=/var/log/nginx/error.log --lock-path=/var/lock/nginx.lock --pid-path=/run/nginx.pid --modules-path=/usr/lib/nginx/modules --http-client-body-temp-path=/var/lib/nginx/body --http-fastcgi-temp-path=/var/lib/nginx/fastcgi --http-proxy-temp-path=/var/lib/nginx/proxy --http-scgi-temp-path=/var/lib/nginx/scgi --http-uwsgi-temp-path=/var/lib/nginx/uwsgi --with-debug --with-compat --with-pcre-jit --with-http_ssl_module --with-http_stub_status_module --with-http_realip_module --with-http_auth_request_module --with-http_v2_module --with-http_dav_module --with-http_slice_module --with-threads --with-http_addition_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_image_filter_module=dynamic --with-http_sub_module --with-http_xslt_module=dynamic --with-stream=dynamic --with-stream_ssl_module --with-mail=dynamic --with-mail_ssl_module




./configure --add-dynamic-module=./ngx_http_geoip2_module-3.3  --with-stream
make modules

sudo rm -f /usr/share/nginx/modules/ngx_http_geoip2_module.so
sudo cp -vi objs/ngx_http_geoip2_module.so /usr/share/nginx/modules/
 cp -vi objs/ngx_stream_geoip2_module.so /usr/share/nginx/modules/


