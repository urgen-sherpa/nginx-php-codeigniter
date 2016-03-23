 nginx php-fpm wordpress centos 6.x
nginx 1.8
php-fpm 5.3
mysql 5.5
cpu core = 1 , memory = 2G

vim /etc/nginx/nginx.conf
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;
    server_tokens off;
    sendfile        on;
    tcp_nopush     on;

    keepalive_timeout  10;

    #gzip  on;
    # Gzip on
gzip on;
gzip_min_length  1100;
gzip_buffers  4 32k;
gzip_types    text/plain application/x-javascript text/xml text/css;

ignore_invalid_headers on;
client_max_body_size    20m;
client_body_buffer_size 15m;
client_header_timeout  400;
client_body_timeout 400;
send_timeout     400;
connection_pool_size  256;
client_header_buffer_size 4k;
large_client_header_buffers 4 32k;
request_pool_size  4k;
output_buffers   4 32k;
postpone_output  1460;

# Cache most accessed static files
open_file_cache          max=10000 inactive=10m;
open_file_cache_valid    2m;
open_file_cache_min_uses 1;
open_file_cache_errors   on;

    include /etc/nginx/conf.d/*.conf;
}

vim /etc/nginx/conf.d/default.conf
server {
    listen       80;
    server_name  enayapatrika.com;

     root   /var/www/html/enayapatrika.com/public_html;
     index  index.php index.html index.htm;

    location / {
     root   /var/www/html/enayapatrika.com/public_html;
     index  index.php index.html index.htm;
     try_files $uri $uri/ /index.php?$args;
    }
   location ~ \.php$ {
               root   /var/www/html/enayapatrika.com/public_html;
               try_files $uri =404;
               include fastcgi_params;
               fastcgi_pass unix:/var/run/php5-fpm.sock ;
               fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
               fastcgi_index index.php;
        }
   location ~* ^.+.(jpg|jpeg|gif|css|png|js|ico|html|xml|txt)$ {
        access_log        off;
        expires           max;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    location ~ /\.ht {
        deny  all;
    }
}


/etc/php-fpm.d/www.conf
listen = /var/run/php5-fpm.sock
listen.owner = nginx
listen.group = nginx
listen.mode = 0666



vim /etc/my.cnf
[mysqld]
....

# Activate query cache
query_cache_limit=2M
query_cache_size=64M
query_cache_type=1

# Max number of connections
max_connections=400

# Reduce timeouts
interactive_timeout=30
wait_timeout=30
connect_timeout=10

 

 

https://stavrovski.net/blog/install-and-configure-nginx-mysql-php-fpm-in-centos-6

https://www.scalescale.com/tips/nginx/improving-wordpress-performance-nginx-php-fpm-mysql-memcached-w3-total-cache/ 


