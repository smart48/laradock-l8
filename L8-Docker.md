# L8 Docker README

Locally we use port 9090 for http and 4433 for https . HTTPS has local issue at the moment and only http loads. This is as we have not set up HTTPS locally and commmented the conf block out in the Nginx configuration

```shell
server {

    listen 80 default_server;
    listen [::]:80 default_server ipv6only=on;

    # For https
    # listen 443 ssl default_server;
    # listen [::]:443 ssl default_server ipv6only=on;
    # ssl_certificate /etc/nginx/ssl/default.crt;
    # ssl_certificate_key /etc/nginx/ssl/default.key;

    server_name localhost;
    root /var/www/public;
    index index.php index.html index.htm;

    location / {
         try_files $uri $uri/ /index.php$is_args$args;
    }

    location ~ \.php$ {
        try_files $uri /index.php =404;
        fastcgi_pass php-upstream;
        fastcgi_index index.php;
        fastcgi_buffers 16 16k;
        fastcgi_buffer_size 32k;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        #fixes timeouts
        fastcgi_read_timeout 600;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }

    location /.well-known/acme-challenge/ {
        root /var/www/letsencrypt/;
        log_not_found off;
    }
}
```

We may remove the `4433` https port in the future

## MariaDB

We run two MariaDB containers on 3307 (base) and 3308 (secondary) locally not to interfere with the Valet database port 3306 (default)

Do use `mariadb/docker-entrypoint-initdb.d/createdb.sql.example` to create the databases needed by renaming the file to `.sql` and adjusting its content

Locally it is `root` user and password `root`, user `default` and password `secret`. Databases the ones added (default and published in our test case). You can look them up logging in as root with ease.