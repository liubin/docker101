FROM liubin/ruby:2.2.2


MAINTAINER bin liu <liubin0329@gmail.com>

LABEL version="0.0.1" lang="myapp"

ADD app.rb /app/app.rb

WORKDIR /app

RUN gem sources --remove https://rubygems.org/ \
  && gem sources -a http://ruby.taobao.org/

RUN gem install sinatra

EXPOSE 4567

CMD ["ruby", "app.rb"]

