FROM php:8.1-fpm

RUN set -eux; \
    apt-get update; \
    apt-get upgrade -y; \
    apt-get install -y --no-install-recommends \
            curl \
            libmemcached-dev \
            libz-dev \
            libpq-dev \
            libjpeg-dev \
            libpng-dev \
            libfreetype6-dev \
            libssl-dev \
            libwebp-dev \
            libxpm-dev \
            libmcrypt-dev \
            libicu-dev \
            libzip-dev \
            zip \
            wget \
            libonig-dev; \
    rm -rf /var/lib/apt/lists/*

RUN set -eux; \
    docker-php-ext-install pdo; \
    docker-php-ext-install mysqli; \
    docker-php-ext-install pdo_mysql; \
    docker-php-ext-install pdo_pgsql; \
    docker-php-ext-configure intl; \
    docker-php-ext-install intl; \
    docker-php-ext-install zip; \
    docker-php-ext-install exif; \
    docker-php-ext-configure gd \
            --prefix=/usr \
            --with-jpeg \
            --with-webp \
            --with-xpm \
            --with-freetype; \
    docker-php-ext-install gd;

# Imagick
RUN apt-get update && \
    apt-get install -y --no-install-recommends libmagickwand-dev && \
    mkdir -p /usr/src/php/ext/imagick && \
    curl -fsSL https://github.com/Imagick/imagick/archive/refs/tags/3.7.0.tar.gz \
        | tar xvz -C /usr/src/php/ext/imagick --strip 1 && \
    docker-php-ext-install imagick && \
    rm -rf /var/lib/apt/lists/*

# sqlsrv pdo_sqlsrv
# Зависимости + Microsoft ODBC Driver
# 1. Системные зависимости
RUN apt-get update && apt-get install -y \
    curl \
    gnupg2 \
    apt-transport-https \
    unixodbc-dev \
    g++ \
    make \
    autoconf \
    && rm -rf /var/lib/apt/lists/*

# 2. Microsoft ODBC Driver
RUN curl -fsSL https://packages.microsoft.com/keys/microsoft.asc \
        | gpg --dearmor > /usr/share/keyrings/microsoft-prod.gpg \
    && echo "deb [arch=amd64 signed-by=/usr/share/keyrings/microsoft-prod.gpg] https://packages.microsoft.com/debian/12/prod bookworm main" \
        > /etc/apt/sources.list.d/mssql-release.list \
    && apt-get update \
    && ACCEPT_EULA=Y apt-get install -y msodbcsql18 \
    && rm -rf /var/lib/apt/lists/*

# 3. Сборка — запускать configure из корня source (не из подпапок!)
RUN curl -fsSL https://github.com/microsoft/msphpsql/archive/refs/tags/v5.11.1.tar.gz \
        -o /tmp/msphpsql.tar.gz \
    && tar -xzf /tmp/msphpsql.tar.gz -C /tmp \
    # sqlsrv — phpize и configure внутри sqlsrv, но с указанием shared-хедеров
    && cd /tmp/msphpsql-5.11.1/source/sqlsrv \
    && cp -r ../shared ./shared \
    && phpize \
    && ./configure \
    && make \
    && make install \
    # pdo_sqlsrv — то же самое
    && cd /tmp/msphpsql-5.11.1/source/pdo_sqlsrv \
    && cp -r ../shared ./shared \
    && phpize \
    && ./configure \
    && make \
    && make install \
    && docker-php-ext-enable sqlsrv pdo_sqlsrv \
    && rm -rf /tmp/msphpsql*

# Node.js
ENV NODE_VERSION=20.19.1
RUN apt install -y curl
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
ENV NVM_DIR=/root/.nvm
RUN . "$NVM_DIR/nvm.sh" && nvm install ${NODE_VERSION}
RUN . "$NVM_DIR/nvm.sh" && nvm use v${NODE_VERSION}
RUN . "$NVM_DIR/nvm.sh" && nvm alias default v${NODE_VERSION}
ENV PATH="/root/.nvm/versions/node/v${NODE_VERSION}/bin/:${PATH}"
RUN node --version
RUN npm --version

# Установка альтернативной версии Python 3.11 для установки node-sass для @bitrix/cli
# Если не нужна работа с битриксом, то вполне можно все это благополоучно не ставить
RUN cd /usr/src \
    && wget https://www.python.org/ftp/python/3.11.0/Python-3.11.0.tgz \
    && tar -xzf Python-3.11.0.tgz \
    && cd Python-3.11.0 \
    && ./configure --enable-optimizations \
    && make \
    && make altinstall

# Ставим альтернативную версию Python на команду python3
RUN ln -fs /usr/src/Python-3.11.0/python /usr/bin/python3

# Bitrix CLI
RUN npm i -g node-sass @bitrix/cli

COPY --from=composer /usr/bin/composer /usr/bin/composer