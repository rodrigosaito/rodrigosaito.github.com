---
layout: post
title: "Hello World Webserver in Go"
tags: [ "golang", "web" ]
---

Go standard library has a lot of useful packages to create a webserver and it's very well built so you can use it in production,
I will write a series of posts about the ```net/http``` package. In this tutorial I will show how to create a Hello World Webserver in Go.

Let's start by creating a Hello World using the ```net/http``` package, create a file named ```main.go```:

{% highlight go linenos %}
package main

import (
  "fmt"
  "net/http"
)

func handler(w http.ResponseWriter, r *http.Request) {
  fmt.Fprintf(w, "Hello")
}

func main() {
  http.HandleFunc("/", handler)
  http.ListenAndServe(":8080", nil)
}
{% endhighlight %}

Here are some exaplanations about this code:

* First we import the ```fmt``` and ```net/http``` packages
* We define a ```handler()``` function that writes "Hello" to the response
* Then in our ```main()``` function we define a route that responds every request with our ```handler()``` function
* Start our server and tell it to listen at port 8080

The handler function must receive the following parameters: an ```http.ResponseWriter``` and a pointer to ```http.Request```, where ```http.ResponseWriter```
is the object used to write all the data to the Response.

Now we can run our Hello World example and see if everything works as expected:

{% highlight bash %}
$ go run main.go
{% endhighlight %}

And we can test it:

{% highlight bash %}
$ curl -i http://localhost:8080
HTTP/1.1 200 OK
Content-Length: 5
Content-Type: text/plain; charset=utf-8

Hello
{% endhighlight %}
