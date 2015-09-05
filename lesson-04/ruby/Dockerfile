FROM centos:7 

MAINTAINER bin liu <liubin0329@gmail.com>

LABEL version="2.2.2" lang="ruby"

ENV RUBY_MAJOR 2.2
ENV RUBY_VERSION 2.2.2

RUN yum install -y wget tar gcc g++ make automake autoconf curl-devel openssl-devel zlib-devel httpd-devel apr-devel apr-util-devel sqlite-devel

RUN cd /tmp \
    && wget http://cache.ruby-lang.org/pub/ruby/2.2/ruby-2.2.2.tar.gz \
    && tar zxvf ruby-2.2.2.tar.gz \
    && cd ruby-2.2.2 \
    && autoconf \
    && ./configure --disable-install-doc \
    && make \
    && make install \
    && rm -rf /tmp/ruby-2.2.2*

# skip installing gem documentation
RUN echo -e 'install: --no-document\nupdate: --no-document' >> "$HOME/.gemrc"

ENV GEM_HOME /usr/local/bundle
ENV PATH $GEM_HOME/bin:$PATH

ENV BUNDLER_VERSION 1.10.6

RUN gem install bundler --version "$BUNDLER_VERSION" \
	&& bundle config --global path "$GEM_HOME" \
	&& bundle config --global bin "$GEM_HOME/bin"

# don't create ".bundle" in all our apps
ENV BUNDLE_APP_CONFIG $GEM_HOME

CMD [ "irb" ]

