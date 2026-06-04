FROM php:5.6-fpm

# RUN echo "deb https://archive.debian.org/debian stable main" > /etc/apt/sources.list

# RUN set -eux; \
#     apt-get update; \
#     apt-get upgrade -y; \
#     apt-get install -y --no-install-recommends \
#             curl \
#             libmemcached-dev \
#             libz-dev \
#             libpq-dev \
#             libjpeg-dev \
#             libpng-dev \
#             libfreetype6-dev \
#             libssl-dev \
#             libwebp-dev \
#             libxpm-dev \
#             libmcrypt-dev \
#             libicu-dev \
#             libzip-dev \
#             zip \
#             libonig-dev; \
#     rm -rf /var/lib/apt/lists/*

RUN set -eux; \
    docker-php-ext-install pdo; \
    docker-php-ext-install mysql; \
    docker-php-ext-install mysqli; \
    docker-php-ext-install pdo_mysql;
    # docker-php-ext-configure intl; \
    # docker-php-ext-install intl; \
    # docker-php-ext-install zip; \
    # docker-php-ext-configure gd \
    #         --prefix=/usr \
    #         --with-jpeg \
    #         --with-webp \
    #         --with-xpm \
    #         --with-freetype; \
    # docker-php-ext-install gd;

# Imagick
# RUN apt-get update; \
#     apt-get install -y php5.6-gd; \
#     apt-get install -y php5.6-imagick; \
#     pecl install imagick; \
#     docker-php-ext-enable gd; \
#     docker-php-ext-enable imagick;

# Node.js
ENV NODE_VERSION=14.18.1
RUN apt install -y curl
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
ENV NVM_DIR=/root/.nvm
RUN . "$NVM_DIR/nvm.sh" && nvm install ${NODE_VERSION}
RUN . "$NVM_DIR/nvm.sh" && nvm use v${NODE_VERSION}
RUN . "$NVM_DIR/nvm.sh" && nvm alias default v${NODE_VERSION}
ENV PATH="/root/.nvm/versions/node/v${NODE_VERSION}/bin/:${PATH}"
RUN node --version
RUN npm --version

COPY --from=composer /usr/bin/composer /usr/bin/composer