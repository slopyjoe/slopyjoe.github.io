---
layout: page
title: "Docker Helps"
date: 2015-09-16 12:28
comments: false
sharing: true
footer: true
---

#Docker Projects Reminders

##Rails & Postgres Project

Good Read for later

<http://blog.carbonfive.com/2015/03/17/docker-rails-docker-compose-together-in-your-development-workflow/>

Dockerfile for Rails project

~~~docker
FROM ruby
RUN apt-get update -qq && apt-get install -y build-essential libpq-dev
RUN mkdir /myapp
WORKDIR /myapp
ADD Gemfile /myapp/Gemfile
RUN bundle install
ADD . /myapp
~~~

docker-compose.yml 

~~~yaml
db:
  image: postgres
web:
  build: .
  command: /bin/bash
  volumes:
    - .:/myapp
  ports:
    - "3000:3000"
  links:
    - db
~~~