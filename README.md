setup instruction for nginx-php-Codeigniter




/etc/php5/fpm/pool.d/www.conf
>>find below lines and set it like below "www-data" replaced by nginx
listen.owner = nginx
listen.group = nginx

sudo nano /etc/php5/fpm/php.ini

cgi.fix_pathinfo=0

>>This is an extremely insecure setting because it tells PHP to attempt to execute the closest file it can find if a PHP file does not match exactly. it is enabled"1" by default so set it to zero -> 0(zero)


sudo service php5-fpm restart
vim /etc/nginx/nginx.conf

>>add below lines..

include /etc/nginx/sites-enabled/*.conf;
include /etc/nginx/conf.d/*.conf;


vim /etc/nginx/sites-enabled/default.conf

server {
listen 80 default_server;
    listen [::]:80 default_server ipv6only=on;

    root /var/www/html/hottiehunter/public;
    index index.php index.html index.htm;

    server_name example.com;

 location ~* \.(ico|css|js|gif|jpe?g|png)(\?[0-9]+)?$ {
                expires max;
                log_not_found off;
        }

    location / {
    #    try_files $uri $uri/ =404;
    try_files $uri $uri/ /index.php;
    }

    error_page 404 /404.html;
    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root /usr/share/nginx/html;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/var/run/php5-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}




