---
layout: post
title: "Rails With Docker Postgres Container"
categories: docker
---
This is entry one in a series of posts investigating the usages of Docker within a Rails environment.
We will go from a standard Rails with Postgres app and start introducing Docker at different levels.
This entry will focus on Dockerizing Postgres while still keeping the Rails development on the host machine. _throw in link to github account_

## Pre-requisites
I'll be using Ubuntu as my OS with Rails, Postgres, and Docker already installed.
There are already good tutorials out there explaining how to install and verify.

## Creating our Rails app
We start off with a simple Rails app
~~~
rails new docker-play -d postgresql
rails g scaffold items name:string quantity:integer
~~~

Lets configure postgres for our database, below is what I did adjust commands accordingly
~~~
su - postgres
psql
 > create role dockerplay with createdb login password 'password1';
 > \q
exit #Exit the postgres session we su'ed in
~~~

Edit `db/seeds.rb`
~~~
items = Item.create([
  {name: 'Apples', quantity: 4},
  {name: 'Bananas', quantity: 2},
  {name: 'Oranges', quantity: 3},
])
~~~

Now lets setup the database for our application
~~~
rake db:create
rake db:migrate
rake db:seed
~~~

If everything went smoothly we should be able to start the rails server, navigate to `localhost:3000/items` and see three items.

Yay, we established a baseline. Everything is running on the Host machine and nothing is yet dockerized.

##Dockerizing Postgres
Being able to use Docker to contain our postgresql dependency allows me change the database without having to mess with my Host machine.

We will start off with a Dockerfile to describe our postgres needs.








--- blog entry one
create rails project
create postgres docker containter
run migragtions on container
investigate how to build container -- good for development
connect rails project to container
use mounted volumes for docker container - is that needed??  -- maybe good for staging and production
--blog entry two
create docker container for rails
use docker compose to link them together
run test on containers
demonstrate hot reloading
--blog entry three
configuring jenkins
