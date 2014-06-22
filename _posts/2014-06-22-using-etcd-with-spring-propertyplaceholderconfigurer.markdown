---
layout: post
title: "Using Etcd with Spring PropertyPlaceholderConfigurer"
tags: [ "java", "spring", "etcd" ]
---

Spring [PropertyPlaceholderConfigurer](http://docs.spring.io/spring/docs/4.0.5.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-placeholderconfigurer) is used to make some beans configurable like database connections, http clients, feature flags etc.

The problem with PropertyPlaceholderConfigurer is when you want to change one of the properties you either have to redeploy your app or ssh into all your servers and update the config file one by one.

[Etcd](https://github.com/coreos/etcd) is a highly-available key value store for shared configuration and service discovery and can be used to hold all your application configs in a centralized way.

There is lib called [jetcd](https://github.com/justinsb/jetcd) which can communicate with etcd server.

Here is an extension to PropertyPlaceholderConfigurer that uses jetcd client, if the property couldn't be found on etcd then fallback to default implementation:

{% highlight java linenos %}
public class EtcdPropertyPlaceholderConfigurer extends PropertyPlaceholderConfigurer {

    private static final Logger LOGGER = LoggerFactory.getLogger(EtcdPropertyPlaceholderConfigurer.class);

    private EtcdClient client;

    public void setServerUrl(final String serverUrl) {
        this.client = EtcdClientFactory.newInstance(serverUrl);
    }

    @Override
    protected String resolvePlaceholder(final String placeholder, final Properties props) {
        if (this.client != null) {
            try {
                return client.get(placeholder.replaceAll("\\.","/"));
            } catch (Exception e) {
                // ignore
            }
        }

        LOGGER.info("Failed to resolve property from etcd, falling back to default PropertyPlaceholderConfigurer implementation.");

        return super.resolvePlaceholder(placeholder, props);
    }
}
{% endhighlight %}

You can find the full application on [github](http://github.com/rodrigosaito/spring-etcd)
