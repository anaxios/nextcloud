# nextCloud

A Simple nextCloud image. The Dockerfile is almost idetical to the offical ownCloud Dockerfile.
PHP is upgraded to 7.0.9.

Please see the Git repo for the sample config files. 
[GitHub/skybosh/nextcloud](https://github.com/skybosh/nextcloud)
### Sample docker-compose.yml
    db:
      image: mariadb:latest
      volumes:
        - ./db:/var/lib/mysql
      environment:
        MYSQL_ROOT_PASSWORD: monkey123
        MYSQL_DATABASE: nextcloud
        MYSQL_USER: nextcloud
        MYSQL_PASSWORD: monkey123
    redis:
      image: redis:alpine
    nextcloud:
      image: skybosh:nextcloud
      volumes:
        - ./apps:/var/www/html/apps
        - ./config:/var/www/html/config
        - ./data:/var/www/html/data
      ports:
        - 80:80
      links:
        - db
        - redis

### Sample config for nginx reverse proxy

[GitHub/nextcloud.conf](https://github.com/skybosh/nextcloud/blob/master/nextcloud.conf)

## Cacheing with redis & APCu

Note: memcached is not compatible with PHP 7. 

[nextCloud Docs: Configuring Memory Caching](https://docs.nextcloud.com/server/9/admin_manual/configuration_server/caching_configuration.html?highlight=memcache)
