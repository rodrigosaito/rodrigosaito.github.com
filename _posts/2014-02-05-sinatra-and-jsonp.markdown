---
layout: post
title: "Sinatra and JSONP"
tags: [ "sinatra", "ruby", "jsonp" ]
---

[Sinatra](http://sinatrarb.com) is really cool for creating apis beacause of its simplicity, but some defaults can bring you trouble when creating a [JSONP](http://json-p.org/) endpoint.

JSONP is widely used to execute cross domain requests and by default Sinatra will use [rack-protection](https://github.com/rkh/rack-protection) gem so your app is automatically protected by some vector attacks.

This defaults can cause you trouble when the client code sending the request adds the *Referer* header with a domain different from you app domain.

Imagine the following sinatra code:

{% highlight ruby linenos %}
require 'sinatra'
require 'sinatra/json'

get "/jsonp" do
  json(%(do_something({ "some_name": "some value" })))
end
{% endhighlight %}

If you run the following spec then everything works fine:

{% highlight ruby linenos %}
it "returns ok" do
  get "/jsonp"
  expect(last_response).to be_ok
end
{% endhighlight %}

But when you add the *Referer* header to the request, it will fail with a 403 (Forbidden) status:

{% highlight ruby linenos %}
it "returns ok when referer is another site" do
  get "/jsonp", {}, { "HTTP_REFERER" => "http://google.com" }
  expect(last_response).to be_ok
end
{% endhighlight %}

To avoid this issue you have to disable the *json_csrf* protection by doing this in your sinatra app:

{% highlight ruby linenos %}
set :protection, except: [ :json_csrf ]
{% endhighlight %}

After that the second spec will be success.

You can find the full example app on my [github](https://github.com/rodrigosaito/sinatra_jsonp/). The master branch has the failing spec, and the fixed branch has the configuration to remove *json_csrf* protection.
