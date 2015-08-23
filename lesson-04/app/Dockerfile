FROM liubin/ruby:2.2.2


MAINTAINER bin liu <liubin0329@gmail.com>

LABEL version="0.0.1" lang="myapp"

RUN mkdir /app
WORKDIR /app

# NOT WORK!!!
# ADD app.rb /app
ADD app.rb /app/app.rb

RUN gem install sinatra

EXPOSE 4567

CMD ruby app.rb

