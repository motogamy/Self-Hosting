# Step 1

Create Docker file.
**Choose the latest stable version of php-apache updated - December 13, 2024** 

```dockerfile
FROM php:8.3.12-apache

RUN cp /usr/local/etc/php/php.ini-development /usr/local/etc/php/php.ini

RUN apt-get update && apt-get upgrade -y
RUN docker-php-ext-install mysqli pdo pdo_mysql opcache bcmath && docker-php-ext-enable mysqli && a2enmod rewrite

# Install zip, Intl, imap plugin
RUN apt-get install -y libzip-dev libicu-dev
RUN docker-php-ext-install zip
RUN docker-php-ext-configure intl
RUN docker-php-ext-install intl

#Install GD

RUN apt-get install -y libfreetype6-dev libjpeg-dev libjpeg62-turbo-dev libpng-dev libwebp-dev
RUN docker-php-ext-configure gd --with-jpeg --with-webp --with-freetype
RUN docker-php-ext-install -j$(nproc) gd

# Imagemagick

RUN apt-get install -y libmagickwand-dev --no-install-recommends \
    && pecl install imagick \
	&& docker-php-ext-enable imagickhttps://wordpress.org/download/

# Composer

RUN curl -sS https://getcomposer.org/installer | php -- \
--install-dir=/usr/bin --filename=composer && chmod +x /usr/bin/composer 

# bcmath, calendar, exif

RUN docker-php-ext-install bcmath calendar exif
RUN docker-php-ext-enable calendar exif 
```

# Step 2

Add the below php app container in docker compose file:

```docker
# PHP
  php-apache:
    container_name: php-apache
    build: .
    restart: unless-stopped
    volumes:
      - ./php/src:/var/www/html/
	  # Below volume will be added once php container spins for 1st time
    #   - ./phpconfig/php:/usr/local/etc/php/
    ports:
      - 2007:80
    networks:
      - <network_name>

```

# Step 3

Run below command to copy contents of php config files from container to host

```bash
docker cp php-apache:/usr/local/etc/php/ ../../phpconfig

```

# Step 4

- Go back to docker compose file and uncomment the below volume line from php container
- Restart container after updating
- Docker file should look like this

```docker
# PHP
  php-apache:
    container_name: php-apache
    build: .
    restart: unless-stopped
    volumes:
      - ./php/src:/var/www/html/
      - ./phpconfig/php:/usr/local/etc/php/ #this is added
    ports:
      - 2007:80
    networks:
      - <network_name>

```

# Step 5

Make necessary changes to the `php.ini` file in the `phpconfig/php` folder on local
After each change make sure to restart the php container

# Step 6

Make sure to set permission of php folder to www-data by running the below command

```
sudo chown -R www-data:www-data php

```
