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
List helps
http://ryaneschinger.com/blog/dockerized-postgresql-development-environment/
https://docs.docker.com/engine/userguide/containers/dockervolumes/
https://hub.docker.com/_/postgres/

Being able to use Docker to contain our postgresql dependency allows me change the database without having to mess with my Host machine.

###Creating the docker data container
We can create a docker data container to store all the data needed for the application
~~~
docker create -v /var/lib/postgresql/data --name postgres-data busybox
~~~

Now create the Postgres container to use the data containter
~~~
docker run --name local-postgres9.4 -e POSTGRES_PASSWORD=password1 -d --volumes-from postgres-data postgres:9.4
~~~

Lets setup the database
~~~
docker run -it --link local-postgres9.4:postgres --rm postgres:9.4 sh -c 'exec psql -h "$POSTGRES_PORT_5432_TCP_ADDR" -p "$POSTGRES_PORT_5432_TCP_PORT" -U postgres'
~~~
We should be in psql by now
~~~
create role dockerplay with createdb login password 'password1';
~~~

Now let's start the container so we can access it from our Rails application

_Note we are using 5442 as the local postgres is running on 5432_

~~~
docker run --name local-postgres9.4 -p 5442:5432 -e POSTGRES_PASSWORD=password1 -d --volumes-from postgres-data postgres:9.4
~~~

We will start off with a Dockerfile to describe our postgres needs.
