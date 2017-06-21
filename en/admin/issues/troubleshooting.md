# General troubleshooting

If you have trouble installing, configuring or maintaining wallabag, please refer to our community support channels:

* [The wallabag Forums](https://community.wallabag.org)
* [A Gitter room](https://gitter.im/wallabag/wallabag)
* The wallabag IRC chat channel irc://#wallabag@freenode.net on freenode.net, also accessible via [webchat](https://webchat.freenode.net/?channels=wallabag) (not much active)

Please understand that all these channels essentially consist of users like you helping each other out. Consider helping others out where you can, to contribute back for the help you get. This is the only way to keep a community like wallabag healthy and sustainable!

If you are using wallabag in a business or otherwise large scale deployment, note that [wallabag.it](https://www.wallabag.it/en) offers commercial support options.

## Bugs

If you think you have found a bug in wallabag, please:

* Search for a solution (see FAQ for instance)
* Double-check your configuration

If you can’t find a solution, please use our [bugtracker](github.com/wallabag/wallabag/issues). Keep in mind someone may already have filled in a similar issue, so please use the search before reporting.

## Logs

In a standard wallabag installation the log level is set to *production* (or `error`) which means wallabag will only log errors. To find any issues you need to raise the `action_level` value of Monolog to `debug` instead of `error` in your `wallabag/app/config/config_prod.yml` file.

For JavaScript issues you will also need to view the javascript console of your browser. All major browsers have developer tools for viewing the console, and you usually access them by pressing F12.

{% hint style='info' %}
The logfile of wallabag is located in the data directory `wallabag/var/log/prod.log`.
{% endhint %}

Server logs usally are inside the `/var/log` folder of your server.
