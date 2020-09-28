FROM php:7.2-fpm
RUN apt-get update \
    && apt-get install -y libzip-dev git mariadb-client libmagickwand-dev --no-install-recommends
RUN docker-php-ext-install pdo_mysql zip \
    && pecl install imagick \
    && docker-php-ext-enable imagick \
    && php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php composer-setup.php \
    && php -r "unlink('composer-setup.php');" \
    && mv composer.phar /usr/local/bin/composer

RUN curl -sL https://deb.nodesource.com/setup_12.x | bash - 
RUN apt-get install -y nodejs

# confirm that it was successful 
RUN node -v
# npm installs automatically 
RUN npm -v
COPY . .
RUN composer install
RUN npm install
RUN npm run prod
RUN rm -rf node_modules

FROM php:7.2-alpine3.12

ENV \
  APP_DIR="/app" \
  APP_PORT="80"

# the "app" directory (relative to Dockerfile) containers your Laravel app...
COPY --from=0 /var/www/html/ $APP_DIR

RUN apk add --update \
    curl \
    && rm -rf /var/cache/apk/*

RUN cd $APP_DIR

WORKDIR $APP_DIR

RUN apk add --no-cache zip libzip-dev
RUN docker-php-ext-configure zip
RUN docker-php-ext-install zip
RUN docker-php-ext-install pdo pdo_mysql 

CMD php artisan serve --host=0.0.0.0 --port=$APP_PORT