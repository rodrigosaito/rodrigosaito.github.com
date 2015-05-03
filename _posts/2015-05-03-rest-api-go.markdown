---
layout: post
title: "Rest API in Go"
tags: [ "golang", "json", "rest" ]
---

On the [previous post]({% post_url 2015-05-02-json-responses-go %}) we learned how to return JSON responses in Go,
now we will see how to build our first Rest API.

Let's modify our previous example to have a route to handle the member resource:

{% highlight go linenos %}
package main

import (
        "encoding/json"
        "io/ioutil"
        "net/http"
)

var members = []Member{Member{"someuser", "someuser@somedomain.com"}}

type Member struct {
        Login string
        Email string
}

func main() {
        http.HandleFunc("/members", membersHandler)
        http.ListenAndServe(":8080", nil)
}

func membersHandler(w http.ResponseWriter, r *http.Request) {
        w.Header().Set("Content-Type", "application/json")

        if r.Method == "GET" {
                j, _ := json.Marshal(members)

                w.Write(j)
        }

        if r.Method == "POST" {
                var m Member
                b, _ := ioutil.ReadAll(r.Body)
                json.Unmarshal(b, &m)

                members = append(members, m)

                j, _ := json.Marshal(m)
                w.Write(j)
        }
}
{% endhighlight %}

On line 17 we changed the first parameter from ```"/"``` to ```"/members"```, now our membersHandler will only handle requests to
the ```/members``` endpoint.

The ```membersHandler``` function:

* Sets the ```"Content-Type"``` header to ```application/json```.
* Checks if the request method is ```GET```, if so it just returns all the members.
* If the method is ```POST``` it saves the new member to the members array and then returns the newly created member.

When we issue a ```GET /members``` then we will see the following response:

{% highlight bash %}
$ curl -i localhost:8080/members
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 56

[{"Login":"someuser","Email":"someuser@somedomain.com"}]%
{% endhighlight %}

We can also create a new member:

{% highlight bash %}
$ curl -d '{"Login":"anotheruser", "Email": "another@somedomain.com"}' -i localhost:8080/members
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 56

{"Login":"anotheruser","Email":"another@somedomain.com"}%
{% endhighlight %}

The following posts I will show you how to have smarter routes and how to persist your members using MongoDB.
