API documentation
=================

Thanks to this documentation, we'll see how to interact with the wallabag API.

There are two types of API clients : the ones created by the users for their own use and the ones created by the server admin for all of it's users so that they can connect automatically and smoothly.

Requirements
------------

* wallabag freshly (or not) installed on http://localhost:8000
* ``httpie`` installed on your computer (`see project website <https://github.com/jkbrzt/httpie>`__). Note that you can also adapt the commands using curl or wget.
* all the API methods are documented here http://localhost:8000/api/doc (on your instance) and `on our example instance <http://v2.wallabag.org/api/doc>`_
* Knowledge of oAuth 2 flows

Register a new app
------------------

To register a new app, you just need to do a POST request at `/api/apps` with the following parameters :

* `client_name`: **REQUIRED** the client name, typically your app name, that will be shown to the user
* `redirect_uri`: **REQUIRED** all the redirect URIs used through the auth process
* `logo_uri`: An URL for your logo that will be displayed on the allow page
* `description`: A short description for your app that will be displayed at the user
* `app_uri`: An URL for your app, it's homepage or help section...

It returns (among other things) a `client_id` and `client_secret` that will be used afterwards.

From here the process differs if you're using a server-side app or a client-side app. The `client_secret` is indeed *secret* and shouldn't be accessible on the device of an user. As consequence, `refresh_token`s are not available for client-side apps, which will need the user to login again after the `access_token` has expired.

Server app
----------
Applications first call `https://wallabag.tld/oauth/v2/auth` with the following parameters :

* `client_id`: the one returned at the app creation
* `redirect_uri`: the uri (schemes allowed !) to redirect after authorization is made, read below)
* `response_type`: `code`
* `state`: A random string (for instance base64 encoded) that will be returned by the server to check against XSRF
* `scope`: The rights your app requires. There is read, write and user (ref #3065)


Then, wallabag promps a page (after login if needed) asking the user to allow the app and then redirects to the redirect_uri with a code as parameter.
The app now calls `https://wallabag.tld/oauth/v2/auth` with the following parameters :

* `code`: the code you just got
* `client_id`: the client id, same as before
* `redirect_uri`: the final redirect uri
* `grant_type`: `authorization_code`

The redirect_uri is finally called with the access_token (and refresh_token and others like before...) as parameter.

Client app
----------
Applications first call `https://wallabag.tld/oauth/v2/auth` with the following parameters :

* `client_id`: the one the instance administrator gave
* `redirect_uri`: the uri (schemes allowed !) to redirect after authorization is made, read below)
* `response_type`: `token`
* `state`: A random string (for instance base64 encoded) that will be returned by the server to check against XSRF
* `scope`: There is read, write and user (ref #3065)

The redirect_uri defined is called with the access_token as parameter. Clients apps can't get a refresh token, they need to authenticate the user again when the access_token has expired.


Refresh your access token
-------------------------

You'll need the `refresh_token` that you'll created earlier, and the `client_id` and `client_secret` provided when the app was created.

::

    http POST http://127.0.0.1:8000/oauth/v2/token \
    grant_type=refresh_token \
    refresh_token=N2M2YTg2NDE3ZTQyNTZmMzQ0NmZkZmI3OTRlNjg5N2JiOTljMTkyMzFkZWYwZjMxNTJmNGFhYjA3MjFhMGEzYQ \
    client_id=1_60a2o9salk8480wkgggoks0k04ko0ck8csw8gw0woc80sckwgw \
    client_secret=1bnyka2tk90k84ss8sgws4ok8ss04gokc4sc8oscs4gc84go44

Getting existing entries
------------------------

Documentation for this method: http://localhost:8000/api/doc#get--api-entries.{_format}

As we work on a fresh wallabag installation, we'll have no result with this command:

::

    http GET http://localhost:8000/api/entries.json \
    "Authorization:Bearer ZGJmNTA2MDdmYTdmNWFiZjcxOWY3MWYyYzkyZDdlNWIzOTU4NWY3NTU1MDFjOTdhMTk2MGI3YjY1ZmI2NzM5MA"

returns:

::

    HTTP/1.1 200 OK
    0: application/json
    Cache-Control: no-cache
    Connection: close
    Content-Type: application/json
    Date: Tue, 05 Apr 2016 08:51:32 GMT
    Host: localhost:8000
    Set-Cookie: PHPSESSID=nrogm748md610ovhu6j70c3q63; path=/; HttpOnly
    X-Debug-Token: 4fbbc4
    X-Debug-Token-Link: /_profiler/4fbbc4
    X-Powered-By: PHP/7.0.4

    {
        "_embedded": {
            "items": []
        },
        "_links": {
            "first": {
                "href": "http://localhost:8000/api/entries?page=1&perPage=30"
            },
            "last": {
                "href": "http://localhost:8000/api/entries?page=1&perPage=30"
            },
            "self": {
                "href": "http://localhost:8000/api/entries?page=1&perPage=30"
            }
        },
        "limit": 30,
        "page": 1,
        "pages": 1,
        "total": 0
    }

The ``items`` array is empty.

cURL example:

::

    curl --get "https://localhost:8000/api/entries.html?access_token=ZGJmNTA2MDdmYTdmNWFiZjcxOWY3MWYyYzkyZDdlNWIzOTU4NWY3NTU1MDFjOTdhMTk2MGI3YjY1ZmI2NzM5MA"

Adding your first entry
-----------------------

Documentation for this method: http://localhost:8000/api/doc#post--api-entries.{_format}

::

    http POST http://localhost:8000/api/entries.json \
    "Authorization:Bearer ZGJmNTA2MDdmYTdmNWFiZjcxOWY3MWYyYzkyZDdlNWIzOTU4NWY3NTU1MDFjOTdhMTk2MGI3YjY1ZmI2NzM5MA" \
    url="http://www.numerama.com/tech/160115-le-pocket-libre-wallabag-fait-le-plein-de-fonctionnalites.html"

returns

::

    HTTP/1.1 200 OK
    0: application/json
    Cache-Control: no-cache
    Connection: close
    Content-Type: application/json
    Date: Tue, 05 Apr 2016 09:07:54 GMT
    Host: localhost:8000
    Set-Cookie: PHPSESSID=bjie40ck72kp2pst3i71gf43a4; path=/; HttpOnly
    X-Debug-Token: e01c51
    X-Debug-Token-Link: /_profiler/e01c51
    X-Powered-By: PHP/7.0.4

    {
        "_links": {
            "self": {
                "href": "/api/entries/1"
            }
        },
        "content": "<p class=\"chapo\">Fonctionnant sur le même principe que Pocket, Instapaper ou Readability, le logiciel Wallabag permet de mémoriser des articles pour les lire plus tard. Sa nouvelle version apporte une multitude de nouvelles fonctionnalités.</p><p>Si vous utilisez Firefox comme navigateur web, vous avez peut-être constaté l’arrivée d’<a href=\"http://www.numerama.com/magazine/33292-update-firefox.html\">une fonctionnalité intitulée Pocket</a>. Disponible autrefois sous la forme d’un module complémentaire, et sous un autre nom (Read it Later), elle est depuis le mois de juin 2015 directement incluse au sein de Firefox.</p>\n<p>Concrètement, Pocket sert à garder en mémoire des contenus que vous croisez au fil de la navigation, comme des articles de presse ou des vidéos, afin de pouvoir les consulter plus tard. Pocket fonctionne un peu comme un système de favoris, mais en bien plus élaboré grâce à ses options supplémentaires.</p>\n<p>Mais <a href=\"https://en.wikipedia.org/wiki/Pocket_%28application%29#Firefox_integration\" target=\"_blank\">Pocket fait polémique</a>, car il s’agit d’un projet propriétaire qui est intégré dans un logiciel libre. C’est pour cette raison que des utilisateurs ont choisi de se tourner vers d’autres solutions, comme <strong>Wallabag</strong>, qui est l’équivalent libre de Pocket et d’autres systèmes du même genre, comme Instapaper et Readability.</p>\n<p>Et justement, Wallabag évolue. C’est ce dimanche que la <a href=\"https://www.wallabag.org/blog/2016/04/03/wallabag-v2\" target=\"_blank\">version 2.0.0 du logiciel</a> a été publiée par l’équipe en  charge de son développement et celle-ci contient de nombreux changements par rapport aux moutures précédentes (la <a href=\"http://doc.wallabag.org/fr/v2/\" target=\"_blank\">documentation est traduite</a> en français), lui permettant d’apparaître comme une alternative à Pocket, Instapaper et Readability.</p>\n<p><img class=\"aligncenter size-medium wp-image-160439\" src=\"http://www.numerama.com/content/uploads/2016/04/homepage-680x347.png\" alt=\"homepage\" width=\"680\" height=\"347\" srcset=\"//www.numerama.com/content/uploads/2016/04/homepage-680x347.png 680w, //www.numerama.com/content/uploads/2016/04/homepage-1024x523.png 1024w, //www.numerama.com/content/uploads/2016/04/homepage-270x138.png 270w, //www.numerama.com/content/uploads/2016/04/homepage.png 1286w\" sizes=\"(max-width: 680px) 100vw, 680px\"/></p>\n<p>Parmi les principaux changements que l’on peut retenir avec cette nouvelle version, notons la possibilité d’écrire des annotations dans les articles mémorisés, de filtrer les contenus selon divers critères (temps de lecture, nom de domaine, date de création, statut…), d’assigner des mots-clés aux entrées, de modifier le titre des articles, le support des flux RSS ou encore le support de plusieurs langues dont le français.</p>\n<p>D’autres options sont également à signaler, comme l’aperçu d’un article mémorisé (si l’option est disponible), un guide de démarrage rapide pour les débutants, un outil d’export dans divers formats (PDF, JSON, EPUB, MOBI, XML, CSV et TXT) et, surtout, la possibilité de migrer vers Wallabag depuis Pocket, afin de convaincre les usagers de se lancer.</p>\n    \n    \n    <footer class=\"clearfix\" readability=\"1\"><p class=\"source\">\n        Crédit photo de la une : <a href=\"https://www.flickr.com/photos/bookgrl/2388310523/\">Laura Taylor</a>\n    </p>\n    \n    <p><a href=\"http://www.numerama.com/tech/160115-le-pocket-libre-wallabag-fait-le-plein-de-fonctionnalites.html?&amp;show_reader_reports\" target=\"_blank\" rel=\"nofollow\">Signaler une erreur dans le texte</a></p>\n        \n</footer>    <section class=\"related-article\"><header><h3>Articles liés</h3>\n    </header><article class=\"post-grid format-article\"><a class=\"floatleft\" href=\"http://www.numerama.com/magazine/34444-firefox-prepare-l-enterrement-des-vieux-plugins.html\" title=\"Firefox prépare l'enterrement des vieux plugins\">\n        <div class=\"cover-preview cover-tech\">\n                            <p>Lire</p>\n            \n                            \n            \n            <img class=\"cover-preview_img\" src=\"http://c2.lestechnophiles.com/www.numerama.com/content/uploads/2015/10/cimetierecolleville.jpg?resize=200,135\" srcset=\"&#10;                    //c2.lestechnophiles.com/www.numerama.com/content/uploads/2015/10/cimetierecolleville.jpg?resize=200,135 200w,&#10;                                            //c2.lestechnophiles.com/www.numerama.com/content/uploads/2015/10/cimetierecolleville.jpg?resize=100,67 100w,&#10;                                        \" sizes=\"(min-width: 1001px) 200px, (max-width: 1000px) 100px\" alt=\"Firefox prépare l'enterrement des vieux plugins\"/></div>\n        <h4> Firefox prépare l'enterrement des vieux plugins </h4>\n    </a>\n    <footer class=\"span12\">\n    </footer></article><article class=\"post-grid format-article\"><a class=\"floatleft\" href=\"http://www.numerama.com/tech/131636-activer-navigation-privee-navigateur-web.html\" title=\"Comment activer la navigation privée sur son navigateur web\">\n        <div class=\"cover-preview cover-tech\">\n                            <p>Lire</p>\n            \n                            \n            \n            <img class=\"cover-preview_img\" src=\"http://c1.lestechnophiles.com/www.numerama.com/content/uploads/2015/11/Incognito.jpg?resize=200,135\" srcset=\"&#10;                    //c1.lestechnophiles.com/www.numerama.com/content/uploads/2015/11/Incognito.jpg?resize=200,135 200w,&#10;                                            //c1.lestechnophiles.com/www.numerama.com/content/uploads/2015/11/Incognito.jpg?resize=100,67 100w,&#10;                                        \" sizes=\"(min-width: 1001px) 200px, (max-width: 1000px) 100px\" alt=\"Comment activer la navigation privée sur son navigateur web\"/></div>\n        <h4> Comment activer la navigation privée sur son navigateur web </h4>\n    </a>\n    <footer class=\"span12\">\n    </footer></article><article class=\"post-grid format-article\"><a class=\"floatleft\" href=\"http://www.numerama.com/tech/144028-firefox-se-mettra-a-jour-regulierement.html\" title=\"Firefox se mettra à jour un peu moins régulièrement\">\n        <div class=\"cover-preview cover-tech\">\n                            <p>Lire</p>\n            \n                            \n            \n            <img class=\"cover-preview_img\" src=\"http://c0.lestechnophiles.com/www.numerama.com/content/uploads/2016/02/firefox-mobile.jpg?resize=200,135\" srcset=\"&#10;                    //c0.lestechnophiles.com/www.numerama.com/content/uploads/2016/02/firefox-mobile.jpg?resize=200,135 200w,&#10;                                            //c0.lestechnophiles.com/www.numerama.com/content/uploads/2016/02/firefox-mobile.jpg?resize=100,67 100w,&#10;                                        \" sizes=\"(min-width: 1001px) 200px, (max-width: 1000px) 100px\" alt=\"Firefox se mettra à jour un peu moins régulièrement\"/></div>\n        <h4> Firefox se mettra à jour un peu moins régulièrement </h4>\n    </a>\n    <footer class=\"span12\">\n    </footer></article>\n</section>\n",
        "created_at": "2016-04-05T09:07:54+0000",
        "domain_name": "www.numerama.com",
        "id": 1,
        "is_archived": 0,
        "is_starred": 0,
        "language": "fr-FR",
        "mimetype": "text/html",
        "preview_picture": "http://www.numerama.com/content/uploads/2016/04/post-it.jpg",
        "reading_time": 2,
        "tags": [],
        "title": "Le Pocket libre Wallabag fait le plein de fonctionnalités - Tech - Numerama",
        "updated_at": "2016-04-05T09:07:54+0000",
        "url": "http://www.numerama.com/tech/160115-le-pocket-libre-wallabag-fait-le-plein-de-fonctionnalites.html",
        "user_email": "",
        "user_id": 1,
        "user_name": "wallabag"
    }

Now, if you execute the previous command (see **Get existing entries**), you'll have data.

cURL example:

::

    curl "https://localhost:8000/api/entries.html?access_token=ZGJmNTA2MDdmYTdmNWFiZjcxOWY3MWYyYzkyZDdlNWIzOTU4NWY3NTU1MDFjOTdhMTk2MGI3YjY1ZmI2NzM5MA&url=http://www.numerama.com/tech/160115-le-pocket-libre-wallabag-fait-le-plein-de-fonctionnalites.html"

Deleting an entry
-----------------

Documentation for this method: http://localhost:8000/api/doc#delete--api-entries-{entry}.{_format}

::

    http DELETE http://localhost:8000/api/entries/1.json \
    "Authorization:Bearer ZGJmNTA2MDdmYTdmNWFiZjcxOWY3MWYyYzkyZDdlNWIzOTU4NWY3NTU1MDFjOTdhMTk2MGI3YjY1ZmI2NzM5MA"

returns

::

    HTTP/1.1 200 OK
    0: application/json
    Cache-Control: no-cache
    Connection: close
    Content-Type: application/json
    Date: Tue, 05 Apr 2016 09:19:07 GMT
    Host: localhost:8000
    Set-Cookie: PHPSESSID=jopgnfvmuc9a62b27sqm6iulr6; path=/; HttpOnly
    X-Debug-Token: 887cef
    X-Debug-Token-Link: /_profiler/887cef
    X-Powered-By: PHP/7.0.4

    {
        "_links": {
            "self": {
                "href": "/api/entries/"
            }
        },
        "annotations": [],
        "content": "<p class=\"chapo\">Fonctionnant sur le même principe que Pocket, Instapaper ou Readability, le logiciel Wallabag permet de mémoriser des articles pour les lire plus tard. Sa nouvelle version apporte une multitude de nouvelles fonctionnalités.</p><p>Si vous utilisez Firefox comme navigateur web, vous avez peut-être constaté l’arrivée d’<a href=\"http://www.numerama.com/magazine/33292-update-firefox.html\">une fonctionnalité intitulée Pocket</a>. Disponible autrefois sous la forme d’un module complémentaire, et sous un autre nom (Read it Later), elle est depuis le mois de juin 2015 directement incluse au sein de Firefox.</p>\n<p>Concrètement, Pocket sert à garder en mémoire des contenus que vous croisez au fil de la navigation, comme des articles de presse ou des vidéos, afin de pouvoir les consulter plus tard. Pocket fonctionne un peu comme un système de favoris, mais en bien plus élaboré grâce à ses options supplémentaires.</p>\n<p>Mais <a href=\"https://en.wikipedia.org/wiki/Pocket_%28application%29#Firefox_integration\" target=\"_blank\">Pocket fait polémique</a>, car il s’agit d’un projet propriétaire qui est intégré dans un logiciel libre. C’est pour cette raison que des utilisateurs ont choisi de se tourner vers d’autres solutions, comme <strong>Wallabag</strong>, qui est l’équivalent libre de Pocket et d’autres systèmes du même genre, comme Instapaper et Readability.</p>\n<p>Et justement, Wallabag évolue. C’est ce dimanche que la <a href=\"https://www.wallabag.org/blog/2016/04/03/wallabag-v2\" target=\"_blank\">version 2.0.0 du logiciel</a> a été publiée par l’équipe en  charge de son développement et celle-ci contient de nombreux changements par rapport aux moutures précédentes (la <a href=\"http://doc.wallabag.org/fr/v2/\" target=\"_blank\">documentation est traduite</a> en français), lui permettant d’apparaître comme une alternative à Pocket, Instapaper et Readability.</p>\n<p><img class=\"aligncenter size-medium wp-image-160439\" src=\"http://www.numerama.com/content/uploads/2016/04/homepage-680x347.png\" alt=\"homepage\" width=\"680\" height=\"347\" srcset=\"//www.numerama.com/content/uploads/2016/04/homepage-680x347.png 680w, //www.numerama.com/content/uploads/2016/04/homepage-1024x523.png 1024w, //www.numerama.com/content/uploads/2016/04/homepage-270x138.png 270w, //www.numerama.com/content/uploads/2016/04/homepage.png 1286w\" sizes=\"(max-width: 680px) 100vw, 680px\"/></p>\n<p>Parmi les principaux changements que l’on peut retenir avec cette nouvelle version, notons la possibilité d’écrire des annotations dans les articles mémorisés, de filtrer les contenus selon divers critères (temps de lecture, nom de domaine, date de création, statut…), d’assigner des mots-clés aux entrées, de modifier le titre des articles, le support des flux RSS ou encore le support de plusieurs langues dont le français.</p>\n<p>D’autres options sont également à signaler, comme l’aperçu d’un article mémorisé (si l’option est disponible), un guide de démarrage rapide pour les débutants, un outil d’export dans divers formats (PDF, JSON, EPUB, MOBI, XML, CSV et TXT) et, surtout, la possibilité de migrer vers Wallabag depuis Pocket, afin de convaincre les usagers de se lancer.</p>\n    \n    \n    <footer class=\"clearfix\" readability=\"1\"><p class=\"source\">\n        Crédit photo de la une : <a href=\"https://www.flickr.com/photos/bookgrl/2388310523/\">Laura Taylor</a>\n    </p>\n    \n    <p><a href=\"http://www.numerama.com/tech/160115-le-pocket-libre-wallabag-fait-le-plein-de-fonctionnalites.html?&amp;show_reader_reports\" target=\"_blank\" rel=\"nofollow\">Signaler une erreur dans le texte</a></p>\n        \n</footer>    <section class=\"related-article\"><header><h3>Articles liés</h3>\n    </header><article class=\"post-grid format-article\"><a class=\"floatleft\" href=\"http://www.numerama.com/magazine/34444-firefox-prepare-l-enterrement-des-vieux-plugins.html\" title=\"Firefox prépare l'enterrement des vieux plugins\">\n        <div class=\"cover-preview cover-tech\">\n                            <p>Lire</p>\n            \n                            \n            \n            <img class=\"cover-preview_img\" src=\"http://c2.lestechnophiles.com/www.numerama.com/content/uploads/2015/10/cimetierecolleville.jpg?resize=200,135\" srcset=\"&#10;                    //c2.lestechnophiles.com/www.numerama.com/content/uploads/2015/10/cimetierecolleville.jpg?resize=200,135 200w,&#10;                                            //c2.lestechnophiles.com/www.numerama.com/content/uploads/2015/10/cimetierecolleville.jpg?resize=100,67 100w,&#10;                                        \" sizes=\"(min-width: 1001px) 200px, (max-width: 1000px) 100px\" alt=\"Firefox prépare l'enterrement des vieux plugins\"/></div>\n        <h4> Firefox prépare l'enterrement des vieux plugins </h4>\n    </a>\n    <footer class=\"span12\">\n    </footer></article><article class=\"post-grid format-article\"><a class=\"floatleft\" href=\"http://www.numerama.com/tech/131636-activer-navigation-privee-navigateur-web.html\" title=\"Comment activer la navigation privée sur son navigateur web\">\n        <div class=\"cover-preview cover-tech\">\n                            <p>Lire</p>\n            \n                            \n            \n            <img class=\"cover-preview_img\" src=\"http://c1.lestechnophiles.com/www.numerama.com/content/uploads/2015/11/Incognito.jpg?resize=200,135\" srcset=\"&#10;                    //c1.lestechnophiles.com/www.numerama.com/content/uploads/2015/11/Incognito.jpg?resize=200,135 200w,&#10;                                            //c1.lestechnophiles.com/www.numerama.com/content/uploads/2015/11/Incognito.jpg?resize=100,67 100w,&#10;                                        \" sizes=\"(min-width: 1001px) 200px, (max-width: 1000px) 100px\" alt=\"Comment activer la navigation privée sur son navigateur web\"/></div>\n        <h4> Comment activer la navigation privée sur son navigateur web </h4>\n    </a>\n    <footer class=\"span12\">\n    </footer></article><article class=\"post-grid format-article\"><a class=\"floatleft\" href=\"http://www.numerama.com/tech/144028-firefox-se-mettra-a-jour-regulierement.html\" title=\"Firefox se mettra à jour un peu moins régulièrement\">\n        <div class=\"cover-preview cover-tech\">\n                            <p>Lire</p>\n            \n                            \n            \n            <img class=\"cover-preview_img\" src=\"http://c0.lestechnophiles.com/www.numerama.com/content/uploads/2016/02/firefox-mobile.jpg?resize=200,135\" srcset=\"&#10;                    //c0.lestechnophiles.com/www.numerama.com/content/uploads/2016/02/firefox-mobile.jpg?resize=200,135 200w,&#10;                                            //c0.lestechnophiles.com/www.numerama.com/content/uploads/2016/02/firefox-mobile.jpg?resize=100,67 100w,&#10;                                        \" sizes=\"(min-width: 1001px) 200px, (max-width: 1000px) 100px\" alt=\"Firefox se mettra à jour un peu moins régulièrement\"/></div>\n        <h4> Firefox se mettra à jour un peu moins régulièrement </h4>\n    </a>\n    <footer class=\"span12\">\n    </footer></article>\n</section>\n",
        "created_at": "2016-04-05T09:07:54+0000",
        "domain_name": "www.numerama.com",
        "is_archived": 0,
        "is_starred": 0,
        "language": "fr-FR",
        "mimetype": "text/html",
        "preview_picture": "http://www.numerama.com/content/uploads/2016/04/post-it.jpg",
        "reading_time": 2,
        "tags": [],
        "title": "Le Pocket libre Wallabag fait le plein de fonctionnalités - Tech - Numerama",
        "updated_at": "2016-04-05T09:07:54+0000",
        "url": "http://www.numerama.com/tech/160115-le-pocket-libre-wallabag-fait-le-plein-de-fonctionnalites.html",
        "user_email": "",
        "user_id": 1,
        "user_name": "wallabag"
    }

And if you want to list the existing entries (see **Get existing entries**), the array is empty.

cURL example:

::

    curl --request DELETE "https://localhost:8000/api/entries/1.html?access_token=ZGJmNTA2MDdmYTdmNWFiZjcxOWY3MWYyYzkyZDdlNWIzOTU4NWY3NTU1MDFjOTdhMTk2MGI3YjY1ZmI2NzM5MA"


Other methods
-------------

We won't write samples for each API method.

Have a look on the listing here: http://localhost:8000/api/doc to know each method.

Creating a new API client (depreciated)
---------------------------------------

In your wallabag account, you can create a new API client by going to the *API clients management* section in the sidebar.

You just need give a name to name your client, then confirm it's creation.

You get information like this:

::

    Client ID:

    1_3o53gl30vhgk0c8ks4cocww08o84448osgo40wgw4gwkoo8skc

    Client secret:

    636ocbqo978ckw0gsw4gcwwocg8044sco0w8w84cws48ggogs4

These can be used like this :

Using clients (depreciated)
---------------------------

For each API call, you'll need a token. Let's create it with this command (replace ``client_id`` and``client_secret`` with their values):

::

    http POST http://localhost:8000/oauth/v2/token \
        grant_type=client_credentials \
        client_id=1_3o53gl30vhgk0c8ks4cocww08o84448osgo40wgw4gwkoo8skc \
        client_secret=636ocbqo978ckw0gsw4gcwwocg8044sco0w8w84cws48ggogs4 \

You'll have this in return:

::

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
        "expires_in": 5184000,
        "refresh_token": "OTNlZGE5OTJjNWQwYzc2NDI5ZGE5MDg3ZTNjNmNkYTY0ZWZhZDVhNDBkZTc1ZTNiMmQ0MjQ0OThlNTFjNTQyMQ",
        "scope": null,
        "token_type": "bearer"
    }

We'll work with the ``access_token`` value in our next calls.

cURL example:

::

    curl -s "https://localhost:8000/oauth/v2/token?grant_type=client_credentials&client_id=1_3o53gl30vhgk0c8ks4cocww08o84448osgo40wgw4gwkoo8skc&client_secret=636ocbqo978ckw0gsw4gcwwocg8044sco0w8w84cws48ggogs4"



Third party resources
---------------------

Some applications or libraries use our API. Here is a non-exhaustive list of them:

- `Java wrapper for the wallabag API <https://github.com/di72nn/wallabag-api-wrapper>`_ by Dmitriy Bogdanov.
- `.NET library for the wallabag v2 API <https://github.com/jlnostr/wallabag-api>`_ by Julian Oster.
- `Python API for wallabag <https://github.com/foxmask/wallabag_api>`_ by FoxMaSk, for his project `Trigger Happy <https://blog.trigger-happy.eu/>`_.
- `A plugin <https://github.com/joshp23/ttrss-to-wallabag-v2>`_ designed for `Tiny Tiny RSS <https://tt-rss.org/gitlab/fox/tt-rss/wikis/home>`_ that makes use of the wallabag v2 API. By Josh Panter.
- `Golang wrapper for the wallabag API <https://github.com/Strubbl/wallabago>`_ by Strubbl, for his projects `wallabag-stats graph <https://github.com/Strubbl/wallabag-stats>`_ and the command line tool `wallabag-add-article <https://github.com/Strubbl/wallabag-add-article>`_.
- Tool to automatically download Wallabag articles into your local computer or Kobo ebook reader `wallabako <https://gitlab.com/anarcat/wallabako>`_ by anarcat.
