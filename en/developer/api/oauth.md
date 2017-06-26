# Creating a new API client

In your wallabag account, you can create a new API client at this URL http://localhost:8000/developer/client/create.

Just give the redirect URL of your application and create your client.
If your application is a desktop one, put whatever URL suits you the most.

You get information like this:

```
Client ID:

1_3o53gl30vhgk0c8ks4cocww08o84448osgo40wgw4gwkoo8skc

Client secret:

636ocbqo978ckw0gsw4gcwwocg8044sco0w8w84cws48ggogs4
```

# Obtaining a access token

For each API call, you'll need a token. Let's create it with this command (replace `client_id` and `client_secret` with their values):

```bash
http POST http://localhost:8000/oauth/v2/token \
    grant_type=client_credentials \
    client_id=1_3o53gl30vhgk0c8ks4cocww08o84448osgo40wgw4gwkoo8skc \
    client_secret=636ocbqo978ckw0gsw4gcwwocg8044sco0w8w84cws48ggogs4
```

> **[info] Old behaviour**
>
> Before wallabag 2.3, the `password` `grant_type` was the only one possible and you couldn't just use `client_credentials`. With this authentication method, you needed to provide the user's credentials. [Read more](https://github.com/wallabag/wallabag/pull/3227)

> Example :
>
> ```bash
> http POST http://localhost:8000/oauth/v2/token \
>     grant_type=password \
>     client_id=1_3o53gl30vhgk0c8ks4cocww08o84448osgo40wgw4gwkoo8skc \
>     client_secret=636ocbqo978ckw0gsw4gcwwocg8044sco0w8w84cws48ggogs4 \
>     username=wallabag \
>     password=wallabag
> ```
>
> Knowing wallabag version to get which authentication methods can be supported can be easily done with a simple GET request to `api/version`.

cURL example:

```bash
curl -s "https://localhost:8000/oauth/v2/token?grant_type=client_credentials&client_id=1_3o53gl30vhgk0c8ks4cocww08o84448osgo40wgw4gwkoo8skc&client_secret=636ocbqo978ckw0gsw4gcwwocg8044sco0w8w84cws48ggogs4"
```

You'll have this in return:

```http
HTTP/1.1 200 OK
Cache-Control: no-store, private
Connection: close
Content-Type: application/json
Date: Tue, 05 Apr 2016 08:44:33 GMT
Host: localhost:8000
Pragma: no-cache
X-Debug-Token: 19c8e0
X-Debug-Token-Link: /_profiler/19c8e0
X-Powered-By: PHP/7.0.4

{
    "access_token": "ZGJmNTA2MDdmYTdmNWFiZjcxOWY3MWYyYzkyZDdlNWIzOTU4NWY3NTU1MDFjOTdhMTk2MGI3YjY1ZmI2NzM5MA",
    "expires_in": 3600,
    "scope": null,
    "token_type": "bearer"
}
```

We'll work with the `access_token` value in our next calls.
