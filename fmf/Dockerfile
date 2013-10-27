# FMF Environment
#
# VERSION	0.1
# DOCKER-VERSION	0.6

# Picked Ubuntu 12.04.3 as the Primary OS.
FROM ubuntu:precise

# Gave myself some credit for doing this. Give it where it's due!
MAINTAINER Kenny Raghunath, Kenny@masyafstudios.com

# Appearantly a requirement that's needed for the next step, also some basic tools to use later on.
RUN apt-get -y install python-software-properties nano wget

# Install all necessary keys for MongoDB. Instructions are found on the official website.
RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10
RUN echo 'deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen' | tee /etc/apt/sources.list.d/mongodb.list

# Install Nginx using Launchpad's version of latest stable.
RUN /usr/bin/apt-add-repository -y ppa:nginx/stable

# Update all apt repositories to get new sources.
RUN apt-get update

# Install all apt repository software. This includes MongoDB, Memcache, Git, Beanstalkd, and nginx. There are also some other basic system requirements that are needed.
RUN apt-get install -y mongodb-10gen memcached nginx git openjdk-6-jdk libxml2-dev curl libtidy-dev libzip-dev libgd2-xpm-dev libcurl4-openssl-dev autoconf libyaml-dev libevent-dev make

# Install Beanstalkd from source since it cannot be pulled from apt or when I figure out where it is in apt.
ADD https://github.com/kr/beanstalkd/archive/v1.9.tar.gz ./
RUN tar xvzf ./v1.9.tar.gz && cd beanstalkd-1.9 && make && make install

# Install PHP 5.5.5 from source. It is a long command chain due to what I understand of Docker.
ADD http://us2.php.net/get/php-5.5.5.tar.gz/from/us1.php.net/mirror ./php-5.5.5.tar.gz
RUN tar xvzf ./php-5.5.5.tar.gz && cd php-5.5.5/ && ./configure --enable-fpm --with-curl --with-openssl --with-gd --with-tidy --enable-zip && make && make install

# Install the PHP extensions that's needed for FMF. Since some of the pecl requests are interactive, the printf command tells it to default to whatever it has.
RUN printf "\n" | pecl install yaml memcache mongo

# Next we install Elastic Search. I'm not sure if this works out of the box, but through testing the script, we shall see.
ADD https://download.elasticsearch.org/elasticsearch/elasticsearch/elasticsearch-0.90.5.deb ./
RUN dpkg -i elasticsearch-0.90.5.deb

# Expose the ports we need to the outside. [ 11300 is Beanstalkd ]
EXPOSE 80 11211 27017 11300

# Configure nginx, PHP and other pieces of software for use.
ADD https://raw.github.com/masyafStudios/docker-collections/master/fmf/conf/nginx.conf /etc/nginx/nginx.conf
ADD https://raw.github.com/masyafStudios/docker-collections/master/fmf/conf/php.ini /usr/local/lib/php.ini
ADD https://raw.github.com/masyafStudios/docker-collections/master/fmf/conf/php-fpm.conf /usr/local/etc/php-fpm.conf

# Run commands to start up the environment.
CMD nginx && php-fpm && mongod && memcache && beanstalkd -p 11300 && service elasticsearch start
