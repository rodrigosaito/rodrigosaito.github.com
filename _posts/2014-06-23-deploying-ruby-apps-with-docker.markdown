---
layout: post
title: "Deploying Ruby apps with Docker"
tags: [ "ruby", "docker", "devops" ]
---

[Docker](http://www.docker.com/) is a very fantastic tool for packing, shipping
and running any application as a lightweight container. I will show a way to use
Docker to deploy Ruby to an EC2 instance.

First we need a Ruby application, I have this simple
[sinatra application](https://github.com/rodrigosaito/rack_unicorn_docker)
that I created to test deployment with Docker.

At the root of the project we have a [Dockerfile](https://github.com/rodrigosaito/rack_unicorn_docker/blob/master/Dockerfile):

{% highlight bash linenos %}
FROM rodrigosaito/ruby:2.1.2

MAINTAINER Rodrigo Saito <rodrigo.saito@gmail.com>

ADD . /app/

WORKDIR /app/

RUN bundle install --deployment --without development test --jobs 4

ENV RACK_ENV development

CMD bundle exec unicorn -E $RACK_ENV

EXPOSE 8080
{% endhighlight %}

Explaining how a Dockerfile works is beyond the scope of this post, but basically
this Dockerfile will be built starting from another image that I created with
ruby 2.1.2 installed, copy the app files to the container, run bundle install,
set the command to run when we start the container and exposes port 8080.

To build this image you have to run the following command:

{% highlight bash %}
docker build -t rodrigosaito/rack_unicorn_docker .
{% endhighlight %}

After the build finishes you can see the image with __docker images__ command.

To run a new container with this image you execute:

{% highlight bash %}
# creates a new container from image and starts it
docker run -p 8080:8080 -e RACK_ENV=production -d rodrigosaito/rack_unicorn_docker

# check if your docker container is running
docker ps

# check if your application is running
curl localhost:8080/hello
{% endhighlight %}

Now that we have successfully built a docker image with our ruby app we can deploy
it to an AWS EC2 instance. __Note: You can be charged by using an EC2 instance,
be aware of that__
