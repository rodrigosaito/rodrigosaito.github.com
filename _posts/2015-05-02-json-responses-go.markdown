---
layout: post
title: "JSON responses in Go"
tags: [ "golang", "json" ]
---

On the [previous part of this tutorial]({% post_url 2015-05-01-hello-world-webserver-go %}) I showed how to create a Hello World Webserver in Go,
in this part I will demonstrate how to return JSON responses using the standard library.

Let's modify our previous example and generate a JSON response instead of a hardcoded string:

{% highlight go linenos %}
package main

import (
  "encoding/json"
  "net/http"
)

type Member struct {
  Login string
  Email string
}

func handler(w http.ResponseWriter, r *http.Request) {
  member := Member{"someuser", "someuser@somedomain.com"}
  j, _ := json.Marshal(member)

  w.Header().Set("Content-Type", "application/json")
  w.Write(j)
}

func main() {
  http.HandleFunc("/", handler)
  http.ListenAndServe(":8080", nil)
}
{% endhighlight %}

Then:

{% highlight bash %}
$ curl -i localhost:8080
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 54

{"Login":"someuser","Email":"someuser@somedomain.com"}
{% endhighlight %}


