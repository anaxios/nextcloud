# Nextcloud
A Simple Nextcloud image. The Dockerfile is almost identical to the offical ownCloud Dockerfile.  
Nextcloud is version 12.0.3.  
PHP is upgraded to 7.0.24.  

### Run From CL
    docker run -d -p 80:80 anaxios/nextcloud:latest

### Run from CL with database
    docker run -d -p 80:80 --link nextcloud-mariadb:mysql anaxios/nextcloud:latest

### For data persistence
* -v /my/nextcloud/dir:/var/www/html

It is sometimes useful to separate the file structure with a little more granularity. In particular, by putting the /var/www/html/data directory on a RAID Disk.  
* -v /my/data/dir:/var/www/html/data
* -v /my/apps/dir:/var/www/html/apps
* -v /my/config/dir:/var/www/html/config

Please see the Git repo for the example config files.
[GitHub/anaxios/nextcloud](https://github.com/anaxios/nextcloud)

### Example docker-compose.yml
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
      image: anaxios:nextcloud
      volumes:
        - ./apps:/var/www/html/apps
        - ./config:/var/www/html/config
        - ./data:/var/www/html/data
      ports:
        - 80:80
      links:
        - db
        - redis

### Ubuntu Upstart config example for docker-compose
    description "Nextcloud"

    # Start Nextcloud only after Docker service starts
    start on (started docker)
    stop on runlevel [!2345]

    # Automatically Respawn with finite limits
    respawn
    respawn limit 99 5

    # Path to our app
    chdir /var/www/nextcloud

    script
        # Bring up mariadb, redis, and Nextcloud
        exec /usr/local/bin/docker-compose up
    end script

    post-stop script
        # Actually stop the containers
        exec /usr/local/bin/docker-compose stop
    end script

### Example config for nginx reverse proxy
[GitHub/nextcloud.conf](https://github.com/anaxios/nextcloud/blob/master/nextcloud.conf)

### Cacheing with redis & APCu

Note:* memcached is not compatible with PHP 7.*

[Nextcloud Docs/Configuring Memory Caching](https://docs.nextcloud.com/server/12/admin_manual/configuration_server/caching_configuration.html?highlight=memcache)
