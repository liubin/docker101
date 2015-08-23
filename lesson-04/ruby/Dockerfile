FROM centos:7 

MAINTAINER bin liu <liubin0329@gmail.com>

LABEL version="2.2.2" lang="ruby"

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
RUN echo 'gem: --no-rdoc --no-ri' >> "$HOME/.gemrc"


