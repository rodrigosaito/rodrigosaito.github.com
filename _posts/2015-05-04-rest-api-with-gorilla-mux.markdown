---
layout: post
title: "Rest API with gorilla/mux routing"
tags: [ "golang", "json", "rest" ]
---

Go standard library has a lot of useful packages, but for creating routes on your API other packages are a better fit.
In this tutorial we will use ```gorilla/mux``` package as our request router.

You can find the documentation for ```gorilla/mux``` [here](http://www.gorillatoolkit.org/pkg/mux)

First let's modify our previous example to have two different handler functions, one for ```GET``` and one for ```POST```, instead of one for both requests.
You can find the previous post [here]({% post_url 2015-05-03-rest-api-go %}).

{% highlight go linenos %}
func getMembersHandler(w http.ResponseWriter, r *http.Request) {
        w.Header().Set("Content-Type", "application/json")

        j, _ := json.Marshal(members)
        w.Write(j)
}

func postMembersHandler(w http.ResponseWriter, r *http.Request) {
        w.Header().Set("Content-Type", "application/json")

        var m Member
        b, _ := ioutil.ReadAll(r.Body)
        json.Unmarshal(b, &m)

        members = append(members, m)

        j, _ := json.Marshal(m)
        w.Write(j)
}
{% endhighlight %}

Now we have two different functions for handling two different requests ```GET /members``` and ```POST /members```, the main advantage of this
refactoring is that we can now test the functions separately, I will show you how to test your handler functions on a future post.

And here is the new ```main()``` function:

{% highlight go linenos %}
func main() {
        r := mux.NewRouter()
        r.HandleFunc("/members", getMembersHandler).Methods("GET")
        r.HandleFunc("/members", postMembersHandler).Methods("POST")

        http.Handle("/", r)
        http.ListenAndServe(":8080", nil)
}
{% endhighlight %}

Explanations:

* On line 2 we created a new Router, this is the object that will route our requests to the right handler function.
* On line 3 we are declaring that the ```getMembersHandler``` function will handle ```GET /members``` requests.
* On line 4 we are declaring that the ```postMembersHandler``` function will handle ```POST /members``` requests.

The package ```gorilla/mux``` has a lot of different ways to match requests, check the documentation for more information.

Here is the full example:

{% highlight go linenos %}
package main

import (
        "encoding/json"
        "io/ioutil"
        "net/http"

        "github.com/gorilla/mux"
)

var members = []Member{Member{"someuser", "someuser@somedomain.com"}}

type Member struct {
        Login string
        Email string
}

func getMembersHandler(w http.ResponseWriter, r *http.Request) {
        w.Header().Set("Content-Type", "application/json")

        j, _ := json.Marshal(members)
        w.Write(j)
}

func postMembersHandler(w http.ResponseWriter, r *http.Request) {
        w.Header().Set("Content-Type", "application/json")

        var m Member
        b, _ := ioutil.ReadAll(r.Body)
        json.Unmarshal(b, &m)

        members = append(members, m)

        j, _ := json.Marshal(m)
        w.Write(j)
}

func main() {
        r := mux.NewRouter()
        r.HandleFunc("/members", getMembersHandler).Methods("GET")
        r.HandleFunc("/members", postMembersHandler).Methods("POST")

        http.Handle("/", r)
        http.ListenAndServe(":8080", nil)
}
{% endhighlight %}
