FROM ubuntu:16.04

# thanks to https://github.com/phalcon/dockerfiles/blob/master/build/ubuntu-xenial-7.1/Dockerfile

ARG PHP_VERSION=7.1
ARG PHALCON_VERSION=3.4.2

ENV TIMEZONE=CST \
    LANGUAGE=en_US.UTF-8 \
    LANG=en_US.UTF-8 \
    LC_ALL=en_US.UTF-8 \
    TERM=xterm

WORKDIR /var/www/html/

RUN apt clean -y \
    && apt update -y \
    && apt upgrade -y \
    && apt install -y locales \
    && export LANGUAGE=${LANGUAGE} \
    && export LANG=${LANG} \
    && export LC_ALL=${LC_ALL} \
    && locale-gen ${LANG} \
    && dpkg-reconfigure --frontend noninteractive locales \
    && apt install --no-install-recommends -yq \
        apt-utils \
        software-properties-common \
        python-software-properties \
        lsb-release \
        ca-certificates \
        apt-transport-https \
        tzdata \
    && echo "#!/bin/sh\nexit 0" > /usr/sbin/policy-rc.d \
    && ln -sf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime \
    && echo "${TIMEZONE}" | tee /etc/timezone \
    && dpkg-reconfigure --frontend noninteractive tzdata \
    && apt-add-repository -y multiverse \
    && apt-add-repository -y ppa:ondrej/php \
    && apt update -y \
    && apt upgrade -y \
    && apt install --no-install-recommends -yq \
        alien \
        ant \
        binutils-dev \
        ccache \
        cdbs \
        cmake \
        curl \
        debhelper \
        devscripts \
        doxygen \
        equivs \
        fakeroot \
        gdb \
        gdebi-core \
        git \
        gnupg \
        libpcre3-dev \
        libreadline-dev \
        libyaml-dev \
        memcached \
        mysql-client \
        re2c \
        rpm \
        sudo \
        vim \
        wget \
        wkhtmltopdf \
        zlib1g-dev \

    # Apache
    && apt-get install -y apache2 libapache2-mod-php${PHP_VERSION} \
    && a2enmod rewrite \
    && a2enmod headers \

    # PHP
    && apt-get install --no-install-recommends -yq \
        php${PHP_VERSION} \
        php${PHP_VERSION}-dev \
        php${PHP_VERSION}-cli \
        php${PHP_VERSION}-common \
        php${PHP_VERSION}-curl \
        php${PHP_VERSION}-fileinfo \
        php${PHP_VERSION}-gd \
        php${PHP_VERSION}-gettext \
        php${PHP_VERSION}-intl \
        php${PHP_VERSION}-json \
        php${PHP_VERSION}-mb \
        php${PHP_VERSION}-mbstring \
        php${PHP_VERSION}-mcrypt \
        php${PHP_VERSION}-memcached \
        php${PHP_VERSION}-mysql \
        php${PHP_VERSION}-redis \
        php${PHP_VERSION}-sqlite \
        php${PHP_VERSION}-xml \
        php${PHP_VERSION}-zip \

    # Phalcon
    && git clone --branch v${PHALCON_VERSION} --depth 1 https://github.com/phalcon/cphalcon.git /opt/cphalcon \
    && cd /opt/cphalcon/build \
    && ./install \
    && printf '%s\n%s\n%s\n' '; configuration for php common module' '; priority=30' 'extension=phalcon.so' > /etc/php/${PHP_VERSION}/mods-available/phalcon.ini \
    && phpenmod phalcon \

    # Cleanup
    && apt autoremove -y \
    && apt clean -y \
    && rm -rf /tmp/* /var/tmp/* /tmp/.htaccess \
    && find /var/cache/apt/archives /var/lib/apt/lists -not -name lock -type f -delete \
    && find /var/cache -type f -delete \
    && find /var/log -type f | while read f; do echo -n '' > ${f}; done \
    && rm -rf /run/httpd/* /tmp/httpd*

EXPOSE 80
EXPOSE 443

CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
