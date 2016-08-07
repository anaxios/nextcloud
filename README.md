# Nextcloud

A Simple Nextcloud image. The Dockerfile is almost idetical to the offical ownCloud Dockerfile.
PHP is upgraded to 7.0.9.
### Run From CL
    docker run -d -p 80:80 skybosh/nextcloud:latest
### Run from CL with database
    docker run -d -p 80:80 --link nextcloud-mariadb:mysql skybosh/nextcloud:latest

### For data persistence 

* -v /my/nextcloud/dir:/var/www/html

You can separate the data, config and apps directory.
* -v /my/data/dir:/var/www/html/data
* -v /my/apps/dir:/var/www/html/apps
* -v /my/config/dir:/var/www/html/config

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
### Ubuntu Upstart config sample for docker-compose
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
### Sample config for nginx reverse proxy

[GitHub/nextcloud.conf](https://github.com/skybosh/nextcloud/blob/master/nextcloud.conf)

### Cacheing with redis & APCu

Note: memcached is not compatible with PHP 7. 

[Nextcloud Docs/Configuring Memory Caching](https://docs.nextcloud.com/server/9/admin_manual/configuration_server/caching_configuration.html?highlight=memcache)
