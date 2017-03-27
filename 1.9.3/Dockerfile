FROM       ubuntu:trusty
MAINTAINER Myros <myros.net@gmail.com>

ENV PACKAGES \
  automake \
  autoconf \
  bison \
  curl \
  git-core \
	libxml2-dev \
	libxslt-dev \
	libffi-dev \
	libmcrypt-dev \
  libxslt-dev \
  libxml2-dev \
  libmysqlclient-dev \
  libreadline6 \
  libreadline6-dev \
  libssl-dev \
  libyaml-dev \
  libsqlite3-0 \
  libsqlite3-dev \
  libxml2-dev \
  libxslt-dev \
  libc6-dev \
  libtool \
  ncurses-dev \
  openssl \
  sqlite3 \
  subversion \
  zlib1g \
  zlib1g-dev \
  wget

RUN apt-get update && apt-get install -y $PACKAGES

RUN apt-get install make
RUN \
# Install build dependencies
  apt-get update &&\
  DEBIAN_FRONTEND=noninteractive apt-get install -y \
  ruby &&\
# Install ruby-install
  cd /tmp &&\
  wget -O ruby-install-0.5.0.tar.gz https://github.com/postmodern/ruby-install/archive/v0.5.0.tar.gz &&\
  tar -xzvf ruby-install-0.5.0.tar.gz &&\
  cd ruby-install-0.5.0/ &&\
  make install &&\
# Install Ruby
  ruby-install ruby 1.9.3-p551 &&\
# Install bundler globally
  PATH=/opt/rubies/ruby-1.9.3-p551/bin:$PATH gem install bundler &&\
# Remove build dependencies, clean up APT and temp files
  apt-get clean &&\
  DEBIAN_FRONTEND=noninteractive apt-get remove --purge -y ruby wget build-essential &&\
  rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Set $PATH so that non-login shells will see the Ruby binaries
ENV PATH /opt/rubies/ruby-1.9.3-p551/bin:$PATH

# Add Ruby binaries to login shells's $PATH
COPY ./ruby.sh /etc/profile.d/ruby.sh

ARG APP_LOCAL_PATH
ARG buildno

RUN echo $APP_LOCAL_PATH

# vars
ENV CONTAINER_APP_FOLDER /app

# Run `bundle install` BEFORE adding the Rails application directory structure
# to /app on the container's filesystem.
RUN mkdir $CONTAINER_APP_FOLDER

# Add the Rails application directory structure to $CONTAINER_APP_FOLDER on the container's filesystem
ADD ./Gemfile $CONTAINER_APP_FOLDER/Gemfile
# ADD ./Gemfile.lock $CONTAINER_APP_FOLDER/Gemfile.lock

WORKDIR $CONTAINER_APP_FOLDER

RUN gem install bundler

RUN bundle install --jobs 20 --retry 5

# Make directories we need
RUN mkdir -p /tmp/videos
RUN mkdir -p /tmp/dnload
RUN mkdir -p /tmp/test

# Overridable startup commands
COPY docker-entrypoint.sh /
RUN chmod +x /docker-entrypoint.sh
ENTRYPOINT ["/docker-entrypoint.sh"]

# The main command to run when the container starts. Also
# tell the Rails dev server to bind to all interfaces by
# default.
# CMD ["bundle", "exec", "rails", "server", "-b", "0.0.0.0"]
